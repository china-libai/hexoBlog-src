---
title: Nmap cheat sheet
date: 2017-05-07 18:47:45
tags:
---
http://www.91ri.org/8654.html
## Nmap 介绍
![功能架构图](http://my.csdn.net/uploads/201206/26/1340719324_9785.JPG)

### 四项基本功能

- 主机发现（Host Discovery）

- 端口扫描（Port Scanning）

- 版本侦测（Version Detection）

- 操作系统侦测（Operating System Detection）

    而这四项功能之间，又存在大致的依赖关系（通常情况下的顺序关系，但特殊应用另外考虑），首先需要进行主机发现，随后确定端口状况，然后确定端口上运行具体应用程序与版本信息，然后可以进行操作系统的侦测。而在四项基本功能的基础上，Nmap提供防火墙与IDS（IntrusionDetection System,入侵检测系统）的规避技巧，可以综合应用到四个基本功能的各个阶段；另外Nmap提供强大的NSE（Nmap Scripting Language）脚本引擎功能，脚本可以对基本功能进行补充和扩展。

## 基本使用
### 主机发现


```bash
nmap oddboy.cn 192.168.0,1,4-7.2-255 10.1.1.0/24    # 多主机地址扫描
nmap -A -T4 host        
# -A 对主机进行完整全面的扫描(主机发现、端口扫描、应用程序与版本侦测、操作系统侦测及调用默认NSE脚本扫描)
# -T4 指定扫描时序，总共6个级别(0-5),级别越高，速度越快，但容易被防火墙检测屏蔽。

```




## 常规使用

```bash
nmap IP/Hostname                    # 最基本的使用情况
nmap host1 host2 host3 etc...       # 扫描多主机
nmap 192.168.0.1-192.168.1.254      # 扫描IP地址段
nmap 192.168.0.1/23                 # CIDR格式的网络地址段（与上一条命令等同）
nmap -iL list.txt                   # 扫描目标主机列表
nmap -Pn host                       # 假定目标存活



```
## 高级使用
```bash
nmap -A host                        # ？？？？？？？


```
---
---
---

## 渗透测试中不常用(我反正没用过)
```bash
nmap 192.168.1.0/24 --exclude 192.168.1.11  # 排除一些主机
nmap -iR 100                        # 随机扫描互联网上100个主机
nmap -Sp                            # 只ping扫描，
nmap -PS                            # TCP SYN scan (TCP SYN ping)
nmap -PA                            # TCP ACK scan ping
nmap -PU                            # UDP scan  ping
nmap -PY                            # ??? SCTP scan ping
nmap -PE/PP/PM                      # ??? ICMP echo/timestamp/netmask request discovery probes  什么鬼？
nmap -PO [protocol] host            # 指定协议ping
nmap -PR                            # ??? ARP ping 
nmap --traceroute                   # traceroute 功能 探测网络路径
nmap -n/-R                          # Never do DNS resolution/Always resolve [default: sometimes]
nmap --dns-servers serv1,serv2,,,   # 指定DNS
nmap --system-dns                   # 使用系统DNS
nmap -sL IPs                        # 列出IPs的反向DNS结果
nmap 



```
## 参考资料
[Introduction to Nmap](http://resources.infosecinstitute.com/nmap-cheat-sheet/)

[Nmap从探测到漏洞利用备忘录 – Nmap简介(一)](http://www.freebuf.com/articles/network/32302.html)

## #零散知识

#### Nmap脚本引擎(NSE)
    - 网络探测
    - 漏洞检测
    - 漏洞利用

#### 端口状态说明
- Open(开放的): 应用程序正在这个端口上监听连接。

- Closed(关闭的): 端口对探测做出了响应，但是现在没有应用程序在监听这个端口。

- Filtered(过滤的): 端口没有对探测做出响应。同时告诉我们探针可能被一些过滤器（防火墙）终止了。

- Unfiltered(未被过滤的):端口对探测做出了响应，但是Nmap无法确定它们是关闭还是开放。

- Open/Filtered: 端口被过滤或者是开放的，Nmap无法做出判断。

- Closed/Filtered: 端口被过滤或者是关闭的，Nmap无法做出判断。
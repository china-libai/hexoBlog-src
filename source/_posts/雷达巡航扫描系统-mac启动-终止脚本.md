---
title: 雷达巡航扫描系统--mac启动/终止脚本
date: 2017-08-03 10:56:13
tags:
---
主要涉及到使用[osascript](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/osascript.1.html)提权功能。

另外,打算使用 trap "bash kill.sh;exit" EXIT INT TERM QUIT KILL来自动结束脚本所启动的后台服务，然后将启动脚本写入automantor中。

但最后并没有使用，因为有些问题不太清楚，比如：在automator中结束时到底具体捕获哪一个信号？

暂且这样使用，后续有需求，再处理。

trap捕获信号 [http://man.linuxde.net/trap](http://man.linuxde.net/trap)
PS: bash下可用，zsh下疑似有问题。
<!-- more -->
### 启动脚本
```bash
#!/bin/sh
cd /Users/jason/geekTools/radar
if [ $? -eq 0 ]
then
    /usr/local/bin/mongod --port 65521 --dbpath DBData --auth  >> log/db.log  2>&1 &
    /usr/local/bin/python Run.py >> log/web.log 2>&1 &
	osascript -e 'do shell script "sudo -s /usr/local/bin/python aider/Aider.py >> log/aider.log 2>&1 &" with administrator privileges'
    /usr/local/bin/python nascan/NAScan.py >> log/nascan.log 2>&1 &
    /usr/local/bin/python vulscan/VulScan.py >> log/vulscan.log 2>&1 &
    echo "Radar Started";
    say "Radar Started";
    #trap "bash kill.sh;exit" EXIT INT TERM QUIT KILL
    #bash kill.sh;
    # while true; do 
    #     sleep 86400;
    # done
fi
```
### 终止脚本
```bash
#!/bin/sh
ps aux | grep 'mongod --port 65521 --dbpath DBData --auth' | grep -v 'grep'| awk -F ' ' '{print $2}' | xargs kill;
ps aux | grep 'Run.py' | grep -v 'grep' | awk '{print $2}' | xargs kill;
#osascript -e 'do shell script "ps aux | grep \"aider/Aider.py\" | grep -v \"grep\" | awk \"{print $2}\" | xargs sudo -s kill" with administrator privileges'
killAider="ps aux | grep 'aider/Aider.py' | grep -v 'grep' | awk '{print \$2}' | xargs sudo -s kill";
killAider='do shell script "'$killAider'" with administrator privileges';
osascript -e "$killAider";
#trap "osascript -e \"$killAider\"" EXIT INT TERM QUIT KILL;
ps aux | grep 'nascan/NAScan.py' | grep -v 'grep' | awk '{print $2}' | xargs kill;
ps aux | grep 'vulscan/VulScan.py' | grep -v 'grep' | awk '{print $2}' | xargs kill;
echo "Radar Terminated";
say "Radar Terminated";
exit
```

### 问题记录
需要kill -9 xxx 才能结束某些进程。
```bash
➜  radar git:(master) ✗ ps aux | grep 'mongod --port 65521 --dbpath DBData --auth'
jason            17784   0.0  0.0  2452848   2004   ??  S    10:39AM   0:00.02 /bin/bash -c #!/bin/sh\012cd /Users/jason/geekTools/radar\012if [ $? -eq 0 ]\012then\012    /usr/local/bin/mongod --port 65521 --dbpath DBData --auth  >> log/db.log  2>&1 &\012    /usr/local/bin/python Run.py >> log/web.log 2>&1 &\012\011osascript -e 'do shell script "sudo -s /usr/local/bin/python aider/Aider.py >> log/aider.log 2>&1 &" with administrator privileges'\012    /usr/local/bin/python nascan/NAScan.py >> log/nascan.log 2>&1 &\012    /usr/local/bin/python vulscan/VulScan.py >> log/vulscan.log 2>&1 &\012    echo "Radar Started";\012    say "Radar Started";\012    trap "bash kill.sh;exit" EXIT INT TERM QUIT KILL\012    while true; do \012        sleep 86400;\012    done\012fi\012 -
jason            18686   0.0  0.0  2433828   1924 s001  R+   10:47AM   0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn mongod --port 65521 --dbpath DBData --auth
jason            18370   0.0  0.0  2435440   2240   ??  S    10:44AM   0:00.01 /bin/bash -c #!/bin/sh\012cd /Users/jason/geekTools/radar\012if [ $? -eq 0 ]\012then\012    /usr/local/bin/mongod --port 65521 --dbpath DBData --auth  >> log/db.log  2>&1 &\012    /usr/local/bin/python Run.py >> log/web.log 2>&1 &\012\011osascript -e 'do shell script "sudo -s /usr/local/bin/python aider/Aider.py >> log/aider.log 2>&1 &" with administrator privileges'\012    /usr/local/bin/python nascan/NAScan.py >> log/nascan.log 2>&1 &\012    /usr/local/bin/python vulscan/VulScan.py >> log/vulscan.log 2>&1 &\012    echo "Radar Started";\012    say "Radar Started";\012    trap "bash kill.sh;exit" EXIT INT TERM QUIT KILL\012    while true; do \012        sleep 86400;\012    done\012fi\012 -
```

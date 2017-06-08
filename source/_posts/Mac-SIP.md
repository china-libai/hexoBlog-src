---
title: MacOS - SIP (System Integrity Protection) 系统集成保护
date: 2017-04-26 19:19:03
tags: 
---
# Mac - SIP 系统集成保护
官方资料[About System Integrity Protection on your Mac](https://support.apple.com/en-us/HT204899)

## 开启/关闭SIP
参考来源 [http://www.jianshu.com/p/0572336a0771](http://www.jianshu.com/p/0572336a0771)
### 1.进入Recovery Mode
开机按住command+R

### 2.使用csrutil命令
<!-- more -->
打开终端Terminal，键入csrutil可以显示该命令的使用方法
```bash
➜  ~ csrutil
usage: csrutil <command>
Modify the System Integrity Protection configuration. All configuration changes apply to the entire machine.
Available commands:

    clear
        Clear the existing configuration. Only available in Recovery OS.
    disable
        Disable the protection on the machine. Only available in Recovery OS.
    enable
        Enable the protection on the machine. Only available in Recovery OS.
    status
        Display the current configuration.

    netboot
        add <address>
            Insert a new IPv4 address in the list of allowed NetBoot sources.
        list
            Print the list of allowed NetBoot sources.
        remove <address>
            Remove an IPv4 address from the list of allowed NetBoot sources.
```
正常系统模式下仅可以用status命令查询SIP状态

### 3.常用参数
clear：清除配置设置，等同于完全开启SIP(仅在恢复模式下有效)

disable：关闭SIP(仅在恢复模式下有效)

enable：开启SIP(仅在恢复模式下有效)

status：查询SIP状态

### 4.常用参数进阶

除了可以完全关闭/打开，还可以进行单项和多项组合关闭相关功能，用法如下
```bash
csrutil enable [--without kext|fs|debug|dtrace|nvram] [--no-internal]
# 单项使用：
sudo csrutil enable –without fs：Filesystem Protections disable
sudo csrutil enable –without kext：Kext Signing disable
sudo csrutil enable –without debug：Debugging Restrictions disable
sudo csrutil enable –without nvram：NVRAM Protections disable
sudo csrutil enable –without dtrace：DTrace Restrictions disable
# 组合使用：
sudo csrutil enable –without kext –without fs：Filesystem Protections and Kext Signing are disabled
```

## mac下使用proxychains-ng实现代理
由于mac下SIP的保护，不能使用proxychains，除非关闭SIP。
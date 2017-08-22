---
title: PHP多版本安装与切换(Ubuntu-16-04-LTS)
date: 2017-07-26 09:06:16
tags: PHP
---
>   打算使用phpBrew来进行PHP版本管理，然而phpBrew的使用需要在Ubuntu 16上默认安装的PHP版本为5，却无法直接apt-get install php5，所以打算基于php7尝试使用。
>   结果很悲剧，疑似pkg-config出问题了。 所以本文主要做一个记录，祭奠浪费掉的两整天时间。最后憋屈的使用PHPstudy了。 /(ㄒoㄒ)/~~
<!-- more -->
## PPA安装PHP
### - 安装PHP 5.6
```
$ sudo apt-get install software-properties-common   # 安装PPA工具
$ sudo add-apt-repository ppa:ondrej/php            # ondrej这个哥们维护在launchpad上的
$ sudo apt-get update
$ sudo apt-get install -y php5.6
```
### - 安装PHP 7.1
```
$ sudo apt-get install python-software-properties
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt-get update
$ sudo apt-get install -y php7.1                    
```

## PHP版本切换
### PHP 5.6 => PHP 7.1
```
Apache:-
$ sudo a2dismod php5.6
$ sudo a2enmod php7.1
$ sudo service apache2 restart

CLI:-
$ update-alternatives --set php /usr/bin/php7.1
```
### PHP 7.1 => PHP 5.6
```
Apache:-
$ sudo a2dismod php7.1
$ sudo a2enmod php5.6
$ sudo service apache2 restart

CLI:-
$ sudo update-alternatives --set php /usr/bin/php5.6
```
## 使用phpBrew安装/切换PHP版本【失败】
[https://github.com/phpbrew/phpbrew](https://github.com/phpbrew/phpbrew)

```
$ sudo apt-get update
$ sudo apt-get install  php7.0 php7.0-cli php7.0-dev php7.0-curl php7.0-json php7.0-cgi php-pear autoconf automake curl build-essential openssl libssl-dev libcurl4-openssl-dev libxslt1-dev re2c libxml2 libxml2-dev bison libbz2-dev libreadline-dev libmhash2 libmhash-dev libmcrypt4 libmcrypt-dev

# openssl成功安装的
$ pkg-config --list-all | grep openssl
openssl              OpenSSL - Secure Sockets Layer and cryptography libraries and tools

$ phpbrew install 5.5.38    # 出错！！！！
    checking for pkg-config... /usr/bin/pkg-config
    configure: error: Cannot find OpenSSL's libraries

----------------------

$ phpbrew install 5.5.38 -- --with-openssl=/usr/bin/openssl
    checking for pkg-config... /usr/bin/pkg-config
    configure: error: Cannot find OpenSSL's <evp.h>

$ phpbrew install 5.5.38 -- --with-openssl=/usr/bin/openssl --with-openssl-dir=/usr/include/openssl/
    checking for pkg-config... /usr/bin/pkg-config
    configure: error: Cannot find OpenSSL's <evp.h>
```

>   各种无言以对，花了两整天时间，无法解决！
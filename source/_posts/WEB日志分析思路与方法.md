---
title: WEB日志分析思路与方法
date: 2017-11-08 09:44:04
tags: 日志分析
---

本文很杂乱，主要是为了简单记录一下。网上有一篇好文：[Web日志安全分析浅谈](http://bobao.360.cn/learning/detail/4009.html)

------

从WEB日志数据中，我们也许可以挖掘分析出很多好玩的东西，但在有百度统计之类的产品后，这样的功能再通过日志来实现是很鸡肋的。 然而，站在安全层面上来看，分析WEB日志，可以判断黑客的攻击是否成功？ 攻击路径？ 突破方式？ 损失情况鉴定等。

作为一个安全从业者，也手工分析过一些web日志。在分析的方式/手段/姿势上还需要总结提炼，故进行一下学习记录。

<!--more-->

## 常规办法

人工"手撕"日志，效率和效果也许都并不好，但好歹也算是一种方法吧。这种方法处理十来个GB的日志(非压缩)还是勉强能应付的。
比较传统geek的办法是使用 awk / grep / sort / join 等Unix/Linux工具。这样的方式我不推荐，一来我自己用得不是很溜，二来现在各种文本编辑器的功能非常强大，比如本人目前使用的Visual Studio Code。其它的sublime Text, Notepad++（win）等

0. 直接将数据载入文本编辑器。
1. 搜索"base64_decode","evil","python-urllib","system","whoami","ipconfig","select","script"等关键字，定位疑似攻击的日志项。
2. 找出相应的IP地址或(和)user-agent(有些服务器架构问题，导致无法记录原始IP地址,如负载均衡)，将所有的这些日志找出来。
3. 参考时间线，查看URL信息,服务器响应代码，逐一判断是否攻击成功。比如：是否有成功的webshell调用，SQL注入等等。

## 进阶办法

1. ELK (Elasticsearch + Logstash + Kinaba)

    Elasticsearch 开源分布式搜索引擎

    Logstash 对日志进行收集、过滤并存储到Elasticsearch或其他数据库

    Kibana 对日志分析友好的Web界面,可对Elasticsearch中的数据进行汇总、分析、查询

    [ELK (Elasticsearch + Logstash + Kinaba)](https://www.elastic.co/)

2. 基于WAF/正则规则

    - 将日志数据导入数据库
    - 建立攻击规则表(可考虑从modsecurity等WAF中提取)
    - 写SQL语句进行统计
    
    [FreeBuf-多线程WEB安全日志分析脚本](http://www.freebuf.com/sectool/110644.html)

    [我的日志分析之道：简单的Web日志分析脚本](http://www.freebuf.com/sectool/126698.html)

3. 干货好文

    [Web日志安全分析浅谈](http://bobao.360.cn/learning/detail/4009.html)

## 海量日志分析

- ### 工具

    spark等大数据相关工具

- ### 方法
1. 数据预处理
2. 数据挖掘算法(统计、分类、聚类、关联等多重方法)

- ### 模式
1. 基于特征的检测

    基于特征，是一种立竿见影的手段，对于一般的攻击很有效，但是永远不可能做到百分百，并且实效性极强，需要强大的响应队伍，对新漏洞尽可能快地做成特征库。

2. 鉴于行为的检测
    基于行为，是一种较为复杂的方式，是通过数学统计的方式来寻找异常，通过模式学习来寻找异常，但缺点是准确度不确定，可以做到很高，但误报率也很高。

- ### 参考资料
[使用Flume+Kafka+SparkStreaming进行实时日志分析](http://blog.csdn.net/trigl/article/details/70237981)

## 机器学习方法

啥都不懂，扯犊子，占坑！！！
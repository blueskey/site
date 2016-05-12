---
layout: post
title: "字符编码学习"
date: 2015-05-21 10:01:32
categories: encoding
---


`code`：
    当计算机传到世界各个国家时，为了适合当地语言和字符，设计和实现类似 GB232/GBK/GB18030的编码方案。这样各搞一套，在本地使用没有问题，一旦出现在网络中，由于不兼容，互相访问就出现了乱码现象。想像一下，假如有一种编码，包含世上所有的符号，每个符号都给一个独一无二的编码，那么就不会有乱码了。这就是Unicode。Unicode
只是一个符号集，它只规定了符号二进制代码，却没有规定这个二进制代码应该如何存储。当一个unicode字符需要2个字节存储时，是高位在前，还是低位在前呢？都可以。高位在前即high-endian方式，低位在前则是low-endian模式。Unicode规范中定义，每一个文件的最前面加上一个表示编码顺序的字符，用FEFF表示。当一个文件头两个字节是“FEFF”时，表示文件采用high-endian模式，如果是“FFFE"，则表示采用的是low-endian模式。看上去Unicode似乎很好了，但是，对于英文（不仅是英文，所有可以用
ASCII表示的字符） 来说，有人看着那么多的零不舒服了，太浪费内存了。这是无法接受的，于是造就了多种Unicode的实现方式的出现，Unicode在很长一段时间无法推广。

`UTF-8 `：
    由于互联网的普及，强烈要求出现一种统一的编码方式，于是UTF-8 出现了。它是针对 Unicode的可变长度字符编码。在 utf-8中，每个0-127 的代码点用单个字节存放， 128及以上的则用 2-3个字节存放，最多使用 6个字节。这样的话，英文用 utf-8编码就和用ascii 码编码一样了。UTF-8 有以下编码规则：
如果一个字节，最高位（第 8 位）为 0，表示这是一个 ASCII 字符（00 - 7F）。可见，所有 ASCII 编码已经是 UTF-8 了。
如果一个字节，以 11 开头，连续的 1 的个数暗示这个字符的字节数，例如：110xxxxx 代表它是双字节 UTF-8 字符的首字节。
如果一个字节，以 10 开始，表示它不是首字节，需要向前查找才能得到当前字符的首字节


`网页编码`： 对于一个字符串，如果你不知道它的编码方式，那么它将没有任何意义，你可能无法正确地显示它。那么对于字符串，如何保存它的编码信息呢？这里有一标准来做这些事。例如，对于一封邮件，在表单的头部会有这样类似的信息： Content-Type: text/plain; charset="UTF-8" 。对于一个网页， http响应的头部包含一个类似的 Content-Type。这个不是html
页面上的编码。但是这样也存在问题，对于一个有着成千上万人用不能语言编写的网页，它可能根本不能真的知道每个网页所用的编码，所以它发送不了正确的 Content-Type头。为解决这个问题，所以需要在每个 html页面本身使用特殊的标记显示正确的 Content-Type。当然这里还存在一个问题，怎么读取一个不知道编码的
html文件呢？幸运的是，所有的编码在 32-127之间都是一样的，所以不管用的什么编码，你都能不用有趣的字母开头而获取到 html页面上的这些东西：
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
这个meta标签必须放在 <head>部分的第一个位置上，因为浏览器一看到这个就会停下来开始用你指定的编码来解析这个页面。
当浏览器在 http响应头或meat 标签中都找不到任何 Content-Type时，它就只能靠猜的方式了。根据字符使用的概率等。

参考链接：
<a href='http://www.joelonsoftware.com/articles/Unicode.html'>http://www.joelonsoftware.com/articles/Unicode.html</a>
<a href='http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html'>http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html</a>
<a href="{{site.baseurl}}/index.html">Go back</a>
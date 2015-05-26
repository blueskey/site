---
layout: post
title: "http accept header"
date: 2015-05-25 23:05:45 
categories: http
---
Accept首部为客户端提供了一种将其喜好和能力告知服务器的方式，包括它们想要什么，可以使用什么，以及最重要的，它们不想要什么。服务器可以根据这些额外信息对发送的内容做出更明知的决定，不会浪费时间和带宽来发送客户端无法使用的东西。

    Accept:告诉服务器能够发送哪些媒体类型。

    Accept-Charset: 能够发送哪些字符集

    Accept-Encoding: 能够发送哪些编码方式,常见的有giz,compress。（sdch最近新出的，chrome 和firefox支持）

    Accept-Language: 能够发送哪些语言

    质量值：允许客户端为每种偏好类别列出多种选项，并为每种偏好选项关联一个优先次序。

    


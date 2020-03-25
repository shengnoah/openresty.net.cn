---
title: openresty best practices
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: pra_nginx_nginx.html
folder: practices
---
# Nginx

Nginx ("engine x") 是一个高性能的 HTTP 和反向代理服务器，也是一个 IMAP/POP3/SMTP 代理服务器。Nginx 是由 Igor Sysoev 为俄罗斯著名的 Rambler.ru 站点开发的，第一个公开版本 0.1.0 发布于 2004 年 10 月 4 日。其将源代码以类 BSD 许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。

由于 Nginx 使用基于事件驱动的架构，能够并发处理百万级别的 TCP 连接，高度模块化的设计和自由的许可证使得扩展 Nginx 功能的第三方模块层出不穷。因此其作为 Web 服务器被广泛应用到大流量的网站上，包括淘宝、腾讯、新浪、京东等访问量巨大的网站。

2015 年 6 月，Netcraft 收到的调查网站有 8 亿多家，主流 web 服务器市场份额（前四名）如下表：

|Web服务器|市场占有率|
|:--------|:---------:|
|Apache|49.53%|
|Nginx|13.52%|
|Microsoft IIS|12.32%|
|Google Web Server|7.72%|

其中在访问量最多的一万个网站中，Nginx 的占有率已超过 Apache。

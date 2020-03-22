---
title:  "Openresty发布新版本-修复内存内容泄漏"
categories:  openresty
permalink: openresty-mem-leak.html
tags: [news]
published: true
summary: "Openresty发布新版本-修复内存内容泄漏"
hide_sidebar: true
---





刚刚发布了 OpenResty 1.15.8.3 安全更新正式版：[链接](https://openresty.org/en/ann-1015008003.html) 包含了最近社区报告的 Nginx core 和 HTTP Lua 模块中的安全漏洞，特别是 HackerOne 团队报告的内存内容泄漏隐患。当 Lua 程序员在设置 URI 或请求头等数据时没有检查数据中是否存在换行符或其他控制字符时，可能会让攻击者注入恶意请求头或泄漏内存中的敏感数据。建议所有的 OpenResty 用户升级到这个版本。


[变更解析](https://mp.weixin.qq.com/s?__biz=MjM5NjEzNzU5OQ==&mid=2247484129&idx=1&sn=be67ff6502ce337068fb4fb2e495cdaf&chksm=a6ec9df1919b14e704ec1744dea6b4d66dcc25ff8fe417ea36a617e39f88223e983232c1aaa7&token=1129419040&lang=zh_CN#rd)



{% include links.html %}





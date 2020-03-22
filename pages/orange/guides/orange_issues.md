---
tags: [orange]
keywords: release notes, announcements, what's new, new features
last_updated: July 3, 2016
summary: "Orange依赖的OpenResty版本是多少？"
sidebar: orange_sidebar
permalink: orange_issues.html
folder: orange
category: Guides
redirect_from:
    - /docs/v0.4.0/guides/issues/
    - /docs/latest/guides/issues/
title: "常见问题"
sort_title: "3"
---


#### Orange依赖的OpenResty版本是多少？

Orange的监控插件需要统计http的某些状态数据，所以需要编译OpenResty时添加`--with-http_stub_status_module`。由于使用了*_block指令，所以OpenResty的版本最好在1.9.7.3以上.


#### start.sh脚本无法启动

检查OpenResty和lor是否安装成功。命令行要能直接执行`Nginx -v`和`lord -v`，并且Orange依赖的OpenResty版本应在1.9.7.3++，lor版本在v0.1.0+

#### 找不到libuuid.so

Orange现在依赖libuuid来生成规则id，centos用户可尝试使用`yum install  libuuid-devel`安装，其他用户请自行google对应平台的安装方式。

#### 默认的Dashboard登录账户是什么？

admin/orange_admin，登录后再修改或添加其他普通账户即可




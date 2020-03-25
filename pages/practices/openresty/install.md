---
tile: 环境搭建
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: install.html
folder: practices
---
# 环境搭建

实践的前提是搭建环境，本节的几个小节将介绍在几种常见操作平台上 OpenResty 的安装。

为了降低用户安装门槛，对于不同系统安装，部分章节存在比较大的重复内容。读者只需要选择自己需要的平台并尝试安装即可。除了 windows 版本是以二进制发行，其他平台由于系统自身的兼容性，推荐的都是源码编译方式。

在 OpenResty 的规划路线中，准备发行独立的 `opm` 安装工具（截止到目前，目前还没完成，名称可能依然会变），帮会我们完成从环境到周边库的下载、更新。

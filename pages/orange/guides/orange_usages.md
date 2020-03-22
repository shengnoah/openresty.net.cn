---
tags: [orange]
keywords: release notes, announcements, what's new, new features
last_updated: July 3, 2016
summary: "Orange是基于插件设计的，基本思想是通过实现各种插件灵活的在Nginx的各个执行阶段进行逻辑处理。"
sidebar: orange_sidebar
permalink: orange_usages.html
folder: orange
category: Guides
redirect_from:
    - /docs/v0.4.0/guides/usages/
    - /docs/latest/guides/usages/
title: "使用场景"
sort_title: "2"
---

Orange是基于插件设计的，基本思想是通过实现各种插件灵活的在Nginx的各个执行阶段进行逻辑处理。

Orange提供的默认插件功能如下：

- 全局的监控插件，可统计API访问情况、Nginx连接情况、流量统计、QPS等
- 自定义监控，可根据配置的规则筛选监控项，统计其各个指标
- rewrite插件，可通过UI配置各种rewrite策略，省去手写nginx rewrite，然后重启的麻烦
- redirect插件，实现请求重定向功能
- HTTP Basic Authorization，为API动态配置鉴权
- 简单地WAF应用防火墙，可提取各个请求参数，通过各种匹配规则甄别需要禁止的流量
- 分流插件，可分为三个使用场景：
    - 作为proxy，如代理后端的多个HTTP应用
    - 用于AB测试
    - 用于动态分流，API版本控制等

其它插件，用户可根据具体需要按规范编写即可。

## 典型使用案例

以下使用案例对于成体系、各职责/配置比较健全的团队来说可能并不存在，但仍可能具有一些参考意义。

### Case 1

想象下面的场景：

 - 原来的Nginx配置文件中包含大量的rewrite语句，每次更改需要重启
 - 有时候rewrite特别难写，比如我需要把来自于某个Host并且uri以`/user`开头的请求rewrite到以`/some_host/user`开头，如果是在Nginx里写，一般要通过`if`判断Host，再rewrite，可能需要多次调试和reload才能成功。这对于有经验的运维或是开发可能并不是难事，但对刚接手的新人或是不熟悉Nginx的运维人员来说，就是一件头疼事儿了
 - 再想像一下，如果再加上几个限制条件，比如需要来自某些IP访问某个Host并且Header里含有某个标识变量的请求执行某个rewrite，Orz...
 - 原来对于某个移动端API，客户端已经发布了很多个版本，每个版本的该API内部实现可能都不太一样，如果需要后端去兼容这个更改，可能就需要在代码中添加大量的`if/else`，或者给同一个职责的API取不同的名字(其它更好的实践暂且不表)。长此以往，API的版本管理可能就是一团乱麻

这时，通过Orange的rewrite/redirect插件就能很方便的解决这个问题，并且实时生效无需重启或是reload：

 - orange的rewrite/redirect规则配置非常简单，基本上只需要了解一些Nginx/lua正则表达式的知识即可解决上述场景中提到的问题，无须更改Nginx配置文件
 - orange可以提取请求中的各个变量或是标识，比如URI、Header、QueryString、Referer、Host等等，然后根据这些提取出来的值做匹配规则运算，匹配成功的就可以执行各种rewrite、redirect操作将uri变得更有条理，也更好管理。


### Case 2

在内部系统中，大量模块或者异构的子系统之间都是通过HTTP交互的，这时不可避免的要引入一个七层负载，选型最多的基本上也就是Nginx了。对于内部网关的管理，可能存在的问题：

 - 由于是内部使用，运维对外网隔离比较严格，但内网隔离性可能就会略差，这是就可能发生内部服务API被调用者误调用或者乱调用的问题
 - 为了使用方便，内部的一些通用API是不需要鉴权的，但当需要排查问题的时候，如果有多个使用方，很难甄别到底是谁调用的

Orange提供的WAF插件可以解决这个问题：
 
 - 如对API请求做白名单限制，白名单可以以比较常用的IP、Header作为判断条件
 - Orange提供的默认插件都有Log功能，可将某条匹配规则的Log开启，这样出现问题时就可以根据匹配规则的Log，然后结合Nginx本身的访问日志来甄别流量来源了


### Case 3

应用的AB测试或者流量切分，虽然业界已经有很多方案可供参考，各个公司或团队也有相应实现，Orange给出了另一种动态解决方案

 - 由自定义的条件表达式选择出某部分流量，动态分流到指定的upstream server，结束后只需要关闭这条规则即可
 - 线上问题排查，如果某个API出了问题，可临时添加一条规则，将测试账户的流量打到某台机器或者某个新的bug fix的upstream server，待排查完后清除规则、升级bug fix版本





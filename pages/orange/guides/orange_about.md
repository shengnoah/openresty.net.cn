---
tags: [orange]
keywords: orange
last_updated: July 3, 2016
summary: "Orange是一个基于OpenResty的API Gateway，提供API及自定义[规则](/docs/rule.html)的监控和管理，如访问统计、流量切分、API重定向、API鉴权、WEB防火墙等功能。它有以下特性："
sidebar: orange_sidebar
permalink: orange_about.html
folder: orange
category: Guides
redirect_from:
    - /docs/v0.4.0/guides/about/
    - /docs/latest/guides/about/
title: "关于Orange"
sort_title: "1"
---

欢迎使用Orange，使用过程中如碰到问题，请到[Github](https://github.com/sumory/orange/issues)进行提问。


### 关于

Orange是一个基于OpenResty的API Gateway，提供API及自定义[规则](/docs/rule.html)的监控和管理，如访问统计、流量切分、API重定向、API鉴权、WEB防火墙等功能。它有以下特性：

- 通过MySQL存储来简单支持集群部署
- 支持多种[条件匹配和变量提取](/docs/expression.html)
- 支持通过自定义插件方式扩展功能
- 默认内置的插件
    - 全局状态统计
    - 自定义监控
    - URL重写
    - URI重定向
    - 访问速度控制Rate Limiting
    - HTTP Basic Auth
    - HTTP Key Auth
    - 简单防火墙WAF
    - 代理、ABTesting、分流
- 提供管理界面用于管理内置插件
- 开放API: 灵活配置插件、查看运行状态、统计数据等


### API

内置插件以HTTP Restful形式开放全部API，详细可查看[API文档](http://orange.sumory.com/plugin/)


### 注意

- 现实中由于用户的业务系统多种多样，对于复杂应用，Orange并不是一个开箱即用的组件，需要调整一些配置才能集成到现有系统中。
- Orange提供的的配置文件和示例都是最简配置，用户使用时请根据具体项目或业务需要自行调整，这些调整可能包括但不限于:

    - 使用的各个shared dict的大小， 如ngx.shared.status
    - nginx.conf配置文件中各个server、location的配置及其权限控制，比如orange dashboard/API的server应该只对内部有权限的机器开放访问
    - 根据不同业务而设置的不同nginx配置，如timeout、keepalive、gzip、log、connections等等

### Licence

Orange采用MIT协议开源


### QQ群

522410959、141991731

### 其它

Orange的插件设计参考了[Kong](https://getkong.org)，Kong是一个功能比较全面的API Gateway实现，推荐关注。

---
tags: [orange]
keywords: release notes, announcements, what's new, new features
last_updated: July 3, 2016
summary: "如何设置Orange的监听服务。"
sidebar: orange_sidebar
permalink: orange_dashboard_usage.html
folder: orange
category: Advanced
redirect_from:
    - /docs/latest/advanced/dashboard_usage/
title: "如何使用Dashboard"
sort_title: "1"
---

启动Orange后，浏览器输入`http://localhost:9999`即可访问内置的Dashboard。

从v0.1.1版本开始，Orange Dashboard(默认在9999端口监听)可开启授权验证，只有通过授权的账户才能使用控制台。

请注意，目前仅当选择`store`为`mysql`时，才能使用该功能。

其相关的配置在orange.conf中:

```
 "dashboard": {
    "auth": false, //是否开启授权验证，开启时需要先登录才能使用Dashboard
    "session_secret": "y0ji4pdj61aaf3f11c2e65cd2263d3e7e5", // 用于加密cookie的盐，自行更改即可
    "whitelist": [//不需要判断是否登录的白名单url
        "^/auth/login$",
        "^/error/$"
    ]
}
```

在MySQL表`dashboard_user`中存储了Dashboard的用户信息，请导入该表到库中，参见sql文件[install/orange-v0.4.0.sql](https://github.com/sumory/orange/blob/master/intall/orange-v0.4.0.sql)

默认的管理员用户名和密码如下，登录后可更改密码或添加其他用户：

```
用户名：admin
密码：orange_admin
```



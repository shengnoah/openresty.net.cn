---
tags: [orange]
keywords: release notes, announcements, what's new, new features
last_updated: July 3, 2016
summary: "变量提取器"
sidebar: orange_sidebar
permalink: orange_extraction.html
folder: orange
category: Concept
redirect_from:
    - /docs/v0.4.0/concept/extraction/
    - /docs/latest/concept/extraction/
title: "变量提取器"
sort_title: "6"
---



每个提取器的格式如下：

```
{
    "type": "",
    "name": ""
},
```

实例：

```
{
    "type": "Query",
    "name": "username"
},
{
    "type": "Header",
    "name": "id"
} 
```

以下针对每个条件的格式作解释：

`type`即要提取的数据来源，目前包括以下几种：


- Header K/V
- Query K/V
- Post Params K/V
- Host
- URI
- IP
- Method

`name`指的是如果是K/V行的提取来源，name指要提取的key


如以下示例要提取QueryString里的username这个变量：

```
{
    "type": "Query",
    "name": "username"
}
```


---
tags: [orange]
keywords: release notes, announcements, what's new, new features
last_updated: July 3, 2016
summary: "匹配条件"
sidebar: orange_sidebar
permalink: orange_condition.html
folder: orange
category: Concept
redirect_from:
    - /docs/v0.4.0/concept/condition/
    - /docs/latest/concept/condition/
title: "匹配条件"
sort_title: "5"
---


每个条件的格式如下：

```
{
    "type": "",
    "operator": "",
    "name":"", //可能为空
    "value": ""
}
```

实例：

```
{
    "type": "Header",
    "operator": "=",
    "name": "uid",
    "value": "123"
},
{
    "type": "Host",
    "operator": "=",
    "value": "127.0.0.1"
}

```

以下针对每个条件的格式作解释：

`type`即要匹配的数据来源，也就是支持的条件提取方式，目前包括以下几种：

- URI
- IP
- Header K/V
- Query K/V
- Post Params K/V
- Host
- Referer
- UserAgent

`operator`即匹配符，指的是要对提取出的值做的操作，包含：

- `=`
- `!=`
- `match` 正则匹配
- `not_match` 正则匹配后再取非
- `>`
- `>=`
- `<`
- `<=`

`name`, 当type为K/V型的来源，如"Header"、"Query"、"PostParams"时此字段不为空，即要从这项来源中提取值对应的key。

`value`即要匹配的目标值，需要拿提取出的值来跟这个值使用`operator`做匹配操作。

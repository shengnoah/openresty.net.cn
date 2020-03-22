---
tags: [orange]
keywords: orange,api gateway
last_updated: July 3, 2016
summary: "条件判断模块"
sidebar: orange_sidebar
permalink: orange_judge.html
folder: orange
category: Concept
redirect_from:
    - /docs/v0.4.0/concept/judge/
    - /docs/latest/concept/judge/
title: "条件判断模块"
sort_title: "2"
---

条件判断模块，是一系列[匹配条件](/docs/concept/condition)和使用方式的集合。它由以下几部分组成

- type: 0表示只有一个匹配条件，1表示对所有条件与操作，2表示对所有条件或操作，3表示按照另一个字段`expression`对所有条件求值
- expression: 当type为3时，此字段不为空，它的格式是一个lua的逻辑判断表达式。表达式中每个值的格式为`v[index]`, 比如v[1]对应的就是第一个条件的值。示例：(v[1] or v[2]) and v[3]，即前两个条件至少一个为真并且第三个条件为真时，规则为真。
- conditions: [匹配条件](/docs/concept/condition)集合

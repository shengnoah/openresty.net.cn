---
tags: [orange]
keywords: orange,api gateway
last_updated: July 3, 2016
summary: "规则"
sidebar: orange_sidebar
permalink: orange_rule.html
folder: orange
category: Concept
redirect_from:
    - /docs/v0.4.0/concept/rule/
    - /docs/latest/concept/rule/
title: "规则"
sort_title: "1"
---


** 规则 ** 是Orange中的一个概念，它可能包括一个[条件判断模块](/docs/concept/judge)、一个[变量提取模块](/docs/concept/extractor)和一个[后续处理模块](/docs/concept/handle)。一般情况下，插件的一条规则会包含条件判断模块和一个处理模块，根据需要选择是否使用变量提取模块，如分流插件就需要变量提取模块，但防火墙插件则不需要。

下面通过一个具体的实例看一下它的组成。


### 格式

以`重定向插件`的一条规则的结构为例，介绍如下(以json格式描述):

```
{
    "enable": true,
    "name": "用户访问重定向",
    "id": "F7F73D94-AEEB-4B61-9E59-AEDBF200B941",
    "time": "2016-05-04 16:05:03",
    "judge": {
        "type": 1,
        "conditions": [
            {
                "type": "URI",
                "operator": "match",
                "value": "^/user"
            }
        ]
    },
    "extractor": {
        "type": 1,
        "extractions": [
            {
                "type": "Query",
                "name": "username"
            },
            {
                "type": "Header",
                "name": "id"
            }
        ]
    },
    "handle": {
        "trim_qs": false,
        "url_tmpl": "/u/${2}/${1}",
        "log": true
    }
}
```

### 字段描述

各个字段的解释如下：

#### enable

是否开启这条规则  


#### name

规则名称


#### id

这条规则的id，用于修改和删除  

#### time

添加或者更改时间



#### judge

规则适配器，用于筛选请求，详见[规则适配器](/docs/concept/judge/)



#### extractor

变量提取模块，用于提取出请求中的变量供后续使用，详见[变量提取模块](/docs/concept/extractor)


#### handle

这条规则对应的后续处理，如果这条规则有一个下游触发动作，此值不为空。不同的插件这个字段的子字段可能不同。

重定向插件的该字段子字段描述如下：

``` 
log: 当执行这个动作时，是否要记录日志
url_tmpl: 后续要redirect到的url模板，可在里面使用`变量提取模块`提取出来的值
trim_qs: 是否要清除原始请求的QueryString
```


#### judge.conditions

条件集合，每个条件即为对某项值的判断，这个值是从HTTP请求中提取出来的。详见[匹配条件](/docs/concept/condition)


#### extractor.extractions

从哪些项中提取变量。详见[变量提取器](/docs/concept/extraction)

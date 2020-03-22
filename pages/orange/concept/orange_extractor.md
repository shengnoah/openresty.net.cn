---
tags: [orange]
keywords: orange,api gateway
last_updated: July 3, 2016
summary: "变量提取模块，是一系列[变量提取器](/docs/concept/extraction)的集合，一般格式如下："
sidebar: orange_sidebar
permalink: orange_extractor.html
folder: orange
category: Concept
redirect_from:
    - /docs/v0.4.0/concept/extractor/
    - /docs/latest/concept/extractor/
title: "变量提取模块"
sort_title: "3"
---

变量提取模块，是一系列[变量提取器](/docs/concept/extraction)的集合，一般格式如下：

```
"extractor": {
    "type": 1,//提取器类型，1指索引式提取，2指模板式提取
    "extractions": [
        {//一个提取器
            "type": "Query",
            "name": "wd"
        },
        {
            "type": "Header",
            "name": "from",
            "default": "this_is_default_value"
        }
    ]
}
```

关于两种变量提取器的描述，可以参看[#issues15](https://github.com/sumory/orange/issues/15)

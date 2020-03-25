---
title: openresty best practices
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: pra_nginx_match_uri.html
folder: practices
---
# 如何匹配 URI

location 语法:	`location 参数 uri { 配置内容 }`
location 块可存在的的上下文环境包括 server 块 和 location 块。server 块不用解释，但可以存在于 location 块的原因是：location 块允许嵌套。

>location 参数

|符号|匹配模式|优先级|
|:--:|:-----------------------------|:--:|
| =  | 字符串精确匹配               |最高|
| ^~ | 字符串前缀匹配               | 高 |
| ~  | 正则表达式匹配，大小写敏感   |一般|
| ~* | 正则表达式匹配，大小写不敏感 |一般|

除了上面给出的四个匹配模式之外，还可以使用"/"（根目录）符号来捕获所有没有匹配成功的 location。

普通字符串精确匹配的优先级最高，如果在这一级匹配成功，则直接进入配置内容。其次使用  `^~`  的字符串前缀匹配优先级第二。正则表达式的优先级排第三，其中正则表达式的两种匹配模式( `~` 或 `~\*` )是平级的关系。优先级最低的是没有任何参数的普通字符串匹配。

下面结合代码范例来讲解：

```nginx
location /              {echo "no match";}
location = /            {echo "home";}
location ^~ /conf1      {echo "conf1";}
location /conf2         {echo "conf2";}
location ~ ^/conf.$     {echo "conf3";}
location ~ ^/conf.*?$   {echo "conf4";}
location /conf123       {echo "conf123";}
```

```shell
curl localhost/              #输出：home
curl localhost/nomatch       #输出：no match
curl localhost/conf1         #输出：conf1
curl localhost/conf2         #输出：conf3
curl localhost/conf123       #输出：conf1
curl localhost/conf1234      #输出：conf1
curl localhost/conf9         #输出：conf3
curl localhost/conf987       #输出：conf4
```

并不是将哪个 location 规则写在前面，它就被优先匹配。例如，我们将可以捕获所有规则的"/"(根目录)写在最前面，但它并没有捕获所有的规则，而是老老实实按照优先级来办事。但是所有规则都有例外，这唯一一个例外就是当我们进行正则表达式匹配( `~` 或 `~\*` )的时候，谁被写在前面谁就被优先匹配。

我们来依次解释上面的输出结果：
* /与配置 2 精确匹配，输出：home
* /nomatch 与除了配置 1 外的结果都不匹配，所以输出 no match
* /conf1 同时与配置 1 、配置 3 、配置 5 、配置 6 匹配，由于配置 3 是字符串前缀匹配在所有被捕获的匹配中优先级最高，所以输出 conf1。
* /conf2 同时与配置 1 、配置 4 、配置 5 、配置 6 匹配，其中与配置 4 虽然完全一样，但由于只是普通字符串匹配(注意：普通字符串匹配和普通字符串精确匹配是两个概念，后者仅当使用 `=` 参数时成立)，优先级比配置 5 和配置 6 的正则表达式匹配要低，由于配置 5 写在配置 6 的前面，所以被配置 5 捕获，所以输出 conf3。
* /conf123 同时与配置 1 、配置 3 、配置 6 、配置 7 匹配，其中与配置 7 虽然完全一样，但由于配置 3 是字符串前缀匹配，在被捕获的配置中优先级最高，所以输出 conf1。
* /conf1234 同时与配置 1 、配置 3 、配置 6 、配置 7(为什么会匹配配置 7 ，因为普通字符串匹配默认即是前缀匹配，只是不享有 `^~` 显式声明的优先级而已)匹配，但由于配置 3 是字符串前缀匹配，在被捕获的配置中优先级最高，所以输出 conf1。
* /conf9 同时与配置 1 、配置 5 、配置 6 匹配，由于配置 5 是正则表达式匹配，且顺序先于配置 6 ，所以输出：conf3。
* /conf987 同时与配置 1 、配置 6 匹配，尽管配置 1 在 conf 文件中的声明顺序先于配置 6 ，配置 6 的正则表达式匹配优先级高于配置 1 的普通字符串匹配，所以仍然输出：conf4


还有两个不容易引人注意的细节：
1. 在正则表达式模式下，注意将正则表达式后面用空格与 location 块的大括号隔开。否则有可能出现这样的报错：

```shell
directive "location" has no opening "{" in */nginx.conf
```

2. 还是在正则表达式模式下，正则表达式并非从字符串开头开始匹配，除非你显式地使用 `^`、`$` 符号来限定正则表达式匹配的开头和结尾。实例如下：

```nginx
location ~ hell {echo "hello world"}
location  /what {echo "what?"}
```

当我们使用 `curl localhost/whathell` 时，由于正则表达式并不从开头开始匹配，所以也会匹配到第一个配置。由于正则表达式的优先级比较高，所以当你疑惑为什么你的代码不能进入预期的配置时，可以检查一下是否是该问题。
另外，对于上面的正则表达式，将其修改成 `^hell$` 来限定字符串的开头结尾，是一种良好的编码风格。

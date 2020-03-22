---
title: Agentzh的Nginx教程
tags: [OOM,openresty]
keywords: openresty
last_updated: July 31, 2016
summary: "Agentzh的Nginx教程"
sidebar: nginx_sidebar
permalink: agentzh-nginx-guide-01.html
folder: agentzh
---


目录

    缘起
    Nginx 教程的连载计划
    Nginx 变量漫谈（一）
    Nginx 变量漫谈（二）
    Nginx 变量漫谈（三）
    Nginx 变量漫谈（四）
    Nginx 变量漫谈（五）
    Nginx 变量漫谈（六）
    Nginx 变量漫谈（七）
    Nginx 变量漫谈（八）

## 缘起

其实这两年为 Nginx 世界做了这么多的事情，一直想通过一系列教程性的文章把我的那些工作成果和所学所知都介绍给更多的朋友。现在终于下决心在新浪博客 http://blog.sina.com.cn/openresty 上面用中文写点东西，每一篇东西都会有一个小主题，但次序和组织上就不那么讲究了，毕竟并不是一本完整的图书，或许未来我会将之整理出书也不一定。

我现在编写的教程是按所谓的“系列”来划分的，比如首先连载的“Nginx 变量漫谈”系列。每一个系列基本上都可以粗略对应到未来出的 Nginx 书中的一“章”（当然内部还会重新组织内容并划分出“节”来）。我面向的读者是各个水平层次的 Nginx 用户，同时也包括未使用过 Nginx 的 Apache、Lighttpd 等服务器的老用户。

我只保证这些教程中的例子至少兼容到 Nginx 0.8.54，别用更老的版本来找我的错处，我一概不管，毕竟眼下最新的稳定版已经是 1.0.10 了。

凡在教程里面提到的模块，都是经过生产环境检验过的。即便是标准模块，如果没有达到生产标准，或者有重要的 bug，我也不会提及。

我在教程中会大量使用非标准的第三方模块，如果你怕麻烦，不愿自己一个一个从网上下载和安装那些个模块，我推荐你下载和安装我维护的 ngx_openresty 这个软件包：

http://openresty.org/

教程里提及的模块，包括足够新的 Nginx 稳定版核心，都包含在了这个软件包中。

我在这些教程中遵循的一个原则是，尽量通过短小精悍的实例来佐证我陈述的原理和观点。我希望帮助读者养成不随便听信别人现成的观点和陈述，而通过自己运行实例来验证的好习惯。这种风格或许也和我在 QA 方面的背景有关。事实上我在写作过程中也经常按我设计的小例子的实际运行结果，不断地对我的理解以及教程的内容进行修正。

对于有问题的代码示例，我们会有意在排版上让它们和其他合法示例所有区别，即在问题示例的每一行代码前添加问号字符，即（?），一个例子是：
```shell
server {
    listen 8080;

    location /bad {
        echo $foo;
    }
}
```

未经我的同意，请不要随便转载或者以其他方式使用这些教程。因为其中的每一句话，除了特别引用的“名句”，都是我自己的，我保留所有的权利。我不希望读者转载的另一大原因在于：转载后的拷贝版本是死的，我就不能再同步更新了。而我经常会按照读者的反馈，对已发表的老文章进行了大面积的修订。

我欢迎读者多提宝贵意见，特别是建设性的批评意见。类似“太烂了！”这样无聊中伤的意见我看还是算了。

所有这些文章的源都已经放在 GitHub 网站上进行版本控制了：

http://github.com/agentzh/nginx-tutorials/

源文件都在此项目的 zh-cn/ 目录下。我使用了一种自己设计的 Wiki 和 POD 标记语言的混合物来撰写这些文章，就是那些 .tut 文件。欢迎建立分支和提供补丁。

本教程适用于普通手机、Kindle、iPad/iPhone、Sony 等电子阅读器的 .html、.mobi、.epub 以及 .pdf 等格式的电子书文件可以从下面这个位置下载：

http://openresty.org/#eBooks

章亦春 (agentzh) 于福州家中

2011 年 11 月 30 日
## Nginx 教程的连载计划

下面以教程系列为单位，列举出了已经发表和计划发表的连载教程：

    Nginx 新手起步
    Nginx 是如何匹配 URI 的
    Nginx 变量漫谈
    Nginx 配置指令的执行顺序
    Nginx 的 if 是邪恶的
    Nginx 子请求
    Nginx 静态文件服务
    Nginx 的日志服务
    基于 Nginx 的应用网关
    基于 Nginx 的反向代理
    Nginx 与 Memcached
    Nginx 与 Redis
    Nginx 与 MySQL
    Nginx 与 PostgreSQL
    基于 Nginx 的应用缓存
    Nginx 中的安全与访问控制
    基于 Nginx 的 Web Service
    Nginx 驱动的 Web 2.0 应用
    测试 Nginx 及其应用的性能
    借助 Nginx 社区的力量

这些系列的名字和最终我的 Nginx 书中的“章”名可以粗略地对应上，但不会等同。同时未发表的系列的名字也可能发生变化，同时实际发表的顺序也会和这里列出的顺序不太一样。

上面的列表会随时更新，以保证总是反映了最新的计划和发表情况。

## Nginx 变量漫谈（一）

Nginx 的配置文件使用的就是一门微型的编程语言，许多真实世界里的 Nginx 配置文件其实就是一个一个的小程序。当然，是不是“图灵完全的”暂且不论，至少据我观察，它在设计上受 Perl 和 Bourne Shell 这两种语言的影响很大。在这一点上，相比 Apache 和 Lighttpd 等其他 Web 服务器的配置记法，不能不说算是 Nginx 的一大特色了。既然是编程语言，一般也就少不了“变量”这种东西（当然，Haskell 这样奇怪的函数式语言除外了）。

熟悉 Perl、Bourne Shell、C/C++ 等命令式编程语言的朋友肯定知道，变量说白了就是存放“值”的容器。而所谓“值”，在许多编程语言里，既可以是 3.14 这样的数值，也可以是 hello world 这样的字符串，甚至可以是像数组、哈希表这样的复杂数据结构。然而，在 Nginx 配置中，变量只能存放一种类型的值，因为也只存在一种类型的值，那就是字符串。

比如我们的 nginx.conf 文件中有下面这一行配置：


```shell
    set $a "hello world";
```

我们使用了标准 ngx_rewrite 模块的 set 配置指令对变量 $a 进行了赋值操作。特别地，我们把字符串 hello world 赋给了它。

我们看到，Nginx 变量名前面有一个 $ 符号，这是记法上的要求。所有的 Nginx 变量在 Nginx 配置文件中引用时都须带上 $ 前缀。这种表示方法和 Perl、PHP 这些语言是相似的。

虽然 $ 这样的变量前缀修饰会让正统的 Java 和 C# 程序员不舒服，但这种表示方法的好处也是显而易见的，那就是可以直接把变量嵌入到字符串常量中以构造出新的字符串：

```shell
    set $a hello;
    set $b "$a, $a";
```

这里我们通过已有的 Nginx 变量 $a 的值，来构造变量 $b 的值，于是这两条指令顺序执行完之后，$a 的值是 hello，而 $b 的值则是 hello, hello. 这种技术在 Perl 世界里被称为“变量插值”（variable interpolation），它让专门的字符串拼接运算符变得不再那么必要。我们在这里也不妨采用此术语。

我们来看一个比较完整的配置示例：

```shell
    server {
        listen 8080;

        location /test {
            set $foo hello;
            echo "foo: $foo";
        }
    }
```

这个例子省略了 nginx.conf 配置文件中最外围的 http 配置块以及 events 配置块。使用 curl 这个 HTTP 客户端在命令行上请求这个 /test 接口，我们可以得到
    $ curl 'http://localhost:8080/test'
    foo: hello

这里我们使用第三方 ngx_echo 模块的 echo 配置指令将 $foo 变量的值作为当前请求的响应体输出。

我们看到， echo 配置指令的参数也支持“变量插值”。不过，需要说明的是，并非所有的配置指令都支持“变量插值”。事实上，指令参数是否允许“变量插值”，取决于该指令的实现模块。

如果我们想通过 echo 指令直接输出含有“美元符”（$）的字符串，那么有没有办法把特殊的 $ 字符给转义掉呢？答案是否定的（至少到目前最新的 Nginx 稳定版 1.0.10）。不过幸运的是，我们可以绕过这个限制，比如通过不支持“变量插值”的模块配置指令专门构造出取值为 $ 的 Nginx 变量，然后再在 echo 中使用这个变量。看下面这个例子：

```shell
    geo $dollar {
        default "$";
    }

    server {
        listen 8080;

        location /test {
            echo "This is a dollar sign: $dollar";
        }
    }
```

测试结果如下：

```shell
    $ curl 'http://localhost:8080/test'
    This is a dollar sign: $
```

这里用到了标准模块 ngx_geo 提供的配置指令 geo 来为变量 $dollar 赋予字符串 "$"，这样我们在下面需要使用美元符的地方，就直接引用我们的 $dollar 变量就可以了。其实 ngx_geo 模块最常规的用法是根据客户端的 IP 地址对指定的 Nginx 变量进行赋值，这里只是借用它以便“无条件地”对我们的 $dollar 变量赋予“美元符”这个值。

在“变量插值”的上下文中，还有一种特殊情况，即当引用的变量名之后紧跟着变量名的构成字符时（比如后跟字母、数字以及下划线），我们就需要使用特别的记法来消除歧义，例如：

```shell
    server {
        listen 8080;

        location /test {
            set $first "hello ";
            echo "${first}world";
        }
    }
```

这里，我们在 echo 配置指令的参数值中引用变量 $first 的时候，后面紧跟着 world 这个单词，所以如果直接写作 "$firstworld" 则 Nginx “变量插值”计算引擎会将之识别为引用了变量 $firstworld. 为了解决这个难题，Nginx 的字符串记法支持使用花括号在 $ 之后把变量名围起来，比如这里的 ${first}. 上面这个例子的输出是：


```shell
    $ curl 'http://localhost:8080/test
    hello world
```

set 指令（以及前面提到的 geo 指令）不仅有赋值的功能，它还有创建 Nginx 变量的副作用，即当作为赋值对象的变量尚不存在时，它会自动创建该变量。比如在上面这个例子中，如果 $a 这个变量尚未创建，则 set 指令会自动创建 $a 这个用户变量。如果我们不创建就直接使用它的值，则会报错。例如

```shell
    server {
        listen 8080;
    
        location /bad {
            echo $foo;
        }
    }
```

此时 Nginx 服务器会拒绝加载配置:

```shell
    [emerg] unknown "foo" variable
```

是的，我们甚至都无法启动服务！

有趣的是，Nginx 变量的创建和赋值操作发生在全然不同的时间阶段。Nginx 变量的创建只能发生在 Nginx 配置加载的时候，或者说 Nginx 启动的时候；而赋值操作则只会发生在请求实际处理的时候。这意味着不创建而直接使用变量会导致启动失败，同时也意味着我们无法在请求处理时动态地创建新的 Nginx 变量。

Nginx 变量一旦创建，其变量名的可见范围就是整个 Nginx 配置，甚至可以跨越不同虚拟主机的 server 配置块。我们来看一个例子：

```shell
    server {
        listen 8080;

        location /foo {
            echo "foo = [$foo]";
        }

        location /bar {
            set $foo 32;
            echo "foo = [$foo]";
        }
    }
```

这里我们在 location /bar 中用 set 指令创建了变量 $foo，于是在整个配置文件中这个变量都是可见的，因此我们可以在 location /foo 中直接引用这个变量而不用担心 Nginx 会报错。

下面是在命令行上用 curl 工具访问这两个接口的结果：

```shell
    $ curl 'http://localhost:8080/foo'
    foo = []

    $ curl 'http://localhost:8080/bar'
    foo = [32]

    $ curl 'http://localhost:8080/foo'
    foo = []
```

从这个例子我们可以看到，set 指令因为是在 location /bar 中使用的，所以赋值操作只会在访问 /bar 的请求中执行。而请求 /foo 接口时，我们总是得到空的 $foo 值，因为用户变量未赋值就输出的话，得到的便是空字符串。

从这个例子我们可以窥见的另一个重要特性是，Nginx 变量名的可见范围虽然是整个配置，但每个请求都有所有变量的独立副本，或者说都有各变量用来存放值的容器的独立副本，彼此互不干扰。比如前面我们请求了 /bar 接口后，$foo 变量被赋予了值 32，但它丝毫不会影响后续对 /foo 接口的请求所对应的 $foo 值（它仍然是空的！），因为各个请求都有自己独立的 $foo 变量的副本。

对于 Nginx 新手来说，最常见的错误之一，就是将 Nginx 变量理解成某种在请求之间全局共享的东西，或者说“全局变量”。而事实上，Nginx 变量的生命期是不可能跨越请求边界的。

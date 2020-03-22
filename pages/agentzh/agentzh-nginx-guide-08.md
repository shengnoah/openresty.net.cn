---
title: Agentzh的Nginx教程
tags: [OOM,openresty]
keywords: openresty
last_updated: July 31, 2016
summary: "Agentzh的Nginx教程"
sidebar: nginx_sidebar
permalink: agentzh-nginx-guide-08.html
folder: agentzh
---

## Nginx 变量漫谈（八）

与 $arg_XXX 类似，我们在 （二） 中提到过的内建变量 $cookie_XXX 变量也会在名为 XXX 的 cookie 不存在时返回特殊值“没找到”：

    location /test {
        content_by_lua '
            if ngx.var.cookie_user == nil then
                ngx.say("cookie user: missing")
            else
                ngx.say("cookie user: [", ngx.var.cookie_user, "]")
            end
        ';
    }

利用 curl 命令行工具的 --cookie name=value 选项可以指定 name=value 为当前请求携带的 cookie（通过添加相应的 Cookie 请求头）。下面是若干次测试结果：

    $ curl --cookie user=agentzh 'http://localhost:8080/test'
    cookie user: [agentzh]

    $ curl --cookie user= 'http://localhost:8080/test'
    cookie user: []

    $ curl 'http://localhost:8080/test'
    cookie user: missing

我们看到，cookie user 不存在以及取值为空字符串这两种情况被很好地区分开了：当 cookie user 不存在时，Lua 代码中的 ngx.var.cookie_user 返回了期望的 Lua nil 值。

在 Lua 里访问未创建的 Nginx 用户变量时，在 Lua 里也会得到 nil 值，而不会像先前的例子那样直接让 Nginx 拒绝加载配置：

    location /test {
        content_by_lua '
            ngx.say("$blah = ", ngx.var.blah)
        ';
    }

这里假设我们并没有在当前的 nginx.conf 配置文件中创建过用户变量 $blah，然后我们在 Lua 代码中通过 ngx.var.blah 直接引用它。上面这个配置可以顺利启动，因为 Nginx 在加载配置时只会编译 content_by_lua 配置指令指定的 Lua 代码而不会实际执行它，所以 Nginx 并不知道 Lua 代码里面引用了 $blah 这个变量。于是我们在运行时也会得到 nil 值。而 ngx_lua 提供的 ngx.say 函数会自动把 Lua 的 nil 值格式化为字符串 "nil" 输出，于是访问 /test 接口的结果是：

    curl 'http://localhost:8080/test'
    $blah = nil

这正是我们所期望的。

上面这个例子中另一个值得注意的地方是，我们在 content_by_lua 配置指令的参数中提及了 $blah 符号，但却并没有触发“变量插值”（否则 Nginx 会在启动时抱怨 $blah 未创建）。这是因为 content_by_lua 配置指令并不支持参数的“变量插值”功能。我们前面在 （一） 中提到过，配置指令的参数是否允许“变量插值”，其实取决于该指令的实现模块。

设计返回“不合法”这一特殊值的例子是困难的，因为我们前面在 （七） 中已经看到，由 set 指令创建的变量在未初始化时确实是“不合法”，但一旦尝试读取它们时，Nginx 就会自动调用其“取处理程序”，而它们的“取处理程序”会自动返回空字符串并将之缓存住。于是我们最终得到的是完全合法的空字符串。下面这个使用了 Lua 代码的例子证明了这一点：

    location /foo {
        content_by_lua '
            if ngx.var.foo == nil then
                ngx.say("$foo is nil")
            else
                ngx.say("$foo = [", ngx.var.foo, "]")
            end
        ';
    }

    location /bar {
        set $foo 32;
        echo "foo = [$foo]";
    }

请求 /foo 接口的结果是：

    $ curl 'http://localhost:8080/foo'
    $foo = []

我们看到在 Lua 里面读取未初始化的 Nginx 变量 $foo 时得到的是空字符串。

最后值得一提的是，虽然前面反复指出 Nginx 变量只有字符串这一种数据类型，但这并不能阻止像 ngx_array_var 这样的第三方模块让 Nginx 变量也能存放数组类型的值。下面就是这样的一个例子：

    location /test {
        array_split "," $arg_names to=$array;
        array_map "[$array_it]" $array;
        array_join " " $array to=$res;

        echo $res;
    }

这个例子中使用了 ngx_array_var 模块的 array_split、 array_map 和 array_join 这三条配置指令，其含义很接近 Perl 语言中的内建函数 split、map 和 join（当然，其他脚本语言也有类似的等价物）。我们来看看访问 /test 接口的结果：

    $ curl 'http://localhost:8080/test?names=Tom,Jim,Bob'
    [Tom] [Jim] [Bob]

我们看到，使用 ngx_array_var 模块可以很方便地处理这样具有不定个数的组成元素的输入数据，例如此例中的 names URL 参数值就是由不定个数的逗号分隔的名字所组成。不过，这种类型的复杂任务通过 ngx_lua 来做通常会更灵活而且更容易维护。

至此，本系列教程对 Nginx 变量的介绍终于可以告一段落了。我们在这个过程中接触到了许多标准的和第三方的 Nginx 模块，这些模块让我们得以很轻松地构造出许多有趣的小例子，从而可以深入探究 Nginx 变量的各种行为和特性。在后续的教程中，我们还会有很多机会与这些模块打交道。

通过前面讨论过的众多例子，我们应当已经感受到 Nginx 变量在 Nginx 配置语言中所扮演的重要角色：它是获取 Nginx 中各种信息（包括当前请求的信息）的主要途径和载体，同时也是各个模块之间传递数据的主要媒介之一。在后续的教程中，我们会经常看到 Nginx 变量的身影，所以现在很好地理解它们是非常重要的。

在下一个系列的教程，即 Nginx 配置指令的执行顺序系列 中，我们将深入探讨 Nginx 配置指令的执行顺序以及请求的各个处理阶段，因为很多 Nginx 用户都搞不清楚他们书写的众多配置指令之间究竟是按照何种时间顺序执行的，也搞不懂为什么这些指令实际执行的顺序经常和配置文件里的书写顺序大相径庭。

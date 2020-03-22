---
title: Agentzh的Nginx教程
tags: [OOM,openresty]
keywords: openresty
last_updated: July 31, 2016
summary: "Agentzh的Nginx教程"
sidebar: nginx_sidebar
permalink: agentzh-nginx-guide-03.html
folder: agentzh
---

## Nginx 变量漫谈（三）

也有一些内建变量是支持改写的，其中一个例子是 $args. 这个变量在读取时返回当前请求的 URL 参数串（即请求 URL 中问号后面的部分，如果有的话），而在赋值时可以直接修改参数串。我们来看一个例子：

    location /test {
        set $orig_args $args;
        set $args "a=3&b=4";

        echo "original args: $orig_args";
        echo "args: $args";
    }

这里我们把原始的 URL 参数串先保存在 $orig_args 变量中，然后通过改写 $args 变量来修改当前的 URL 参数串，最后我们用 echo 指令分别输出 $orig_args 和 $args 变量的值。接下来我们这样来测试这个 /test 接口：

    $ curl 'http://localhost:8080/test'
    original args: 
    args: a=3&b=4

    $ curl 'http://localhost:8080/test?a=0&b=1&c=2'
    original args: a=0&b=1&c=2
    args: a=3&b=4

在第一次测试中，我们没有设置任何 URL 参数串，所以输出 $orig_args 变量的值时便得到空。而在第一次和第二次测试中，无论我们是否提供 URL 参数串，参数串都会在 location /test 中被强行改写成 a=3&b=4.

需要特别指出的是，这里的 $args 变量和 $arg_XXX 一样，也不再使用属于自己的存放值的容器。当我们读取 $args 时，Nginx 会执行一小段代码，从 Nginx 核心中专门存放当前 URL 参数串的位置去读取数据；而当我们改写 $args 时，Nginx 会执行另一小段代码，对相同位置进行改写。Nginx 的其他部分在需要当前 URL 参数串的时候，都会从那个位置去读数据，所以我们对 $args 的修改会影响到所有部分的功能。我们来看一个例子：

    location /test {
        set $orig_a $arg_a;
        set $args "a=5";
        echo "original a: $orig_a";
        echo "a: $arg_a";
    }

这里我们先把内建变量 $arg_a 的值，即原始请求的 URL 参数 a 的值，保存在用户变量 $orig_a 中，然后通过对内建变量 $args 进行赋值，把当前请求的参数串改写为 a=5 ，最后再用 echo 指令分别输出 $orig_a 和 $arg_a 变量的值。因为对内建变量 $args 的修改会直接导致当前请求的 URL 参数串发生变化，因此内建变量 $arg_XXX 自然也会随之变化。测试的结果证实了这一点：

    $ curl 'http://localhost:8080/test?a=3'
    original a: 3
    a: 5

我们看到，因为原始请求的 URL 参数串是 a=3, 所以 $arg_a 最初的值为 3, 但随后通过改写 $args 变量，将 URL 参数串又强行修改为 a=5, 所以最终 $arg_a 的值又自动变为了 5.

我们再来看一个通过修改 $args 变量影响标准的 HTTP 代理模块 ngx_proxy 的例子：

    server {
        listen 8080;

        location /test {
            set $args "foo=1&bar=2";
            proxy_pass http://127.0.0.1:8081/args;
        }
    }

    server {
        listen 8081;

        location /args {
            echo "args: $args";
        }
    }

这里我们在 http 配置块中定义了两个虚拟主机。第一个虚拟主机监听 8080 端口，其 /test 接口自己通过改写 $args 变量，将当前请求的 URL 参数串无条件地修改为 foo=1&bar=2. 然后 /test 接口再通过 ngx_proxy 模块的 proxy_pass 指令配置了一个反向代理，指向本机的 8081 端口上的 HTTP 服务 /args. 默认情况下， ngx_proxy 模块在转发 HTTP 请求到远方 HTTP 服务的时候，会自动把当前请求的 URL 参数串也转发到远方。

而本机的 8081 端口上的 HTTP 服务正是由我们定义的第二个虚拟主机来提供的。我们在第二个虚拟主机的 location /args 中利用 echo 指令输出当前请求的 URL 参数串，以检查 /test 接口通过 ngx_proxy 模块实际转发过来的 URL 请求参数串。

我们来实际访问一下第一个虚拟主机的 /test 接口：

    $ curl 'http://localhost:8080/test?blah=7'
    args: foo=1&bar=2

我们看到，虽然请求自己提供了 URL 参数串 blah=7，但在 location /test 中，参数串被强行改写成了 foo=1&bar=2. 接着经由 proxy_pass 指令将我们被改写掉的参数串转发给了第二个虚拟主机上配置的 /args 接口，然后再把 /args 接口的 URL 参数串输出。事实证明，我们对 $args 变量的赋值操作，也成功影响到了 ngx_proxy 模块的行为。

在读取变量时执行的这段特殊代码，在 Nginx 中被称为“取处理程序”（get handler）；而改写变量时执行的这段特殊代码，则被称为“存处理程序”（set handler）。不同的 Nginx 模块一般会为它们的变量准备不同的“存取处理程序”，从而让这些变量的行为充满魔法。

其实这种技巧在计算世界并不鲜见。比如在面向对象编程中，类的设计者一般不会把类的成员变量直接暴露给类的用户，而是另行提供两个方法（method），分别用于该成员变量的读操作和写操作，这两个方法常常被称为“存取器”（accessor）。下面是 C++ 语言中的一个例子：

    #include <string>
    using namespace std;

    class Person {
    public:
        const string get_name() {
            return m_name;
        }

        void set_name(const string name) {
            m_name = name;
        }

    private:
        string m_name;
    };

在这个名叫 Person 的 C++ 类中，我们提供了 get_name 和 set_name 这两个公共方法，以作为私有成员变量 m_name 的“存取器”。

这样设计的好处是显而易见的。类的设计者可以在“存取器”中执行任意代码，以实现所需的业务逻辑以及“副作用”，比如自动更新与当前成员变量存在依赖关系的其他成员变量，抑或是直接修改某个与当前对象相关联的数据库表中的对应字段。而对于后一种情况，也许“存取器”所对应的成员变量压根就不存在，或者即使存在，也顶多扮演着数据缓存的角色，以缓解被代理数据库的访问压力。

与面向对象编程中的“存取器”概念相对应，Nginx 变量也是支持绑定“存取处理程序”的。Nginx 模块在创建变量时，可以选择是否为变量分配存放值的容器，以及是否自己提供与读写操作相对应的“存取处理程序”。

不是所有的 Nginx 变量都拥有存放值的容器。拥有值容器的变量在 Nginx 核心中被称为“被索引的”（indexed）；反之，则被称为“未索引的”（non-indexed）。

我们前面在 （二） 中已经知道，像 $arg_XXX 这样具有无数变种的变量群，是“未索引的”。当读取这样的变量时，其实是它的“取处理程序”在起作用，即实时扫描当前请求的 URL 参数串，提取出变量名所指定的 URL 参数的值。很多新手都会对 $arg_XXX 的实现方式产生误解，以为 Nginx 会事先解析好当前请求的所有 URL 参数，并且把相关的 $arg_XXX 变量的值都事先设置好。然而事实并非如此，Nginx 根本不会事先就解析好 URL 参数串，而是在用户读取某个 $arg_XXX 变量时，调用其“取处理程序”，即时去扫描 URL 参数串。类似地，内建变量 $cookie_XXX 也是通过它的“取处理程序”，即时去扫描 Cookie 请求头中的相关定义的。

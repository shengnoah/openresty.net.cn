---
tile: Ubuntu平台安装
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: install_on_ubuntu.html
folder: practices
---
# Ubuntu 平台安装

#### 源码包准备

我们首先要在[官网](http://openresty.org/)下载 OpenResty 的源码包。官网上会提供很多的版本，各个版本有什么不同也会有说明，我们可以按需选择下载。
笔者选择下载的源码包为 ngx_openresty-1.9.7.1.tar.gz。

#### 相关依赖包的安装

首先你要安装 OpenResty 需要的多个库
请先配置好你的apt源，配置源的过程在这就不阐述了，然后执行以下命令安装OpenResty编译或运行时所需要的软件包。

```shell
# apt-get install libreadline-dev libncurses5-dev libpcre3-dev \
    libssl-dev perl make build-essential
```

如果你只是想测试一下OpenResty，并不想实际使用，那么你也可以不必去配置源和安装这些依赖库，请直接往下看。

#### OpenResty 安装

1. 在命令行中切换到源码包所在目录。
2. 解压源码包，```tar xzvf ngx_openresty-1.9.7.1.tar.gz```。若你下载的源码包版本不一样，将相应的版本号改为你所下载的即可。
3. 切换工作目录到 `cd ngx_openresty-1.9.7.1`。
4. 了解默认激活的组件。[OpenResty 官网](http://openresty.org/)有组件列表,我们可以参考，列表中大部分组件默认激活，也有部分默认不激活。
默认不激活的组件，我们可以在编译的时激活，后面步骤详说明。
5. 配置安装目录及需要激活的组件。使用选项 --prefix=install_path，指定安装目录（默认为/usr/local/openresty）。

    使用选项 --with-Components 激活组件，--without 则是禁止组件。
    你可以根据自己实际需要选择 with 或 without。如下命令，OpenResty 将配置安装在 /opt/openresty 目录下（注意使用 root 用户）,并激活`luajit`、`http_iconv_module` 并禁止 `http_redis2_module` 组件。

    ```shell
    # ./configure --prefix=/opt/openresty\
                --with-luajit\
                --without-http_redis2_module \
                --with-http_iconv_module
    ```

6. 在上一步中，最后没有什么 error 的提示就是最好的。若有错误，最后会显示
具体原因可以看源码包目录下的 `build/nginx-VERSION/objs/autoconf.err`文件查看。若没有错误，则会出现如下信息：

    ```shell
     Type the following commands to build and install:
         gmake
         gmake install
    ```

7. 编译：根据上一步命令提示，输入`gmake`。
8. 安装：输入`gmake install`。
9. 上面的步骤顺利完成之后，安装已经完成。可以在你指定的安装目录下看到一些相关目录及文件。

#### 设置环境变量

为了后面启动 OpenResty 的命令简单一些，不用在 OpenResty 的安装目录下进行启动，我们设置环境变量来简化操作。
将 nginx 目录添加到 PATH 中。打开文件 /etc/profile，
在文件末尾加入`export PATH=$PATH:/opt/openresty/nginx/sbin`，若你的安装目录不一样，则做相应修改。
注意：这一步操作需要重新加载环境变量才会生效，可通过命令`source /etc/profile`或者重启服务器等方式实现。

接下来，我们就可以进入到后面的章节 [HelloWorld](helloworld.md) 学习。


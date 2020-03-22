---
tags: [semaphore]
keywords: 
last_updated: July 3, 2016
summary: ""
sidebar: semaphore_sidebar
permalink: semaphore_awvs.html
folder: AWVS
category: API
redirect_from:
    - /AWVS/
title: "Semaphore自动扫描请求"
sort_title: "7"
flag: "2"
hide_sidebar: true
---



## 1)  漏洞扫描请求(REST API)

### 1.1 请求

序号 |URI                 | 方法 
---- |------------------- | ---- 
1 | /interface_update/     | Post

### 1.2 参数

序号| 名称 | 类型 | 说明
---- |---- | ---- | -------
1 |key | string | 请求密码key。 
2 |domain | string | 需测试服务的域名。 
3 |index | string | 路由。 
4 |file | string | 路由所在源文件名。 
5 |params | string | 路由带参数。 
6 |source | string | 发送扫描请求的服务器IP。 
7 |content | string | 扫描文件的全部源文件文本内容。 



```
{
    "error":"0", //0无错误， -1发生错误
    "errmsg":"none" /* none无错误信息，
                       key is empty!,
                       access key error,
                       source is empty,
                       domain is empty,
                       index is empty,
                       file is empty,
                       params is empty,
                       content is empty  */
                    
}
```

### 1.3 调用例子

```shell
curl -l -H "Content-type: application/json" -X POST -d '{"key":"test","domain":"test.com","index":"index.php","file":"index.php","params":"key1,key2,key3", "source":"test", "content":"test"}'  127.0.0.1:8080/interface_update/
```



## 2) 扫描接口(RPC)

### 2.1 HTTP请求
 
序号 |URI                 | 方法 
---- |------------------- | ---- 
1 |/json/     | Post

### 2.2 RPC请求

序号 | 接口名          | 函数名 
---- |------------------- | ---- 
1 |sayHello     | whats_the_time 



### 2.3 参数

序号 |名称 | 类型 | 说明 
---- |---- | ---- | -------
1 |name | string | 请求扫描的域名。 



### 2.4 返回结果

```
{
    "Hello ?"  //Hello + 返回域名。
}
```

### 2.5 调用例子

```shell
curl -l -H "Content-type: application/json" -X POST -d '{"key":"test","domain":"test.com","index":"index.php","file":"index.php","params":"key1,key2,key3", "source":"test", "content":"test"}'  127.0.0.1:5000/sidecar/
```



## 3) 请求扫描(Command)

### 3.1 请求

序号 |命令名                 | 文件名 
---- |------------------- | ---- 
1 |dsl     | dsl.py 



### 3.2 参数

序号 |名称 | 类型 | 说明 
---- |---- | ---- | -------
1 |-i —index | string | 路由索引。 
2 |-f —file | string | 文件名。 




**返回结果** 

```
{
    "Hello ?"  //Hello + 返回域名。
}
```



## 4) AWVS(库)

### 4.1）登录

#### 4.1.1 请求 

序号 | 函数名              | 文件名 
---- |------------------- | ---- 
1 |login     | awvs.py 

#### 4.1.2 参数

无

### 4.1.3 返回结果

```
{
    True  // True认证成功， False认证失败
}
```



###  4.2）添加扫描目标

#### 4.2.1 请求

序号 |函数名                 | 文件名 
- |------------------- | ---- 
1 |addTarget     | awvs.py 

#### 4.2.2 参数

序号| 名称 | 类型 | 说明 
- |---- | ---- | -------
1 |domain | string | 域名 

#### 4.2.3 返回结果

```
{
    True  // True扫描域名添加成功， 
          // False扫描域名添加失败
}
```


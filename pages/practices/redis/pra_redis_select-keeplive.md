---
title: openresty best practices
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: pra_redis_select-keeplive.html
folder: practices
---
# select + set_keepalive 组合操作引起的数据读写错误

在高并发编程中，必须要使用连接池技术，通过减少建连、拆连次数来提高通讯速度。

错误示例代码：

```lua
local redis = require "resty.redis"
local red = redis:new()

red:set_timeout(1000) -- 1 sec

-- or connect to a unix domain socket file listened
-- by a redis server:
--     local ok, err = red:connect("unix:/path/to/redis.sock")

local ok, err = red:connect("127.0.0.1", 6379)
if not ok then
    ngx.say("failed to connect: ", err)
    return
end

ok, err = red:select(1)
if not ok then
    ngx.say("failed to select db: ", err)
    return
end

ngx.say("select result: ", ok)

ok, err = red:set("dog", "an animal")
if not ok then
    ngx.say("failed to set dog: ", err)
    return
end

ngx.say("set result: ", ok)

-- put it into the connection pool of size 100,
-- with 10 seconds max idle time
local ok, err = red:set_keepalive(10000, 100)
if not ok then
    ngx.say("failed to set keepalive: ", err)
    return
end
```


如果单独执行这个用例，没有任何问题，用例是成功的。但是这段“没问题”的代码，却导致了诡异的现象。

大部分 Redis 请求的代码应该是类似这样的：

```lua
local redis = require "resty.redis"
local red = redis:new()

red:set_timeout(1000) -- 1 sec

-- or connect to a unix domain socket file listened
-- by a redis server:
--     local ok, err = red:connect("unix:/path/to/redis.sock")

local ok, err = red:connect("127.0.0.1", 6379)
if not ok then
    ngx.say("failed to connect: ", err)
    return
end

ok, err = red:set("cat", "an animal too")
if not ok then
    ngx.say("failed to set cat: ", err)
    return
end

ngx.say("set result: ", ok)

-- put it into the connection pool of size 100,
-- with 10 seconds max idle time
local ok, err = red:set_keepalive(10000, 100)
if not ok then
    ngx.say("failed to set keepalive: ", err)
    return
end
```

这时候第二个示例代码在生产运行中，会出现 cat 偶会被写入到数据库 1 上，且几率大约 1% 左右。出错的原因在于错误示例代码使用了 `select(1)` 操作，并且使用了长连接，并潜伏在连接池中。当下一个请求刚好从连接池中把他选出来，又没有重置 `select(0)` 操作，后面所有的数据操作就都会默认触发在数据库 1 上了。

怎么解决这个问题？

1. 谁制造问题，谁把问题遗留尾巴擦干净；
1. 处理业务前，先把 `前辈` 的尾巴擦干净；

这里明显是第一个好，对吧。

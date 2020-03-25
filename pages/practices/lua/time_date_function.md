---
tile: openresty best practices
tags: [openresty]
keywords: openresty
last_updated: July 3, 2016
summary: openresty best practices
sidebar: best_practice_sidebar
permalink: time_date_function.html
folder: lua
---
# 日期时间函数

在 Lua 中，函数 time、date 和 difftime 提供了所有的日期和时间功能。

在 OpenResty 的世界里，不推荐使用这里的标准时间函数，因为这些函数通常会引发不止一个昂贵的系统调用，同时无法为 LuaJIT JIT 编译，对性能造成较大影响。推荐使用 ngx_lua 模块提供的带缓存的时间接口，如 `ngx.today`, `ngx.time`, `ngx.utctime`,
`ngx.localtime`, `ngx.now`, `ngx.http_time`， 以及 `ngx.cookie_time` 等。

所以下面的部分函数，简单了解一下即可。

#### os.time ([table])

如果不使用参数 table 调用 time 函数，它会返回当前的时间和日期（它表示从某一时刻到现在的秒数）。如果用 table 参数，它会返回一个数字，表示该 table 中 所描述的日期和时间（它表示从某一时刻到 table 中描述日期和时间的秒数）。

对于 time 函数，如果参数为 table，那么 table 中必须含有 year、 month、 day字段。其他字缺省时段默认为中午（12:00:00）。

>示例代码：（地点为北京）

```lua
print(os.time())    -->output  1438243393
a = { year = 1970, month = 1, day = 1, hour = 8, min = 1 }
print(os.time(a))   -->output  60
```

#### os.difftime (t2, t1)

返回 t1 到 t2 的时间差，单位为秒。

>示例代码:

```lua
local day1 = { year = 2015, month = 7, day = 30 }
local t1 = os.time(day1)

local day2 = { year = 2015, month = 7, day = 31 }
local t2 = os.time(day2)
print(os.difftime(t2, t1))   -->output  86400
```

#### os.date ([format [, time]])

把一个表示日期和时间的数值，转换成更高级的表现形式。其第一个参数 format 是一个格式化字符串，描述了要返回的时间形式。第二个参数 time 就是日期和时间的数字表示，缺省时默认为当前的时间。使用格式字符 "*t"，创建一个时间表。

> 示例代码：

```lua
local tab1 = os.date("*t")  --返回一个描述当前日期和时间的表
local ans1 = "{"
for k, v in pairs(tab1) do  --把tab1转换成一个字符串
    ans1 = string.format("%s %s = %s,", ans1, k, tostring(v))
end

ans1 = ans1 .. "}"
print("tab1 = ", ans1)


local tab2 = os.date("*t", 360)  --返回一个描述日期和时间数为360秒的表
local ans2 = "{"
for k, v in pairs(tab2) do      --把tab2转换成一个字符串
    ans2 = string.format("%s %s = %s,", ans2, k, tostring(v))
end

ans2 = ans2 .. "}"
print("tab2 = ", ans2)

-->output
tab1 = { hour = 17, min = 28, wday = 5, day = 30, month = 7, year = 2015, sec = 10, yday = 211, isdst = false,}
tab2 = { hour = 8, min = 6, wday = 5, day = 1, month = 1, year = 1970, sec = 0, yday = 1, isdst = false,}
```

该表中除了使用到了 time 函数参数 table 的字段外，这还提供了星期（wday，星期天为1）和一年中的第几天（yday 一月一日为 1）。
除了使用 "*t" 格式字符串外，如果使用带标记（见下表）的特殊字符串，os.date 函数会将相应的标记位以时间信息进行填充，得到一个包含时间的字符串。

> 示例代码：

```lua
print(os.date("today is %A, in %B"))
print(os.date("now is %x %X"))

-->output
today is Thursday, in July
now is 07/30/15 17:39:22
```

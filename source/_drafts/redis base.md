---
title:  redis 基本命令
tags:  reids,java
notebook: 
---


> 基本命令


### 启动

reids-server redis.conf



### key

- keys *
- exists key
- move key db 移库
- expire key 秒   给key 设置过期时间
- ttl key   查看key还有多长时间过期
- type key  查看key类型

### string

set/get/del/append/strlen

incr/decr/incrby/decrby

getrange/setrage 

setex 
```
setex key seconds value
```

setnx 
```
# set key if not exist
setnx key value 
```

mset/mget/msetnx
```
同时设置（获取）一个或者多个 key-value 对

# msetnx
同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
```

### list

> 如果值为空，则key失效


lpush 
```
# 从左边插入
lpush key values ...
```

rpush 
```
# 从右边插入
rpush key values ...
```


lrange 
```
# 打印全部
lrange key 0 - 1
```

lpop/rpop

lindex
```
按照索引取元素
```

llen

lrem 
```
# 根据参数 count 的值，删除列表中与参数value相等的元素
lrem key count value
```

ltrim 
```
# 对列表进行截取，只保留制定区间内的元素

ltrim key start-index  end-index 
```

rpoplpush


lset
```
# 指定位置设置值
lset key index value 
```

linsert 
```
# 指定key的指定值的前/后插入值
linsert key before/after value1 value2
```
### sset

sadd
```
sadd  key values ....
```
smembers
```
列出所有元素
```

sismembers
```
是否有元素
```
scard
```
获取集合的元素个数
```
srem
```
# 删除集合元素
srem key value 
```

srandmember 
```
# 随记取出n个数
srandmember key n
```
spop 
```
# 随机出栈
spop key 
```

smove
```
# 将key1 中的  value 移动到 key2 中
smove key1 key2 value
```

sdiff
```
差集
```
sinter
```
交集
```
sunion
```
并集
```

### hash

> kv模式不变，但是value是个键值对

hset/hget

hmset/hmget
```
批量 set get
```
hgetall

hdel

hexists
```
# 是否存在哪个key
hexists key 
```

hkeys / hvals

hincrby/hincrbyfloat
```
# 为哈希表 key 中的域 field 的值加上增量 increment(也可以为负数)
hincrby key field number
```

hsetnx
```
# 将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在（如果存在，操作无效）。
HSETNX key field value
```
### zset

zadd
```
# 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。
zadd key score member 
```

zcard 
```
返回有序集 key 的基数。
```

zcount
```
# 返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量。
zcount key min max
```

zrange
```

```

zincrby 
```
# 为有序集 key 的成员 member 的 score 值加上增量 increment 。
zincrby key increment member
```

zrange / zrevrange (递增/递减)
```
# 返回有序集 key 中，指定区间内的成员。
zrange key start stop withscores
```

zrangebyscore
```
# 指定分数内的 member
zrangebyscore key start stop 

# 三个参数
withscores
( 不包含
limit  

```

zrank
```
# 返回有序集 key 中成员 member 的排名。其中有序集成员按 score 值递增(从小到大)顺序排列。
zrank key member
```

zscore
```
# 返回有序集 key 中，成员 member 的 score 值。
ZSCORE key member
```

zrevrank
```
# 返回有序集 key 中成员 member 的排名。其中有序集成员按 score 值递减(从大到小)排序。
zrevrank key member
```







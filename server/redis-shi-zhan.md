# Redis实战

## 1. 初始redis

## 1.1 Redis简介

### 1.1.1 Redis与其他数据库和软件对比

可以存储键和5种不同类型的值(value)之间的映射

## 1.2 Redis数据结构简介

|结构类型|结构存储的值|结构的读写能力|
|---|---|---|
|string|字符串, 整数或浮点数|操作字符串, 对整数和浮点数执行自增,自减操作|
|list|一个链表, 链表上每个节点都包含一个字符串|从链表两端推入或者弹出元素, 根据偏移量对链表进行修剪,读取单个或多个元素, 根据值查找或者移除元素|
|set|包含字符串的无序收集器, 并且被包含的每个字符串都是独一无二,各不相同|添加、获取、移除单个元素, 检查一个元素是否存在集合中, 计算交集、并集、差集, 从集合中随机获取元素|
|hash|包含键值对的无序散列表|添加、获取单个键值对, 获取所有键值对|
|zset(有序集合)|字符串成员与浮点数分值之间的有序映射, 元素的排列顺序由分支大小决定|添加、获取、删除单个元素, 根据分值范围或者成员来获取元素|

### 1.2.1 Redis中的字符串

命令: `get`, `set`, `del`

```bash
127.0.0.1:6379> set hello world
OK
127.0.0.1:6379> get hello
"world"
127.0.0.1:6379> del hello
(integer) 1
127.0.0.1:6379> get hello
(nil)
```

### 1.2.2 Redis中的列表

|命令|行为|
|---|---|
|rpush|rpush key-name value [value...] 将给定值推入列表的右端|
|lpush|lpush key-name value [value...] 将给定的值推入到列表左端|
|rpop|rpop key-name, 移除并返回列表最右侧的元素|
|lpop|lpop key-name, 从列表的左端弹出一个值|
|lrange|lrange key-name start end, 获取列表在给定范围上的所有值|
|lindex|lindex key-name offset, 获取列表在给定位置上的单个元素|
|ltrim|ltrim key-name start end,对列表进行裁剪[start, end], 保留start和end|
```bash
127.0.0.1:6379> rpush list-key item
(integer) 1
127.0.0.1:6379> rpush list-key item2
(integer) 2
127.0.0.1:6379> rpush list-key item
(integer) 3
127.0.0.1:6379> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"
127.0.0.1:6379> lindex list-key 1
"item2"
127.0.0.1:6379> lpop list-key
"item"
127.0.0.1:6379> lrange list-key 0 -1
1) "item2"
2) "item"set
```

### 1.2.3 Redis的集合

集合中存储的每个字符都要求唯一性

|命令|行为|
|---|---|
|SADD|sadd key-name item [item...], 将给定元素添加到集合, 返回添加成功元素的个数|
|SMEMBERS|smembers key-name, 返回集合包含的所在元素|
|SISMEMBER|sismember key-name item, 检查给定元素是否存在于集合中|
|SREM|srem key-name item [item ...]如果给定的元素存在于集合中, 那么移除这个元素, 返回移除元素的个数|
|SCARD|scard key-name, 返回集合包含的所有元素|
|srandmember|srandmember key-name [count], 从集合随机返回一个或多个元素, 当count为整数时, 随机元素不会重复, 为负数时, 可能会重复|
|spop|spop key-name, 随机移除集合中的一个元素, 并返回被移除的元素|
|smove|smove source-key dest-key item, 将item从source-key移除到dest-key,移除成功返回1, 不成功返回0|





```bash
127.0.0.1:6379> sadd set-key item
(integer) 1
127.0.0.1:6379> sadd set-key item2
(integer) 1
127.0.0.1:6379> sadd set-key item3
(integer) 1
127.0.0.1:6379> sadd set-key item
(integer) 0
127.0.0.1:6379> smembers set-key
1) "item"
2) "item3"
3) "item2"
127.0.0.1:6379> sismember set-key item4
(integer) 0
127.0.0.1:6379> sismember set-key item
(integer) 1
127.0.0.1:6379>
127.0.0.1:6379> srem set-key item2
(integer) 1
127.0.0.1:6379> srem set-key item2
(integer) 0
127.0.0.1:6379> smembers set-key
1) "item"
2) "item3"
```

### 1.2.4 Redis的散列

存储多个键值之间的映射, 

|命令|行为|
|---|---|
|HSET|在散列里面关联起给定的键值对|
|HGET|获取指定散列键的值|
|HGETALL|获取散列包含的所有键值对|
|HDEL|如果给定键存在于散列里面, 那么移除这个键|


```bash
127.0.0.1:6379> hset hash-key sub-key1 value1
(integer) 1
127.0.0.1:6379> hset hash-key sub-key2 value2
(integer) 1
127.0.0.1:6379> hset hash-key sub-key1 value1
(integer) 0
127.0.0.1:6379> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"
127.0.0.1:6379> hdel hash-key sub-key2
(integer) 1
127.0.0.1:6379> hdel hash-key sub-key2
(integer) 0
127.0.0.1:6379> hget hash-key sub-key1
"value1"
127.0.0.1:6379> hgetall hash-key
1) "sub-key1"
2) "value1"
```

### 1.2.5 Redis有序集合

存储格式为`成员: 分值`

每个成员都是各不相同的, 值必须为浮点数

有序集合是redis中即可根据成员访问元素(与散列一样), 也可以根据分值和分值的排序来访问元素的结构

|命令|行为|
|---|---|
|ZADD|zadd key-name score member [score member ...] ,将一个带有给定分值的成员添加到有序集合里面|
|ZRANGE|zrange key-name start stop [withscores] 根据元素在有序排列中所处的位置, 从有序集合了里面获取多个元素, 如果带有`withscores`会把成员的分值也一并返回|
|ZRANGEBYSCORE|获取有序集合中给定分值范围的所有元素|
|ZREM|zrem key-name mbmber [member ...],如果给定成员存在于有序集合中,那么移除该元素, 并返回被移除成员的数量|
|zcard|zcard key-name, 返回有序集合包含的成员数量|
|zincby|zincby key-name increment member, 将member成员的分值上加上increment|
|zcount|zcount key-name min max,返回分值介于min和max之间的成员数量|
|zrank|zrank key-name member, 返回成员member在有序集合中的排名|
|zscore|zscore key-name member, 返回成员member的分值|

有序集合的范围型数据获取

|命令|行为|
|---|---|
|zrevrank|zreverank key-name member, 返回有序集合组里成员member的排名, 成员按照分值从大到小排序|
|zrevrange|zrevrange key-name start stop [withscores], 返回有序集合给定排名范围内的成员, 成员按照分值从大到小排列|
|zrangebyscore|zrangebyscore key min max [withscores] [limit offset count], 获得有序集合中分值介于min和max之间的所有成员|
|zrevrangebyscore|zrevrangebyscore max min [withscores] [limit offset count], 获取有序集合中分值介于min和max之间的所有成员, 并按照分值从大到小的顺序返回他们|
|zremrangebyrank|zremrangebyrank key-name start stop, 移除有序集合中介于start和stop之间的所有成员|
|zremrangebyscore|zremrangebyscore key-name min max, 移除有序集合介于min和max之间的所有成员|
|zinterstore|zinterstore dest-key key-count key [key ...] [weights weight weight ...] [aggregate sum\|min\|max], 对给定的有序集合执行类似集合的交集运算|
|zunionstore|zunionstore dest-key key-count key [key ...] [weights weight weight ...] [aggregate sum\|min\|max], 对给定的有序集合进行类似集合的并集运算|

zinterstore和zunionstore默认使用的聚合是sum形式, 即合并同一key时, 将多个集合中同一key的value相加放到新的集合中

```bash
127.0.0.1:6379> zadd zset-key 728 member1
(integer) 1
127.0.0.1:6379> zadd zset-key 982 member0
(integer) 1
127.0.0.1:6379> zadd zset-key 982 member0
(integer) 0
127.0.0.1:6379> zrange zset-key 0 -1 withscores
1) "member1"
2) "728"
3) "member0"
4) "982"
127.0.0.1:6379> zrangebyscore  zset-key 0 800 withscores
1) "member1"
2) "728"
127.0.0.1:6379> zrem zset-key member1
(integer) 1
127.0.0.1:6379> zrem zset-key member1
(integer) 0
127.0.0.1:6379> zrange zset-key 0 -1 withscores
1) "member0"
2) "982"
```

# 3. Redis命令

## 3.1 字符串

整数的取值范围和系统的长整数的取值范围相同(32位系统, 整数就是32位有符号整数, 64位系统, 整数就是64位有符号整数)

而浮点数和IEEE754标准的双精度浮点数相同

redis的自增命令和自减命令

|命令|用途|
|---|---|
|incr|incr key-name, 键存储的值加一|
|decr|decr key-name, 键存储的值减一|
|incrby| incrby key-name amount, 键存储的值加上整数amount|
|decrby| decrby key-name amount, 键存储的值减上整数amount|
|incrbyfloat| incrbyfloat key-name amount, 将键存储的值加上浮点数amount|

如果对一个不存在的键或者空串键执行自增或自减操作, redis会把这个键当成0来处理

如果对一个无法被解释为整数或浮点数的字符串执行自增或自减, 会返回错误

redis处理子串和二进制位的命令

|命令|用途|
|---|---|
|append|append key-name value|
|getrange|getrange key-name start end|
|setrange|setrage key-name offset value, 将从offset偏移量开始的字符串设置为给定值|

## 3.2列表

阻塞式的列表弹出命令以及裂变间移动元素的命令

|命令|用途|
|---|---|
|blpop|blpop key-name [key-name...] timeout, 从第一个非空列表中弹出位于最左端的元素, 或者在timeout秒之内阻塞并等待可弹出的元素出现|
|brpop|bropo key-name [key-name...] timeout, 从第一个非空列表中弹出最右端的元素, 或者在timeout秒之内阻塞并等待可弹出的元素出现|
|rpoplpush|rpoplpush source-key dest-key, 从source-key列表中弹出最右侧的元素, 然后把这个元素推入到dest-key列表的最左端, 并向用户返回这个元素|
|brpoplpush|brpoplpush source-key dest-key timeout, 从source-key列表中弹出位于最右端的元素, 然后将这个元素推入dest-key列表的最左端, 如果source-key为空, 那么等待timeout秒source-key可弹出元素的出现|


```bash
127.0.0.1:6379> rpush list item1
(integer) 1
127.0.0.1:6379> rpush list item2
(integer) 2
127.0.0.1:6379> rpush list2 item3
(integer) 1
127.0.0.1:6379> brpoplpush list2 list 1
"item3"
127.0.0.1:6379> brpoplpush list2 list 1
(nil)
(1.03s)
127.0.0.1:6379> lrange list 0 -1
1) "item3"
2) "item1"
3) "item2"
127.0.0.1:6379> brpoplpush list list2 1
"item2"
127.0.0.1:6379> blpop list list2 1
1) "list"
2) "item3"
127.0.0.1:6379> blpop list list2 1
1) "list"
2) "item1"
127.0.0.1:6379> lrange list2 0 -1
1) "item2"
127.0.0.1:6379> blpop list list2 1
1) "list2"
2) "item2"
127.0.0.1:6379> blpop list list2 1
(nil)
(1.10s)
```

## 3.3 集合

redis的集合用无序的方式存储多个各不相同的元素

用于组合和处理多个集合的redis命令

|命令|说明|
|---|---|
|sdiff|sdiff key-name [key-name...], 返回存在第一个集合, 但不存在于其他集合的元素|
|sdiffstore|sdiffstore dest-key key-name [key-name...], 计算差集并存储到dest_key中|
|sinter|sinter key-name [key-name...], 返回同时存在所有集合的元素|
|sintersotre|sinterstore dest-key key-name [key-name...]|
|sunion|sunion key-name [key-name...] 求并集|
|sunionstore| sunionsotre dest-key key-name [key-name ...]|

```bash
127.0.0.1:6379> sadd skey1 a b c d
(integer) 4
127.0.0.1:6379>
127.0.0.1:6379> sadd skey2 a d e f
(integer) 4
127.0.0.1:6379> sdiff skey1 skey2
1) "b"
2) "c"
127.0.0.1:6379> sinter skey1 skey2
1) "d"
2) "a"
127.0.0.1:6379> sunion skey1 skey2
1) "c"
2) "b"
3) "e"
4) "d"
5) "a"
6) "f"
```

## 3.4 散列

|命令|用例和描述|
|hmget|hmget key-name key [key...],从散列中获取一个或多个键的值|
|hmset|hmset key-name key value [key value], 为散列里面的一个或多个键设置值|
|hdel|hdel key-name key [key ...], 删除散列的一个或多个键值对, 返回成功找到并删除的键值对数量|
|hlen|hlen key-name, 返回散列的键值对数量|
|kexists|hexists key-name key, 检查给定键是否存在散列中|
|hkeys|hkeys key-name, 获取散列包含的所有键|
|hvals|hvals key-nam, 获取散列包含的所有值|
|hgetall|hgetall key-name, 获取散列包含的所有键值对|
|hincrby|hincby key-name key increment, 将键key存储的值加上整数increment|
|hincbyfloat|hincbyfloat key-name key increment, 将键key存储的值加上浮点数increment|

```bash
127.0.0.1:6379> hmset hask-key3 short hello long 1000*1
OK
127.0.0.1:6379> hkeys hask-key3
1) "short"
2) "long"
127.0.0.1:6379> hexists hask-key2 num
(integer) 0
127.0.0.1:6379> hincrby hask-key2 num  1
(integer) 1
127.0.0.1:6379> hexists hask-key2 num
(integer) 1
```

## 3.5 有序集合

```bash
127.0.0.1:6379> zadd zset-key2 3 'a' 2 'b' 1 'c'
(integer) 3
127.0.0.1:6379> zcard zset-key2
(integer) 3
127.0.0.1:6379> zincrby zset-key2 3 'c'
"4"
127.0.0.1:6379> zscore zset-key2 b
"2"
127.0.0.1:6379> zrank zset-key2 'c'
(integer) 2
127.0.0.1:6379> zcount zset-key2 0 3
(integer) 2
127.0.0.1:6379> zrem zset-key2 'b'
(integer) 1
127.0.0.1:6379> zrange zset-key2 0 -1 withscores
1) "a"
2) "3"
3) "c"
4) "4"
```

```bash
127.0.0.1:6379> zadd zset2-1  1 'a' 2 'b' 3 'c'
(integer) 3
127.0.0.1:6379> zadd zset2-2 4 'b' 1 'c' 0 'd'
(integer) 3
127.0.0.1:6379>  zinterstore z-set2-i 2 zset2-1 zset2-2
(integer) 2
127.0.0.1:6379> zrange z-set2-i 0 -1 withscores
1) "c"
2) "4"
3) "b"
4) "6"
127.0.0.1:6379> zunionstore z-set2-u 2 zset2-1 zset2-2 aggregate min
(integer) 4
127.0.0.1:6379> zrange z-set2-u 0 -1 withscores
1) "d"
2) "0"
3) "a"
4) "1"
5) "c"
6) "1"
7) "b"
8) "2"
127.0.0.1:6379> sadd set2-1 'a' 'b'
(integer) 2
127.0.0.1:6379> zunionstore z-set2-u2 3 zset2-1 zset2-2 set2-1
(integer) 4
127.0.0.1:6379> zrange z-set2-u2 0 -1 withscores
1) "d"
2) "0"
3) "a"
4) "2"
5) "c"
6) "4"
7) "b"
8) "7"
```

## 3.6 发布与订阅

不太建议使用redis的发布与订阅

## 3.7 其他命令

### 3.7.2 键的过期时间

|命令|示例与描述|
|---|---|
|persist|persist key-name, 移除键的过期时间|
|ttl|ttl key-name, 查看键距离过期还有多少秒|
|expire|exprie key-name seconds, 让给定键在指定的秒数后过期|
|expripeat|expripeat key-name timestamp, 将给定键的过期时间设置为给定的unix时间戳|
|pttl|pttl key-name, 给定键距离过期还有多少毫秒|
|pexpire|pexpire key-name millseconds, 设置x毫秒后过期|
|pexpireat|pexpireat key-name timestamp-milliseconds, 指定毫秒级的unix时间戳过期

```bash
127.0.0.1:6379> set key value
OK
127.0.0.1:6379> get key
"value"
127.0.0.1:6379> expire key 2
(integer) 1
127.0.0.1:6379> get key
(nil)
127.0.0.1:6379> set key value2
OK
127.0.0.1:6379> expire key 100
(integer) 1
127.0.0.1:6379> ttl key
(integer) 97
```

# 4. 数据安全与性能保障

## 4.1 持久化选项

- 快照: 存储某一时刻的所有数据写到硬盘里
- 只追加文件: 在执行命令时, 将被执行的写命令复制到磁盘里

可以混合使用这两种持久化方式, 也可以单独使用

查看save命令

```bash
127.0.0.1:6379> config get save
1) "save"
2) "3600 1 300 100 60 10000"
```

表示

- 3600s内至少有1个key发生变化
- 300s内至少有100个key发生变化
- 60s内至少有10000个key发生变化

都会触发bgsave

客户端也主动发送bgsave, 来让redis在后台进行持久化, 而save是会阻塞redis的

### 4.1.2 AOF持久化

appendfsync的配置决定AOF的记录时机

- always: 每次写命令都写入磁盘, 会非常降低性能
- everysec: 每秒执行一次写
- no: 由操作系统决定何时写

# 6 使用Redis构建应用程序组件

setnx: 只会在键不存在的情况下为键设置值
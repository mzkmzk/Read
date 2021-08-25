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
|SADD|将给定元素添加到集合|
|SMEMBERS|返回集合包含的所在元素|
|SISMEMBER|检查给定元素是否存在于集合中|
|SREM|如果给定的元素存在于集合中, 那么移除这个元素|
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
|HSET|在散列了里面关联起给定的键值对|
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
|ZADD|将一个带有给定分值的成员添加到有序集合里面|
|ZRANGE|根据元素在有序排列中所处的位置, 从有序集合了里面获取多个元素|
|ZRANGEBYSCORE|获取有序集合中给定分值范围的所有元素|
|ZREM|如果给定成员存在于有序集合中,1 那么移除该元素|


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
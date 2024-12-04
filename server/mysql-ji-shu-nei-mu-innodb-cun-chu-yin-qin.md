# MySQL技术内幕InnoDB存储引擎

# 总结

本书较通俗易懂的讲述了mysql的特性, 评分4星

# 1. MySQL体系结构和存储引擎

## 1.3 MySQL存储引擎

存储引擎是基于表的, 而不是数据库

# 2. InnoDB存储引擎

## 2.6 InnoDB关键特性

### 2.6.1 插入缓存

辅助索引不能是唯一的, 因为在插入缓存时, 数据库并不回去查找索引页来判断插入的记录的唯一性

如果去查找肯定又会有离散读取的情况发生, 从而导致Insert Buffer失去了意义

# 3. 文件

1. 参数文件: mysql启动的配置文件
2. 日志文件: 错误日志, 二进制日志文件, 慢查询日志文件, 查询日志文件等
3. socket文件: 用unix域嵌套字方式进行连接时需要的文件
4. pid文件: mysql实例的进程id文件
5. mysql表结构文件: 用来存放mysql表结构定义文件
6. 存储引擎文件: 存储记录和索引的文件

## 3.1 参数文件

- 启动时参数指定参数文件

查询方式:

```bash
# 根据端口查询占用的pid
[root@x aaa]# lsof -i:3308
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  2924 mysql   13u  IPv6  18684      0t0  TCP *:tns-server (LISTEN)

# 根据pid=2924查询启动命令
[root@aaa 2924]# cat /proc/2924/cmdline
/usr/local/mysql-5.7.17/bin/mysqld--defaults-file=/etc/my.cnf.3308
```

- 查看mysql的默认查找参数日志顺序

```bash
[root@aaa bin]# ./mysql --help | grep my.cnf
                      order of preference, my.cnf, $MYSQL_TCP_PORT,
/etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf
```

- 无配置文件找到

无找到时, 会按mysql的默认配置启动

## 3.2 日志文件

### 3.2.1 错误日志

```bash
mysql> show variables like 'log_error';
+---------------+--------------------------------------------+
| Variable_name | Value                                      |
+---------------+--------------------------------------------+
| log_error     | /usr/local/mysql/data_3307/st01023vm45.err |
+---------------+--------------------------------------------+
1 row in set (0.00 sec)
```

### 3.2.2 慢查询日志

查询慢日志相关开关配置
```bash
# 查询是否开启慢日志记录
mysql> show variables like 'log_slow_queries';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| log_slow_queries | ON    |
+------------------+-------+
1 row in set (0.00 sec)

# 查询慢日志的耗时阈值, 大于该值输出慢日志
mysql> show variables like 'long_query_time';,
+-----------------+----------+
| Variable_name   | Value    |
+-----------------+----------+
| long_query_time | 1.000000 |
+-----------------+----------+

# 查询未使用索引的日志
mysql> show variables like 'log_queries_not_using_indexes';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| log_queries_not_using_indexes | OFF   |
+-------------------------------+-------+

# 每分钟允许未使用索引的日志写入文件个最大次数
mysql> show variables like 'log_throttle_queries_not_using_indexes';
+----------------------------------------+-------+
| Variable_name                          | Value |
+----------------------------------------+-------+
| log_throttle_queries_not_using_indexes | 0     |
+----------------------------------------+-------+
```
查询慢日志路径

```bash
mysql> show variables like 'slow_query_log_file';
+---------------------+------------------+
| Variable_name       | Value            |
+---------------------+------------------+
| slow_query_log_file | slow_queries.log |
+---------------------+------------------+

mysql> show variables like 'datadir';
+---------------+-----------------------------+
| Variable_name | Value                       |
+---------------+-----------------------------+
| datadir       | /usr/local/mysql/data_3307/ |
+---------------+-----------------------------+

[root@st01023vm45 data_3307]# cat /usr/local/mysql/data_3307/slow_queries.log

# User@Host: root[root] @  [172.30.43.69]
# Query_time: 7.214444  Lock_time: 0.001474 Rows_sent: 0  Rows_examined: 728249
SET timestamp=1688711829;
UPDATE `component_entry_configs` SET `deleted_at`='2023-07-07 14:36:46.131707' WHERE key_name in ('cdtid9tqdmgdm34nng50','catbmmp0ep6np4pt61n0','c6559fqqj8qtlq7ep2h0','cdqvd8118hu96ddfnb80','ccmjau1inmkroeob6h20','cd8dbj9inmkmskps1jdg','cdq4rj118hu96ddfn6j0','cdupjkpr0p470mov751g','ce8d97ih6lsdjo3aali0','c5a2f22qj8qoh2hgu3bg','cd8obgpinmkmskps9os0','chjlhbcsqei01766houg','ca2ds7iqj8qpmbs4qda0','cac7nf2qj8qqnksl7uog','cfpir2hr0p4c18b4d7s0','cdtid9tqdmgdm34nng6g','c6fl8jaqj8qtfpahtln0','cdtf8elqdmgdm34nn2n0','ccq19ae25sus2gs6hjm0'
```

### 3.2.3 查询日志


```bash
mysql> SHOW VARIABLES LIKE 'general_log';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| general_log   | OFF   |
+---------------+-------+
1 row in set (0.00 sec)
```

### 3.2.4 二进制日志

二进制日志记录了对mysql数据执行更改的所有操作

查看二进制文件路径

```bash
# 查询bin_log 是否开启
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
1 row in set (0.00 sec)

# 查询datadir bin_log就在这
mysql> show variables like 'datadir';
+---------------+-----------------------------+
| Variable_name | Value                       |
+---------------+-----------------------------+
| datadir       | /usr/local/mysql/data_3307/ |
+---------------+-----------------------------+

# 查看bin-log文件
mysql> SHOW BINARY LOGS;
+------------------+------------+
| Log_name         | File_size  |
+------------------+------------+
| mysql-bin.000233 | 1073745120 |
| mysql-bin.000234 | 1073742062 |
| mysql-bin.000235 |  152539169 |
+------------------+------------+

#查看binlog当前位点
mysql> show master status \G;
*************************** 1. row ***************************
            File: mysql-bin.000235
        Position: 159895850
    Binlog_Do_DB:
Binlog_Ignore_DB:
1 row in set (0.00 sec)


# 查看bin_log文件, 文件名默认为主机名, 可以通过配置参数log-bin=[name]制定
-rw-rw----  1 mysql mysql 1073745604 2024-08-20 11:13:41 mysql-bin.000231
-rw-rw----  1 mysql mysql  353864634 2024-09-13 03:28:59 mysql-bin.000232
-rw-rw----. 1 mysql mysql         38 2024-08-20 11:13:41 mysql-bin.index

# 查看binlog索引文件
[root@st01023vm45 data_3307]# cat  mysql-bin.index;
./mysql-bin.000231
./mysql-bin.000232
```

以下几个参数与binlog文件有关

|  参数   | 作用     |  查询方式 |
| ---- | ---- | ---- |
| max_binlog_size       | 单个二进制日志文件的最大值, 默认为1G | SHOW VARIABLES LIKE 'max_binlog_size'; |
| binlog_cache_size       | binlog缓存池的大小 | show variables like 'binlog_cache_size';|
| sync_binlog       | 二进制日志文件写入缓冲池的频率, 可以为0, 1, 大于1的整数 | show variables like 'sync_binlog'; |
| binlog-do-db       | /usr/local/mysql/data_3307/ |
| binlog-ignore-db       | /usr/local/mysql/data_3307/ |
| log-slave-update       | /usr/local/mysql/data_3307/ |
| binlog-format      | /usr/local/mysql/data_3307/ |

- binlog_cache_size的写入规则

默认为32K, 是每个会话创建一个事务时, mysql会自动分配一个binlog_cache_size的缓存, 所以不能设置过大.

也不能设置过小, 因为会频繁写入二进制临时文件

![binlog_cache_size写入流程](binlog_cache_size写入流程.png)

```bash
mysql> show variables like 'binlog_cache_size';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| binlog_cache_size | 32768 |
+-------------------+-------+

# 查看binlog_cache_use和disk_use的次数, 注意这里是次数, 不是cache空间的大小
mysql> show global status like 'binlog_cache%';
+-----------------------+--------+
| Variable_name         | Value  |
+-----------------------+--------+
| Binlog_cache_disk_use | 5741   |
| Binlog_cache_use      | 458770 |
+-----------------------+--------+
```
当Binlog_cache_disk_use出现比较多次时,就应该考虑增加binlog_cache_size

- sync_binlog

sync_binlog=1为采用同步写磁盘的方式写二进制

当is_innodb_support_xa为0时, binlog和redo log互不影响, 会出现主从数据不一致的情况

![当is_innodb_support_xa为0时](QQ_1728871978055.png)

{% details "代码展开" %}
```bash
flowchart LR
  创建事务 --> 执行update-sql
  执行update-sql --> 
  is_innodb_support_xa{是否开启innodb_support_xa}

  subgraph writeLog[write log并行]
  direction LR
  redoLog[write redo log] 
  binLog[write bin log]
  end

  is_innodb_support_xa -->|否| writeLog
  
```
{% enddetails %}

当is_innodb_support_xa为1时, 当写入binlog后, mysql重启会检查prepare的事务是否已提交了binlog, 提交了的话, prepare的会执行commit环节

![当is_innodb_support_xa为1时](QQ_1728872137326.png)

{% details "代码展开" %}
```bash
@startuml
|innodb_server|
start

:创建事务;
:执行update-sql;
if (是否开启innodb_support_xa?) is (是) then
:write redo log, prepare阶段;
:提交事务;
:写binlog;
|机器|
:故障重启;
|innodb_server|
:检查prepare的redo log完成了binlog提交;
:提交prepare阶段的redo log;
stop;
|innodb_server|
else
:此处省略...;
detach
@enduml
```
{% enddetails %}

[参考链接](https://github.com/mao888/golang-guide/blob/main/mysql/MYSQL%E7%9F%A5%E8%AF%86%E7%82%B9%E6%95%B4%E7%90%86.md#mysql%E7%9A%84binlog%E6%9C%89%E5%87%A0%E7%A7%8D%E5%BD%95%E5%85%A5%E6%A0%BC%E5%BC%8F%E5%88%86%E5%88%AB%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)




- binlog-format

查询binlog格式

```bash
mysql> select @@session.binlog_format;
+-------------------------+
| @@session.binlog_format |
+-------------------------+
| ROW                     |
+-------------------------+
1 row in set (0.01 sec)
```

更改binlog格式

```bash
mysql>set global binlog_format='ROW'
```

格式有三种

1. Row
2. Statement
3. MIXED

设置了binlog_format为ROW, 则可以将innodb事务隔离级别设置为READ COMMTTED, 以获得更好的并发性

MIXED模式下, 默认情况下采取STATEMENT格式存储二进制日志, 但是有些情况会使用ROW格式

  1. 使用了uuid()、user()等不确定函数
  2. 使用了临时表



# 4 表

主要讲述和表在磁盘的存储结构.

## 4.1 索引组织表

innodb中, 表是根据主键顺序存放的, 如果表没有主键, 会按以下方式选择主键

1. 首先选择表中第一个有唯一索引的列(顺序为定义索引的顺序、而不是建表时列的顺序), 如果有则该列为主键
2. 如果没有, 则自动创建一个6字节的指针

## 4.2 Innodb逻辑存储结构

表空间由段、区、页组成

# 5. 索引算法

## 5.2 数据结构和算法

二叉查找树: 左子树的键值必须比根节点的键值小, 而右子树必须比根节点的键值大

平衡二叉树: 符合二叉查找树的定义, 并且任何节点的两科子树高度最大差为1

## 5.2 B+数

基本概念

| 概念     | 说明                                 |
| -------- | ------------------------------------ |
| 最大度数 | 每个非叶子节点最多可以拥有的子节点数 |



### 5.3.1 B+树的插入操作

|Leaf Page满| Index Page满| 操作 |
|---|---|---|
|No|No| 直接插入到叶子节点 |
|Yes|No| 1. 拆分LeafPage<br>2. 将中间的节点放入到IndexPage中<br/>3. 小于中间节点的放左边<br/>4. 大于或等于中间节点的放右边 |
|Yes| Yes | 1. 拆分LeafePage<br/>2. 小于中间节点的放左边<br/>3. 大于或等于中间节点的放右边<br/>4. 拆分IndexPage<br/>5. 小于中间节点的放左边<br/>6. 大于或等于中间节点的放右边<br/>7. 将中间的节点放入到上层IndexPage中 |
|| | |

# 7. 事务

事务是数据库区别于文件系统的重要特性

Innodb的存储引擎的事务完全符合ACID特性

1. 原子性
2. 一致性
3. 隔离性
4. 持久性
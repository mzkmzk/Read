# MySQL排错指南

# 总结

前面第一二章排查性能和锁的问题很不错,后面其实讲比较深的东西,都一笔带过, 收获比较少, 总体3.5分

# 1. 基础

## 1.1 语法错误

mysql可临时打开查询日志记录

查看日志情况

```bash
show variables;
```

|Variable_name|Value|
|---|---|
|general_log|OFF|
|general_log_file|/usr/local/mysql/data_3307/xxx.log|
|log_output|FILE|

开启日志记录

```bash
set GLOBAL general_log='on'
```

log_output可以设置为`table`|`file`

使用file的话就是简单的sql记录, table则有调用者ip等一些信息

```bash
set GLOBAL general_log='on';
select * from mysql.general_log\G
```

```
*************************** 1043. row ***************************
  event_time: 2021-06-30 21:47:06
   user_host: root[root] @  [xx.30.30.118]
   thread_id: 260478
   server_id: 13307
command_type: Query
    argument: select * from mysql.general_log
```

调试完毕后建议将日志输出关闭

```bash
set GLOBAL general_log='off';
```

## 1.2 SELECT返回错误结果

- 如果一个SELECT查询没有按预期返回结果, 那么可以拆成一小段, 逐段分析
- 使用 EXPLANIN EXTENDED, 然后SHOW WARNINGS命令去获取查询执行计划及其实际运行方式的相关信息

## 1.3 当错误可能由之前的更新引起时

通常情况下, 你应该始终检查语句执行的返回信息, 从而了解有多少行数据受到了影响且他们的值是否与你预期的一致

在应用程序中, 你必须明确检查信息功能

# 2. 你不孤单: 并发问题

## 2.1 锁和事务

当线程请求数据集的时候就会加锁, 这可以是表、行、页、元数据. 当线程结束处理特定的数据集之后, 它就会释放锁

事务的隔离等级控制其他并发操作中的变化对本事务是否可见

读锁(也叫共享锁)允许并发线程读取加锁的数据, 但禁止写数据

写锁(排他锁)阻止其他线程的读写操作

元数据是DDL(如CREATE、DROP、ALTER等修改语句)

show engine innodb status能看到innodb的当前一些锁的情况

当使用行锁时, 索引能提高性能, 特别是唯一索引

## 2.2 锁

mysql有4种类型的锁:表锁、行锁、页锁、元数据锁(create, drop, alter)

如果是写锁, 读和写都是禁止的

读锁则是禁止写

测试表结构

```bash
mysql> show create table 2_2_1_t ;
+---------+-----------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                      |
+---------+-----------------------------------------------------------------------------------------------------------------------------------+
| 2_2_1_t | CREATE TABLE `2_2_1_t` (
  `a` int(10) unsigned NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+---------+-----------------------------------------------------------------------------------------------------------------------------------+
```

插入数据

```bash
mysql> insert into 2_2_1_t (`a`) values(1);
Query OK, 1 row affected (0.02 sec)

mysql> insert into 2_2_1_t (`a`) values(256);
Query OK, 1 row affected (0.01 sec)
```

### 2.2.2 行锁

创建表

```bash
mysql> CREATE TABLE `2_2_2_t` (
  `a` int(10) unsigned NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`a`)
) ENGINE=innodb DEFAULT CHARSET=utf8
```

构造数据

```bash
mysql> insert into 2_2_2_t values();
Query OK, 1 row affected (0.02 sec)

mysql> insert into 2_2_2_t select null from 2_2_2_t;
Query OK, 1 row affected (0.02 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> insert into 2_2_2_t select null from 2_2_2_t;
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> insert into 2_2_2_t select null from 2_2_2_t;
Query OK, 4 rows affected (0.02 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> insert into 2_2_2_t select null from 2_2_2_t;
Query OK, 8 rows affected (0.02 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> select * from 2_2_2_t ;
+----+
| a  |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  6 |
|  7 |
|  8 |
|  9 |
| 13 |
| 14 |
| 15 |
| 16 |
| 17 |
| 18 |
| 19 |
| 20 |
+----+
16 rows in set (0.02 sec)
```

模拟操作

```bash
mysql> update 2_2_2_t set a=sleep(100) where a = 6;
Query OK, 1 row affected (1 min 40.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

# 另外开窗口同时执行
mysql> update 2_2_2_t set a = 21 where a= 6;
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction

mysql> show variables like 'innodb_lock_wait_timeout';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 50    |
+--------------------------+-------+
1 row in set (0.02 sec)
```
并行改的话,会直到`innodb_lock_wait_timeout`设置的超时时间而报错

换个问题, 假设第二个update在超时前就拿到锁了,会修改成功吗

```bash
mysql> update 2_2_2_t set a=sleep(49) where a = 0;
Query OK, 0 rows affected (49.01 sec)
Rows matched: 1  Changed: 0  Warnings: 0

# 另外开窗口同时执行
mysql> update 2_2_2_t set a = 21 where a= 0;
Query OK, 1 row affected (38.90 sec)
Rows matched: 1  Changed: 1  Warnings: 0

# 结果
mysql> select * from 2_2_2_t;
+----+
| a  |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  7 |
|  8 |
|  9 |
| 13 |
| 14 |
| 15 |
| 16 |
| 17 |
| 18 |
| 19 |
| 20 |
| 21 |
+----+
```
可以看到原本a=0的那行还是被改到了21, 其实想想还挺后怕的

假设线上mysql的cpu负债过高, 并行修改的sql, 有些update因为没超时而改了, 有些update因为超时被丢弃了

这样的数据就是错乱的了

为了确定innodb的一个请求是否阻塞, 我们可以使用`show engine innodb status`

里面会有关键信息

```bash
---TRANSACTION 17395525, ACTIVE 16 sec
mysql tables in use 1, locked 1
2 lock struct(s), heap size 1136, 1 row lock(s)
MySQL thread id 558581, OS thread handle 139847279269632, query id 298277505 10.10.45.12 root1 User sleep
update 2_2_2_t set a=sleep(100) where a = 21
```

## 2.3 事务

### 2.3.1 隐藏查询

在事务中, 锁住了行, 但是在`show processlist`不会显示

```bash
mysql> begin;
Query OK, 0 rows affected (0.01 sec)

mysql> update 2_2_2_t set a=21 where a= 0;
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

# 新开窗口执行
mysql> begin;
Query OK, 0 rows affected (0.02 sec)

mysql> update 2_2_2_t set a =22 where a=0;
```
查询 processlist 只有后面等锁的update, 没有拿到锁的update 
```bash
mysql> show processlist \G;
*************************** 18. row ***************************
     Id: 561496
   User: root1
   Host: 10.10.45.12:44073
     db: mzk_test
Command: Query
   Time: 18
  State: updating
   Info: update 2_2_2_t set a =22 where a=0


```

而查询`show engine innodb status\G;`能看到等待锁的情况, 但仍然不知道拿到锁的命令, 只能得到等待锁的命令

```bash
mysql> show engine innodb status\G;
...
---TRANSACTION 17419442, ACTIVE 779 sec starting index read
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 1136, 4 row lock(s)
MySQL thread id 561496, OS thread handle 139847951447808, query id 298793550 10.10.45.12 root1 updating
update 2_2_2_t set a =22 where a=0
------- TRX HAS BEEN WAITING 3 SEC FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 4997 page no 3 n bits 88 index PRIMARY of table `mzk_test`.`2_2_2_t` trx id 17419442 lock_mode X locks rec but not gap waiting
Record lock, heap no 18 PHYSICAL RECORD: n_fields 3; compact format; info bits 32
 0: len 4; hex 00000000; asc     ;;
 1: len 6; hex 00000109cca7; asc       ;;
 2: len 7; hex 66000006be0110; asc f      ;;

------------------
---TRANSACTION 17419431, ACTIVE 817 sec
2 lock struct(s), heap size 1136, 1 row lock(s), undo log entries 2
MySQL thread id 561495, OS thread handle 139847279269632, query id 298788536 10.10.45.12 root1
--------
```


还有个工具查看锁就是` select * from information_schema.innodb_locks\G;`, 但只能得到拿到锁和等待锁具体的table, 无法知道具体命令

```bash
mysql> select * from information_schema.innodb_locks\G;
*************************** 1. row ***************************
    lock_id: 17419442:4997:3:18
lock_trx_id: 17419442
  lock_mode: X
  lock_type: RECORD
 lock_table: `mzk_test`.`2_2_2_t`
 lock_index: PRIMARY
 lock_space: 4997
  lock_page: 3
   lock_rec: 18
  lock_data: 0
*************************** 2. row ***************************
    lock_id: 17419431:4997:3:18
lock_trx_id: 17419431
  lock_mode: X
  lock_type: RECORD
 lock_table: `mzk_test`.`2_2_2_t`
 lock_index: PRIMARY
 lock_space: 4997
  lock_page: 3
   lock_rec: 18
  lock_data: 0
2 rows in set, 1 warning (0.02 sec)
```

那么如何得到具体拿住锁的命令呢?

通过`select * from information_schema.innodb_lock_waits`拿到具体持锁进程id
```bash
mysql> select * from information_schema.innodb_lock_waits\G;
*************************** 1. row ***************************
requesting_trx_id: 17419442 # 正在挂起的事务id
requested_lock_id: 17419442:4997:3:18 # 正在挂起的id
  blocking_trx_id: 17419431 # 正在持有锁的事务id
 blocking_lock_id: 17419431:4997:3:18 # 正在持有锁的id
1 row in set, 1 warning (0.01 sec)
```

然后我们查看具体的事务在干嘛`select * from information_schema.innodb_trx\G;`

```bash
mysql> select * from information_schema.innodb_trx\G;
*************************** 1. row ***************************
                    trx_id: 17419442
                 trx_state: LOCK WAIT
               trx_started: 2024-03-13 09:18:03
     trx_requested_lock_id: 17419442:4997:3:18
          trx_wait_started: 2024-03-13 09:45:27
                trx_weight: 2
       trx_mysql_thread_id: 561496
                 trx_query: update 2_2_2_t set a =22 where a=0
       trx_operation_state: starting index read
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 7
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
*************************** 2. row ***************************
                    trx_id: 17419431
                 trx_state: RUNNING
               trx_started: 2024-03-13 09:17:25
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 4
       trx_mysql_thread_id: 561495
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 2
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
2 rows in set (0.02 sec)
```
具体拿到持有锁的id`trx_mysql_thread_id`: 561495

通过`show processlist` 来查看具体的连接, 是否kill掉

```bash
mysql> show processlist \G;
*************************** 16. row ***************************
     Id: 561495
   User: root1
   Host: 10.10.45.12:44004
     db: mzk_test
Command: Sleep
   Time: 2144
  State:
   Info: NULL
```

### 2.3.2 死锁

表结构

```bash
CREATE TABLE `2_2_3_t_1` (
  `a` int(10) unsigned NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`a`)
) ENGINE=innodb DEFAULT CHARSET=utf8;
```

构建数据

```bash
mysql> insert into 2_2_3_t_1 values();
Query OK, 1 row affected (0.02 sec)

mysql> insert into 2_2_3_t_1 select null from 2_2_3_t_1 ;
Query OK, 1 row affected (0.02 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> insert into 2_2_3_t_1 select null from 2_2_3_t_1 ;
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from 2_2_3_t_1 ;
+---+
| a |
+---+
| 1 |
| 2 |
| 3 |
| 4 |
+---+
```

同时打开两个事务, 新insert

```bash
# 事务1
mysql> begin;
mysql> select * from 2_2_3_t_1;
+----+
| a  |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  8 |
| 11 |
+----+
6 rows in set (0.02 sec)

mysql> insert into 2_2_3_t_1 values();
Query OK, 1 row affected (0.02 sec)

mysql> select * from 2_2_3_t_1;
+----+
| a  |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  8 |
| 11 |
| 12 |
+----+
7 rows in set (0.02 sec)
```

并行执行事务2
```bash
mysql> begin;
mysql> insert into 2_2_3_t_1 values();
Query OK, 1 row affected (0.02 sec)

mysql> select * from 2_2_3_t_1;
+----+
| a  |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  8 |
| 11 |
| 13 |
+----+
7 rows in set (0.02 sec)
```

执行修改

```bash
# 在事务1中执行
mysql> update 2_2_3_t_1 set a=13 where a=12;
# 一开始会卡住, 直到事务2执行update
Query OK, 1 row affected (17.88 sec)
Rows matched: 1  Changed: 1  Warnings: 0

# 在事务2中执行
mysql> update 2_2_3_t_1 set a=12 where a=13;
ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction
```

我们可以通过`show engine innodb status`查找死锁, 会输出最近的死锁信息

`WAITING FOR THIS LOCK TO BE GRANTED`代表等待锁的信息

`HOLDS THE LOCK`代表持有锁的信息

```bash
------------------------
LATEST DETECTED DEADLOCK
------------------------
2024-03-14 20:55:48 0x7f30c4b27700
*** (1) TRANSACTION:
TRANSACTION 17463373, ACTIVE 64 sec updating or deleting
mysql tables in use 1, locked 1
LOCK WAIT 3 lock struct(s), heap size 1136, 2 row lock(s), undo log entries 2
MySQL thread id 565780, OS thread handle 139847951447808, query id 299564379 10.10.45.12 root1 updating
update 2_2_3_t_1 set a=13 where a=12
*** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 5260 page no 3 n bits 80 index PRIMARY of table `mzk_test`.`2_2_3_t_1` trx id 17463373 lock mode S locks rec but not gap waiting
Record lock, heap no 9 PHYSICAL RECORD: n_fields 3; compact format; info bits 32
 0: len 4; hex 0000000d; asc     ;;
 1: len 6; hex 0000010a7847; asc     xG;;
 2: len 7; hex 330000085b12a7; asc 3   [  ;;

*** (2) TRANSACTION:
TRANSACTION 17463367, ACTIVE 67 sec updating or deleting
mysql tables in use 1, locked 1
3 lock struct(s), heap size 1136, 2 row lock(s), undo log entries 2
MySQL thread id 565787, OS thread handle 139847435187968, query id 299564496 10.10.45.12 root1 updating
update 2_2_3_t_1 set a=12 where a=13
*** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 5260 page no 3 n bits 80 index PRIMARY of table `mzk_test`.`2_2_3_t_1` trx id 17463367 lock_mode X locks rec but not gap
Record lock, heap no 9 PHYSICAL RECORD: n_fields 3; compact format; info bits 32
 0: len 4; hex 0000000d; asc     ;;
 1: len 6; hex 0000010a7847; asc     xG;;
 2: len 7; hex 330000085b12a7; asc 3   [  ;;

*** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 5260 page no 3 n bits 80 index PRIMARY of table `mzk_test`.`2_2_3_t_1` trx id 17463367 lock mode S locks rec but not gap waiting
Record lock, heap no 6 PHYSICAL RECORD: n_fields 3; compact format; info bits 32
 0: len 4; hex 0000000c; asc     ;;
 1: len 6; hex 0000010a784d; asc     xM;;
 2: len 7; hex 37000005eb2c08; asc 7    , ;;

```

--疑问--: 事务A获得锁后, 不退出,其他都写不了

https://xiaolincoding.com/mysql/lock/mysql_lock.html#next-key-lock

### 2.3.3 隐式提交

DDL语句与事务相关的语句和管理语句都会产生隐式提交

```bash
mysql> create table 2_3_3_t_1(f1 int) engine=InooDB;
Query OK, 0 rows affected, 2 warnings (0.02 sec)

mysql> begin;
Query OK, 0 rows affected (0.02 sec)

mysql> insert into 2_3_3_t_1 values(100);
Query OK, 1 row affected (0.02 sec)

mysql> create table 2_3_3_t_2 like 2_3_3_t_1;
Query OK, 0 rows affected (0.02 sec)

mysql> insert into 2_3_3_t_1 values(200);
Query OK, 1 row affected (0.02 sec)

mysql> rollback;
Query OK, 0 rows affected (0.02 sec)

mysql> select * from 2_3_3_t_1 ;
+------+
| f1   |
+------+
|  100 |
|  200 |
+------+
```

因为create table 就把事务提交了

## 2.4 元数据锁

在其他事务使用表时, 对该表的DDL操作应该阻塞

| 线程 1                  | 线程 2                                |
| ----------------------- | ------------------------------------- |
| begin                   |                                       |
| select * from 2_3_3_t_1 |                                       |
|                         | drop table 2_3_3_t_1                  |
| rollback                |                                       |
|                         | Query OK, 0 rows affected (12.93 sec) |
|                         |                                       |


## 2.6 其他锁问题

```bash
# 查看冲突
mysql> select * from performance_schema.mutex_instances where locked_by_thread_id is not null ;

mysql> show create table performance_schema.mutex_instances \G;
*************************** 1. row ***************************
       Table: mutex_instances
Create Table: CREATE TABLE `mutex_instances` (
  `NAME` varchar(128) NOT NULL,
  `OBJECT_INSTANCE_BEGIN` bigint(20) unsigned NOT NULL,
  `LOCKED_BY_THREAD_ID` bigint(20) unsigned DEFAULT NULL
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8
1 row in set (0.02 sec)

```
这里书中没有给实际案例, 这里也简单略过


## 2.5 并发如何影响性能

### 2.5.2 为并发问题监控其他资源

定时重复执行select ... from informantion_schema.processlist命令是挺好的选择

## 2.7 复制和并发

### 2.7.1 基于语句的复制问题

复制模式有Now()之类的函数, 语句复制就会有问题

每个事务都只会在其提交时, 向二进制日志写入数据

### 2.7.2 混合事务和非事务性表

不要把事务表和非事务表放在一个事务里(innoDB表和MyISAM表), 会造成主从复制不一致性

## 2.8 高效地使用MySQL问题排查工具

### 2.8.3 information_schema中的表

- innodb_trx: 当前运行的所有事务的表
- innodb_locks: 包含事务持有的当前锁的相关信息以及每个事务等待的锁的信息
- innodb_lock_wats: 包含事务正在等待的锁的信息

阻塞的事务列表

```bash
mysql > select * from innodb_locks where lock_trx_id in (select blocking_trx_id from innodb_lock_waits)
```

# 3. 配置选型对服务器的影响

# 6. 问题排查技术与工具

## 6.9 专用的测试工具

### 6.9.1 基准工具

mysqlslap 模拟负载客户端
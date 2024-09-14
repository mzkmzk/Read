# 高性能MySQL

# 5. 创建搞性能的索引

## 5.1 索引基础

### 5.1.1 索引的类型

在MySQL中,索引是在存储引擎层而不是服务层实现的

MySQL支持的索引类型

> 1. B-Tree索引

B-Tree: 即每一个叶子节点都包含指向下一个叶子节点的指针,从而方便子节点的范围遍历



## 7.10 全文索引

全文索引场景: 希望通过关键字的匹配来进行查询过滤,那么久需要基于相似度的查询

搜索引擎需要全文索引,搜索引擎一般背后都不是关系型数据库,但是全文索引的基本原理都是一样的

# 10. 复制

## 10.1 复制概述

复制通常不会增加主库的开销, 主要是启用二进制日志带来的开销, 每个备库也会对主库增加网络I/O开销

### 10.1.2 复制如何工作

- 主库上把数据更改记录到二进制日志(binary Log)
- 备库将主库上的日志复制到自己的中继日志(Relay Log)
- 备库读取中继日志中的事件, 将其重放到备库数据之上

> 主库记录二进制日志

- 每次准备提交事务完成数据更新前, 主库先把数据更新的事件记录到二进制日志
- 记录二进制日志后, 主库会告诉存储引擎可以提交事务了

> 备库同步主库log

- 备库启一个工作线程(I/O线程), I/O线程跟主库建立一个普通的客户端连接
- 主库上启动一个特殊的二进制转储(binlog dump)线程
- 二进制转储线程会读取主库上二进制日志中的事件, 如果该线程追上了主库, 它就会进入睡眠状态, 直到主库发送信号量通知其有新的时机产生才会被唤醒
- 备库I/O会将接受到的事件记录到中继日志中

所以复制过程中会有3个线程

- 主库中一个线程复制写binlog
- 备库中一个线程复制写中继日志
- 备库中一个线程回放中继日志

## 10.2 配置复制

### 10.2.1 创建复制账号

```sql
mysql> grant replication slave, replication client on *.*
 to repl@'127.0.0.%' identified by 'p4ssword';
```

因为我的主备都在同一台机器, 这里ip限定为了本机

在主库和备库都创建该账号

我们把这个账号限制为本地网络

这个账号无法执行select或修改数据, 但仍能从二进制日志获得一些数据

### 10.2.2 配置主库和备库

在主库的my.cnf中添加或修改一下内容

```
log_bin = mysql-bin
server_id = 10
```

必须明确一个唯一的服务器id,  一般采取服务器ip的末8位

如果之前没指定log-bin选项, 增加了log-bin 则需要重启mysql

确认二进制文件是否已经在主库上创建

```bash
mysql> show master status;
+------------------+-----------+--------------+------------------+
| File             | Position  | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+-----------+--------------+------------------+
| mysql-bin.000191 | 461016200 |              |                  |
+------------------+-----------+--------------+------------------+
```

查找`bin-log`的位置

```bash
mysql>  show variables like 'datadir';
+---------------+-----------------------------+
| Variable_name | Value                       |
+---------------+-----------------------------+
| datadir       | /usr/local/mysql/data_3307/ |
+---------------+-----------------------------+
```

文件即为`show master status`的file文件

查看下文件大小

```bash
$ ls -llah mysql-bin.000191
-rw-rw---- 1 mysql mysql 513M 2021-07-10 18:01:01 mysql-bin.000191
```

备库中也需要设置`my.cnf`

```
log_bin = mysql-bin
server_id = 2
relay_log= /var/lib/mysql/mysql-relay-bin
log_slave_updates = 1
read_ony = 1
```
事实上这只有server_id是必要的, 其他(除了read_only)只是显式的列出默认值

log_bin: 为机器命名, 一般建议主库和备库都设置一样的值
relay_log: 指定中继日志的位置和命名
log_slave_updates: 1代表允许备库将其重放的事件也记录到自身的二进制日志中
read_only: 1代表阻止其他没有特权权限的线程修改数据

### 10.2.3 启动复制

告诉备库如何连接到主库并重放其二进制日志

```bash
mysql > mysql > change master to master_host='ip',
  master_port=3306,
  master_user='repl',
  master_password='p4ssword',
  master_log_file='my-bin.000191',
  master_log_pos=0;
```

检查复制是否正确执行

```bash
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 127.0.0.1
                  Master_User: repl
                  Master_Port: 3307
                Connect_Retry: 60
              Master_Log_File: my-bin.000191
          Read_Master_Log_Pos: 4
               Relay_Log_File: xxx-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: my-bin.000191
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 4
              Relay_Log_Space: 107
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
```

Slave_IO_State, Slave_IO_Running, Slave_SQL_Running的值表明 当前备库尚未运行

日志的开头是4而不是0, 因为0不是日志真正开始的地方

执行下面命令开始复制

```bash
mysql > start slave;
```

执行成功的话会显示

```bash
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 127.0.0.1
                  Master_User: repl2
                  Master_Port: 3307
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000191
          Read_Master_Log_Pos: 555300242
               Relay_Log_File: xxx-relay-bin.000005
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000191
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 555300242
              Relay_Log_Space: 559437
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 13307
1 row in set (0.00 sec)
```

在主库上可以看到备库I/O的线程向主库发起的连接

```bash
> show processlist\G;
*************************** 16. row ***************************
     Id: 3943814
   User: repl2
   Host: 127.0.0.1:39052
     db: NULL
Command: Binlog Dump
   Time: 418
  State: Master has sent all binlog to slave; waiting for binlog to be updated
   Info: NULL
```

备库上也可以看到两个线程

```bash
> mysql> show processlist\G;
*************************** 2. row ***************************
     Id: 45
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 650
  State: Waiting for master to send event
   Info: NULL
*************************** 3. row ***************************
     Id: 48
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 23
  State: Slave has read all relay log; waiting for the slave I/O thread to update it
   Info: NULL
```

state为`Slave has read all relay log; waiting for the slave I/O thread to update it`的time代表则多久主库没更新数据了

### 笔者尝试开始备份时的错误

> 如何查看bin-log是哪个server_id产生的

```bash
 > mysqlbinlog mysql-bin.000191 > /tmp/mzk-bin-log.txt // 笔者尝试的生成的文件大小为原bin-log的2倍
 > tail /tmp/mzk-bin-log.txt
 /*!*/;
# at 552513218
#210711  0:35:04 server id 13307  end_log_pos 552513313 	Query	thread_id=3942845	exec_time=0	error_code=0
SET TIMESTAMP=1625934904/*!*/;
COMMIT
/*!*/;
DELIMITER ;
# End of log file
ROLLBACK /* added by mysqlbinlog */;
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
```

这里能看到`server id`为13307

> 用户网络错误

错误提示

```
Last_IO_Errno: 1045
Last_IO_Error: error connecting to master 'repl@127.0.0.1:3307' - retry-time: 60  retries: 86400
```

因为笔者创建的repl账号非127.0.0.1,而是192.178.0.@

> change master配置错误

笔者遇到的错误是

```
Got fatal error 1236 from master when reading data from binary log: 'Could not find first log file name in binary log index file
```

这种错误就是找不到master的bin-log

笔者是因为`master_log_file`设置错误了

> 无法找到表

错误

```bash
Last_SQL_Errno: 1146
Last_SQL_Error: Error 'Table 'database_xxxx.talbe_xxxx' doesn't exist' on query. Default database: 'database_xxxx'. Query: 'UPDATE vip_jinku.talbe_xxxx SET flocktimes=0 WHERE finused=0 AND flocktimes<='1624973701''
```

查看一些文章: https://bobcares.com/blog/mysql-replication-error-1146/

说其实不重要,可以跳过

```bash
mysql> SET GLOBAL SQL_SLAVE_SKIP_COUNTER = 1000;
```

### 10.2.4 从另一个服务器开始复制

前面设置都是主备库刚安装好且都是默认数据

但一般的场景是 一个运行了很久的主库, 然后用同一台新安装的备库与之同步, 此时备库还没数据

有几种办法来初始化备库或从其他服务器克隆数据到备库

需要3个条件让主备库保持同步

- 在某个时间点的主库的数据快照
- 主库当前的二进制文件, 和获取数据快照时在该二进制日志文件中的偏移量, 可以通过`show master status`来获取这些值
- 从快照时间到现在的二进制文件

下面是从别的服务器克隆备库的方法

> 使用mysqldump

```bash
mysqldump --single-transaction --all-databases --master-data=1 --host=server1 | mysql --host=server2
```

选项`--single-transaction`转储的数据为事务开始前的数据。如果是非事务型表, 可以使用`--lock-all-tables`来获得所有表的一致性转储

### 笔者通过mysqldump备份遇到的问题

> 遇到mysql公共表被锁

错误提示: `ERROR 1142 (42000) at line: SELECT,LOCK TABL command denied to user 'root'@'127.0.0.1' for table 'cond_instances'`

网上的说法都是加个`--skip-add-locks`参数 

但是笔者试了不行, 所以笔者通过忽略系统表的做法

```bash
   mysql --host=127.0.0.1 --port=3307 -e 'show databases;'|grep -E -v "Database|information_schema|mysql|test|performance_schema" | xargs  mysqldump --skip-lock-tables --single-transaction    --master-data=1 --host=127.0.0.1 --port=3307 --databases | mysql --host=127.0.0.1 --port=3306
```

### 10.2.5 推荐的复制配置

```
sync_binlog=1
```

开启的话 mysql每次提交事务前都会将二进制日志同步到磁盘上

保证服务器崩溃时不会丢失事件

如果使用`InnoDB` 强烈推荐下吗的设置

```
innodb_flush_log_at_trx_commit=1 flush every log write
innodb_support_xa=ON 
innodb_safe_binlog
```

备库中设定

```
replay_log=/path/to/logs/relay-bin
skip_slave_start
read_only
```

master.info和中继日志文件都不是崩溃安全的, 如果不介意额外的fsync导致的性能开销, 可开启

```
sync_master_info=1
sync_relay_log=1
sync_relay_log_info=1
```

sql线程会重放完一个中继日志中的事件后尽快将其删除(通过relay_log_purge选项控制)

当主库和备库网络延迟非常大时, I/O线程可能会把磁盘撑满, 解决是配置relay_log_space_limit变量

当所有中继日志大小之和大于这个值, I/O线程会停止, 等待SQL线程释放磁盘空间

但这个选项会存在一些BUG, 一般磁盘不吃紧的话 不建议设置`relay_log_space_limit`

## 10.3 复制的原理

### 10.3.1 基于语句的复制

原理: 把主库上执行过的sql再执行一遍

优势:

- 实现简单

劣势:

- 包含元数据信息的sql无法被正确复制, 例如当前时间戳/CURRENT_USER()
- 存储过程/触发器也可能会有问题
- 更新必须是串行的, 这需要更多的锁

### 10.3.2 基于行的复制

原理: 将实际数据记录在二进制日志中

优势: 

- 部分情况的数据会更高效

高效的例如

```bash
mysql > insert into summary_table(col1, col2, sum_col3)
  select col1, col2, sum(col3)
  from enormous_table
  group by col1, col2;
```

这就无需查询, 直接插入行

劣势: 

- 部分情况的数据没有语句复制高效

例如

```bash
mysql > update enormous_table set col1 = 0;
```

因为是全表更新, 会导致每一行数据都会被记录到二进制日志中

由于两种复制各有利弊, mysql支持这两种模式的动态切换, 默认情况下使用语句复制的方式, 但发现语句无法正确被赋值, 就切换到行的复制模式

还可以根据设置回话级别的变量`binlog_format`老控制二进制日志格式

### 10.3.3 基于行复制还是基于语句哪种更优

理论上 基于行的复制整体更优, 但是在编写本书时, 行复制还不是很完善

> 基于语句复制的优点

- 可以修改schema等系统表

> 基于语句复制的缺点

- 存储过程和触发器等局域复制, 在5.0存在大量bug

> 基于行复制的优点

- 几乎没有行复制无法处理的场景, 例如修改schema表

> 基于行复制的缺点

- 无法判断执行了哪些SQL, 像一个黑盒
- 编写本书时, 仍存在挺多bug

### 10.3.5 发送复制事件到其他备库

设定`log_slave_updates`可以让备库变为其他服务器的主库

设置了之后 mysql执行的事件也会记录到它自己的二进制文件中

### 10.3.6复制过滤器

使用`binlog_do_db`和`binlog_ignore_db`来控制

但是过滤是从当前数据库过滤的, 例如

```bash
mysql > use test;
mysql > delete from sakila.film
```

第二个行的操作会被认为是在test库操作的, 而用来作为过滤的判断, 而不是sakila

所以不建议用这两个属性

## 10.4 复制拓扑

基本原则

- 一个mysql备库实例只能有一个主库
- 每个备库必须有一个唯一的服务器ID
- 一个主库可以有多个备库
- 如果打开了`log_slave_updates`选项, 一个备库可以把主库上的数据变化传播到其他备库

### 10.4.2 主动 - 主动模式下的 主-主复制

两个可写的互主服务器导致的问题非常多, 例如

- 两台服务器同时修改一行记录
- 两台服务器同时向一个auto_increment列里插入数据

### 10.4.3 主动 - 被动模式下的主-主复制

其中一台服务器是只读的被动服务器

这么设置

- 确保两套服务器上有相同的数据
- 启动二进制日志, 并选择唯一的服务器ID, 并创建复制账号
- 启用备库更新的日志记录(故障转义和故障恢复的关键)
- 把被动服务器配置为只读(可选), 防止可能与主动服务器上的更新产生冲突
- 启动每个服务器的mysql实例
- 将每个主库设置为对方的备库, 使用新创建的二进制日志开始工作

这个好处在于反复切换主动和被动服务器非常方便, 因为服务器的配置是对称的

一般需要执行维护、优化、升级操作系统或比较方便

例如执行`alert table`可能要锁住整个表, 阻塞表的读和写

我们可以

- 先停止备库复制线程(只接受到中继日志, 不进行回放)
- 在备库上执行alert操作
- 主备角色交换(由于事件的服务器ID与主动服务器相同, 因此原本的主动服务器会忽略这些事件)
- 之前的主库就会启动复制线程

## 10.5 复制的容量和规划

写操作通常是复制的瓶颈, 并且很难通过复制来扩展写操作

例如工作负载为20%的写和80%的读, 假设

- 读和写包含同样的工作量
- 所有服务器都是等同, 每秒能进行1K的查询
- 备库和主库有同样的性能特征
- 可以把所有的读操作转移到备库

问题: 当前有一个服务器能支持每秒1K的访问, 需要增加多少个备库才能处理4K的的访问

首先是主写增加了800次, 读增加了3200次

首先每个备库都要用800个写入操作, 只有200个可以被查询, 那么就是需要增加16个备库

### 10.5.1 为什吗复制无法扩展写操作

对数据进行分区是唯一可以扩展写入的方法

### 10.5.3 规划冗余容量

如果是主-主的拓扑结构, 建议读负载不超过50%

## 10.6 复制管理和维护

### 10.6.1 监控复制

通过`show master status`命令来查看当前主库的二进制日志位置和配置

```bash
mysql> show master logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000191 | 578297856 |
| mysql-bin.000192 |      3692 |
| mysql-bin.000193 |   3914909 |
| mysql-bin.000194 |       126 |
| mysql-bin.000195 |    548051 |
| mysql-bin.000196 |  49851096 |
+------------------+-----------+
```

该命令用于给`purge master logs`命令决定使用哪个参数

可以查看binlog的复制事件

```bash
mysql > show binlog events in 'mysql-bin.000196' from 13630\G;
ERROR 1220 (HY000): Error when executing command SHOW BINLOG EVENTS: Wrong offset or I/O error
```

但笔者的测试报错, 尚未查明原因, 理论上来说 应该能看到最近的sql修改记录

### 10.6.2 测试备库延迟

`show slave status`里输出seconds_behind_master列理论上显示了备库的延迟

但可能因为各种原因导致不总是准确的

- 当发生一些错误(max_allowed_packet不匹配, 或者网络不稳定)可能中断复制,但seconds_behind_master将显示0而不是显示错误
- 备库有时无法计算延迟, 会报0或NULL
- 一个大事务导致延迟波动, 例如一个事务更新长达1小时, 最后提交, 这个更新会比他实际发生的时间要晚一个小时才记录到二进制中, 当备库执行到这个语句时, 会临时报告备库延迟一个小时, 但很快变为0
- 如果分发主库落后了, 其备库已追上分发主库, 那么备库延迟为0, 但是备库和源主库是有延迟的

最好的解决方法是`heartbeat record`, 主库每秒更新一次这个时间戳

为计算延迟, 备库当前时间戳减去心跳记录的值

### 10.6.3 确定主备是否一致

- 通过insert...SELECT复制, 生成校验表
- 然后通过checksum table来校验, 对比主备库的校验表

### 10.6.5 改变主库

步骤

- 停止向老的主库写入
- 让备库追上主库
- 将一台备库配置为新的主库
- 备库的写操作指向新的主库, 开启主库的写入

更细的操作

1. 停止主库所有的写操作, 断开所有客户端的连接以及打开的事务
2. 通过flush tables with read lock在主库上停止所有活跃的写入(可选), 也可以在主库设置read_only,也可kill掉所有打开的事务
3. 选择一个备库作为主库, 并确保它完全追上主库
4. 确保新主库和旧主库的日志是一致的
5. 在新主库中 stop slave
6. 在新主库上执行`change master to master_host=''`, 然后reset slave, 这样断开老主库的连接, 并且丢弃master.info的信息
7. 执行`show master status`记录新主库的二进制坐标
8. 确保其他备库已追赶上
9. 关闭旧主库
10. 在mysql.51以上可能需要激活新主库上的事件
11. 客户端连接新主库
12. 在每个备库执行`change master to`, 使用之前通过`show master status`得到的二进制日志坐标, 指向新的主库

> 意外的提升

- 确认哪台备库的数据最新, 检查master_log_file/read_master_log_pos的值最新的那个作为新主库
- 让所有备库执行完中继日志
- stop slave
- 在新主库上执行`change master to master_host=''`, 然后reset slave, 这样断开老主库的连接, 并且丢弃master.info的信息
- 执行`show master status`记录每个备库的二进制坐标
- 比较每台备库和主库上的master_log_file/read_master_log_pos的值
- 在mysql.51以上可能需要激活新主库上的事件
- 客户端连接新主库
- 在每个备库执行`change master to`, 使用之前通过`show master status`得到的二进制日志坐标, 指向新的主库

> 确定期望的日志位置

有1个主库和2个备库

- 主库的bin-log的pos为1582
- 备库1的read_master_log_pos为1582, bin-log的pos为8167
- 备库2的read_master_log_pos为1492

那么选备库1为主库

备库2`change to master` 并设定pos为(8167-(1582-1492))

执行之前最好确认备库1的(8167-(1582-1492))这个位置是否和备库2中的当前bin-log日志一致

如果备库1的read_master_log_pos是1581呢

需要通过mysqlbinlog或从日志服务器的二进制未见找到丢失的事件

当提升一个备库为主库时, 一定要把它的服务器ID改为原主库的服务器id

否则将不能使用日志服务器从一个旧主库重放日志事件

### 10.6.6 在一个主-主配置中交换角色

- 停止主动服务器上的所有写入
- 在主动服务器执行set global read_only=1&在配置文件中设置read_only, 或执行flush tables with read lock
- 获取主库的二进制坐标, 并在被动服务器上执行 select_master_pos_wait(), 将语句阻塞住, 直到复制赶上主数据库
- 在被动服务器上执行 set global read_only=0
- 修改应用的的配置, 写入到新的主动服务器中

## 10.7 复制的问题和解决方案

### 10.7.1 数据损坏或丢失的错误

mysql的复制并不能很好的从服务器崩溃, 掉电, 磁盘损坏, 内存或网络错误中回复

遇到这些问题几乎可以肯定的是都需要从某个点开始重启复制

大部分情况是由于非正常关机后导致的复制问题是由于没有把数据及时刷到磁盘

> 主库意外关闭

如果主库没有设置`sync_binlog`选项, 在崩溃前没有把最后的几个二进制日志事件刷到磁盘中(不经常发生, 因为二进制转储线程通常很快)

解决方案: 

指定备库从下一个二进制日志的开头读入职, 但一些日志可能永久丢失, 建议通过`pt-table-checksum`检查数据一致性

> 备库意外关闭

当备库意外关闭重启时, 会读master.info找到上次停止复制的位置

但master.info也不一定刷盘及时

如果使用InnoDB, 重启后观察MySQL日志, InnoDB在恢复时会打印出恢复点的二进制坐标

可以使用这个值指向主库的偏移量

但master.info刷盘不及时其实并不常见

> 主库上的二进制损坏

如果主库上二进制损坏, 可以选择忽略`global sql_slave_skip_counter = 1`来忽略一个损坏事件

但如果太多损坏事件, 这么做就没意义了

也可以执行`flush logs`来让主库开始一个新的日志文件

> 备库上的中继日志损坏

如果主库的中继日志是万昊的, 可以重新change master to到它正在复制的位置(Relay_master_log_file/exec_master_log_Pos)

### 二进制文件损坏

> 数据改变, 但事件仍然是有效的SQL

mysql无法察觉这种损坏

> 数据改变, 事件是无效的sql

通过`mysqlbinlog`提取错乱的数据

例如`update tab set col??????`

可以通过增加偏移量的方式来尝试找到下一个事件

> 数据遗漏或者事件的长度是错误的

这种情况mysqlbinlog可能会发生错误退出, 因为它无法读取事件

> 某些事件已经损坏或被覆盖, 或者偏移量已经改变, 并且下一个事件的起始偏移量也是错误的

mysqlbinlog也起不了作用

可以通过mysqlbinlog 找到样例日志的日志事件偏移量

```bash
 mysqlbinlog  ./mysql-bin.000196 | egrep '^# at '
 # at 10719661
# at 10719740
# at 10719818
# at 10720022
# at 10720101
# at 10720179
# at 10720346
# at 10720425
# at 10720503
```

```bash
> strings -n 2 -t d   ./mysql-bin.000196

4576374 xxxx
4576389 BEGIN
4576442 std
4576452 xxxx
4576467 UPDATE xxxx.xxxx_table SET flocktimes=0 WHERE finused=0 AND flocktimes<='1625985601'
4576609 std
4576619 xxxx
```

简单的分析找到下一个完整日志事件偏移量, 然后通过mysqlbinlog的--start-position选项来跳过损坏的事件

或者使用`change master to`命令的master_log_pos参数

### 10.7.12 InnoDB加锁读引起的锁争用

一般情况下读是非阻塞的

但执行`insert ... select`操作会锁定源表上的所有行

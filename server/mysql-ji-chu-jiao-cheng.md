# MySQL基础教程

# 第三章 MySQL监视器

## 3.3 启动MySQL监视器

```bash
mysql -u 用户名 -p密码
```

-p和密码之间不能有空

### 3.3.4 确认MySQL中文符编码的设置情况

```bash
mysql> status
--------------
/usr/local/mysql-5.5.24/bin/mysql  Ver 14.14 Distrib 5.5.24, for Linux (x86_64) using  EditLine wrapper

Connection id:		1245
Current database:
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.5.24-log Source distribution
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/tmp/mysql.sock.3306
Uptime:			5 days 9 hours 4 min 41 sec

Threads: 1  Questions: 482488  Slow queries: 1  Opens: 472832  Flush tables: 1  Open tables: 256  Queries per second avg: 1.038
--------------
```

主要看 `Server characterset`和C`lient characterset` 是否为一致

或者通过变量查看字符集

```bash
mysql> show variables like "char%";
+--------------------------+-----------------------------------------+
| Variable_name            | Value                                   |
+--------------------------+-----------------------------------------+
| character_set_client     | utf8                                    |
| character_set_connection | utf8                                    |
| character_set_database   | latin1                                  |
| character_set_filesystem | binary                                  |
| character_set_results    | utf8                                    |
| character_set_server     | latin1                                  |
| character_set_system     | utf8                                    |
| character_sets_dir       | /usr/local/mysql-5.5.24/share/charsets/ |
+--------------------------+-----------------------------------------+
8 rows in set (0.00 sec)
```

## 3.5 设置MySQL管理员root的密码

### 3.5.1 修改root密码

```bash
mysql > set password for root@localhost=PASSWORD('1234')
```

密码则变成`1234`

# 4. 创建数据库

## 4.1 创建数据库 

```bash
mysql > create database a;
```

指定字符集

```bahsh
mysql > CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

## 4.3 指定使用数据库

显示当前使用的数据库 

```bash
mysql> select database();
+------------+
| database() |
+------------+
| NULL       |
+------------+
```

## 4.6 确认表的列结构

```bash
mysql> desc str_da_xiao_xie;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| str   | varchar(64) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

- Null为No表示该字段不允许不输入任何值
- Default表示如果什么值都不输入就用这个值

> 指定字符编码创建表

`create table tb1 (empid varchar(10), name varchar(10), age int ) charset =utf8`

## 4.9 复制表

```bash
mysql >create table tba1 select * from tba;
```

# 5. 数据类型和数据输入

## 5.2 数值类型

|名称|对应的范围|
|---|---|
|int|-2^31 ~ (2^31 - 1)|
|tinyint||-2^7 ~ (-2^7 - 1)|


## 5.3 字符串类型

### 5.3.3 varchar和char的位数单位

varchar(10) 表示的是最多只能保存10个字符

## 5.4 日期与时间类型

### 5.4.2 输入日期与时间类型的数据

MySQL中 日期必须以`YYYY-MM-DD`的格式输入, 时间必须以`HH:MM:SS`的格式输入

# 6 修改表

## 6.1 alert table 命令

### 6.1.1. alter table命令

- 修改列定义: `alert table 表名 modify 列名 数据类型`
- 添加列: `alert table 表名 add 列名 数据类型
- 修改列名和定义时: `alert table 表名 change 修改前的列名 修改后的列名 修改后的数据类型`
- 删除列: `alert table 表名 drop 列名`

## 6.2 修改列的数据类型

```bash
mysql > alert table 表名 modify 列名 数据类型;
```

数据类型的修改必须具有兼容性, 不兼容可能会发生数据变成没意义的, 全部或者部分数据丢失

### 6.7 设置主键

### 6.7.2 什么是主键

什么是主键

- 没有重复的值
- 不允许输入空值`NULL`

### 6.7.3 创建主键

```bash
mysql > create table t_pk (a int primary key, b varchar(10))
```


```bash
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| a     | int(11)     | NO   | PRI | NULL    |       |
| b     | varchar(10) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

### 6.7.5 设置唯一键

```bash
mysql > create table t_uniq (a int unique, b varcher(10));
```

不允许重复, 但允许未null

```bash
mysql> desc t_uniq;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| a     | int(11)     | YES  | UNI | NULL    |       |
| b     | varchar(10) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

## 6.12 创建索引

### 6.12.2 创建索引

create index 索引名 on  表名 (列名) ;

```bash
mysql > create index b_index on  t_uniq (b) ;
```

### 6.12.3 显示索引

```bash
mysql> show index from t_uniq\G;

*************************** 1. row ***************************
        Table: t_uniq
   Non_unique: 0
     Key_name: a
 Seq_in_index: 1
  Column_name: a
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment:
Index_comment:
*************************** 2. row ***************************
        Table: t_uniq
   Non_unique: 1
     Key_name: b_index
 Seq_in_index: 1
  Column_name: b
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment:
Index_comment:
2 rows in set (0.00 sec)

ERROR:
No query specified

```

### 6.12.4 删除索引

drop index 索引名 on 表名;

索引与处理速度的关系

- 相同的值较多时, 最好不要创建索引, 例如性别
- 索引能增加查询速度, 同时更新速度也会可能变慢

# 7 复制表的列结构和记录

## 7.2 将表的结构和记录整个复制过来

`create table 新表名 select * from 原表名`

但是这种方法会导致

- 不能复制auto_increment等属性
- varchar(100) 可能会变成char(100)

## 7.3 仅复制表的了列结构

`create table 新表名 like 原表名`

会复制`列结构`和`auto_increment`和`primary key`等属性

## 7.4 复制其他表的记录

`insert into 表名 select * from 原表名`

或者仅复制某一列

`insert into tb1_bkc(name) select empid from tb1;`

# 8 使用各种条件进行提取

## 8.2 计算列值或处理字符串之后显示列

- 显示当前MySQL版本: `select version()`
- 显示当前用户: `select user()`


## 8.4 指定多个条件进行选择

### 8.4.3 使用多个AND或OR

当AND和OR组合时, 优先处理And

### 8.7.3 分组方法总结

- 提取记录后分组: `select ... from table where ... group by ...`;
- 分组后提取记录: `select .. from table group by ... having 条件`

# 10 使用多个表

## 10.2 连接多个表并显示(内连接)

### 10.2.4 使用using使 on ~ 的部分更容易阅读

```bash
mysql > select x.empid, y.name, x.sales
  from tb as x
join tb1 as y
  on x.empid=y.empid
```

等效于

```bash
mysql > select tb.empid, tb1.name, tb.sales
  from tb 
join tb1 
   using(empid)
```

## 10.3 显示多个表的所有记录(外连接)

### 10.3.2 外连接的种类

外连接种类

- 左连接(left join): 显示相匹配的记录和左边的全部记录
- 右连接(right join): 显示相匹配的记录和右边的全部记录

## 10.4 自连接

表join自身表, 则会形成一个笛卡尔积

### 10.4.2 排序的技巧其一

```bash
mysql > select a.name, a.age, count(*) 
    from tb1 as a
join tb1 as b 
    where a.age <= b.age
group by a.empid;

name age count(*)
mzk  40  1
mzk2 28  3
mzk3 20  5
mzk4 23  4
mzk5 35  2
```

count(*)可以当做为排名

## 10.5 从select的记录中select(子查询)

### 10.5.4 使用In(返回列的子查询)

`select 显示的列 from 表名 where 列名 in (通过子查询select语句提取的列)`

子查询和内连接的差异

先看下基础信息

```basah
mysql> select *   from tb;
+-------+-------+-------+
| empid | sales | month |
+-------+-------+-------+
| A103  |   101 |     4 |
| A102  |    54 |     5 |
| A104  |   181 |     4 |
| A101  |   184 |     4 |
| A103  |    17 |     5 |
| A101  |   300 |     5 |
| A102  |   205 |     6 |
| A104  |    93 |     5 |
| A103  |    12 |     6 |
| A107  |    87 |     6 |
+-------+-------+-------+
10 rows in set (0.00 sec)

mysql> select *   from tb1;
+-------+--------+------+
| empid | name   | age  |
+-------+--------+------+
| A101  | 佐藤   |   40 |
| A102  | 高乔   |   28 |
| A103  | 中川   |   20 |
| A104  | 渡边   |   23 |
| A105  | 西泽   |   35 |
+-------+--------+------+
5 rows in set (0.00 sec)
```

子查询返回结果

```bash
mysql> select tb1.empid, tb1.name  from tb1 where empid in ( select empid from tb );
+-------+--------+
| empid | name   |
+-------+--------+
| A101  | 佐藤   |
| A102  | 高乔   |
| A103  | 中川   |
| A104  | 渡边   |
+-------+--------+

```

连接返回结果

```bash

mysql> select tb1.empid, tb1.name    from tb1 join tb on tb.empid = tb1.empid ;
+-------+--------+
| empid | name   |
+-------+--------+
| A103  | 中川   |
| A102  | 高乔   |
| A104  | 渡边   |
| A101  | 佐藤   |
| A103  | 中川   |
| A101  | 佐藤   |
| A102  | 高乔   |
| A104  | 渡边   |
| A103  | 中川   |
+-------+--------+
```

可以使用`distinct` 过滤相同的列值

### 10.5.5 使用`=`代替in会报错吗

当`=` 后面的子查询只有一行时, 不会报错, 否则会报错, 例如

` select * from tb1 where empid = ( select empid from tb order by sales desc limit 1  );`

# 11 熟悉使用视图

## 11.1 什么是视图

### 11.1.1 视图的真面目

将select的结果像表一样保留下来的虚表就是视图

### 11.1.2 视图的用处

视图也可以执行update, 视图表更新了记录, 那么基表的记录也会更新

## 11.2 使用视图

### 11.2.1 创建视图

`create view 视图名 as select 列名 from 表名 where 条件`

### 11.2.2 通过视图更新列的值

`update v1 set name='主任+佐藤' where name='佐藤'`

## 11.4 限制通过视图的写入

### 11.4.1 对视图执行insert会出现什么结果

如果使用`union`、`join`、子查询的视图中, 不能执行insert和update

如果只是从某张表提取了某几列, 则可以insert和update

### 11.4.2 设置了条件的基表中会发生什么变化

```bash
mysql > create view v3 as select empid, sales from tb where sales >= 100
```

向视图v3中插入一条sales<100的记录

```bash
mysql > insert into v3 values ('xxx', 50)
```

那么基表会增加一条记录, 而视图表则不会

我们可以通过 `with check option` 来限制不满足视图的记录, 无法从视图表里插入到基表

```bash
mysql > create view v4 
    as 
select empid, sales 
    from tb
where sales > 100
    with check option;
```

这样插入不满住筛选条件的记录就会报错

`error 1369 (HY000): check option failed 'db1.v4'`

## 11.5 替换、修改和删除视图

### 11.5.1 替换视图

`create or replace view v1 as select ...`

### 11.5.2 修改视图

`alert view 视图名 as select 列名 from 表名`

### 11.5.3 删除视图

`drop view 视图名` 或 `drop view if exists v1;`

# 12 熟悉使用存储过程

## 12.1 什么是存储过程

### 12.1.2 什么是存储过程

将多个SQL语句组合成一个只需要使用命令`CALL X X`就能执行的集合, 该集合就称为存储过程

## 12.2 使用存储过程

### 12.2.1 创建存储过程

```bash
mysql -> delimiter //
mysql -> create procedure pr1(d int)
    -> begin
    -> select * from tb where sales >=d;
    -> select * from tb1;
    -> end
    -> //
mysql -> delimiter ;
```

`delimiter //`表示 将结束符设置为`//`, 因为存储过程会出现`;`

### 12.2.2 执行存储过程

执行存储过程

```bash
mysql > call pr1(200);
```

## 12.3 显示和删除存储过程

### 12.3.1 显示存储过程的内容

`show create procedure 存储过程名`

### 12.3.2 删除存储过程

`drop procedure 存储过程名`

## 12.4 什么是存储函数

与存储过程基本相同, 存储函数在执行后返回一个值

```bash
mysql > delimiter //
    -> create function fu2() returns double
    -> begin
    -> declare r double;
    -> select avg(sales) into r from tb;
    -> return r
    -> end
    -> //
mysql > delimiter ;

select fu2();
```

### 12.5.4 显示和删除存储函数

显示存储函数

`show create function 存储函数名;`

删除存储函数

`drop function 存储函数名;`

## 12.6 什么是触发器

对表执行某种操作后执行其他命令的机制

## 12.7 创建触发器

触发器被触发的时机: `before`, `after`

列值: 

- `old.列名`: 对表进行处理之前的列值
- `new.列名`: 对标进行处理之后的列值

### 12.7.2 创建触发器

```
delimiter //

create trigger tr1 before delete 
on tb1 for each row

begin
    insert into tb1_from values(old.empid, old.name, old.age);
end

delimiter ;
```

## 12.8 确认和删除触发器

### 12.8.1 确认设置的触发器

查看触发器: `show triggers;`

删除触发器: `drop trigger tr1;`

# 13. 熟练使用事务

## 13.2 设置存储引擎

### 13.2.1 确认存储引擎

`show create table tb;`

### 13.2.2 修改存储引擎

`alert table 表名 engine=MyISAM`

## 13.3 什么是事务

多个操作作为单个逻辑单元处理的功能称为`事务(transaction)`

事务开始之后处理结果反馈到数据库的操作称为`提交(commit)`

不反映到数据库而是回复为原来状态的操作称为`回滚(rollback)`

## 13.4 使用事务

```bash
mysql > start transaction;
mysql > delete from tb;
rollback; //回滚 或commit进行提交
```

## 13.5 自动提交功能

下面命令会被自动提交, 不能回滚

- drop database
- drop table
- drop view
- alert table

# 14. 使用文件进行交互

## 14.1 从文本文件中读取数据(导入)

### 14.1.1 csv文件

```
N551,佐佐木,337
N552,佐佐木,338
```


### 14.1.2 导入和导出的准备

修改my.ini的`secure-file-priv=""`, 重启mysql

```bash
mysql> select @@global.secure_file_priv;
+---------------------------+
| @@global.secure_file_priv |
+---------------------------+
|                       |
+---------------------------+
```

这里显示空字符串 则任何路径都可以导入或导出文件, 而为null则不能导入或导出文件

### 14.1.3 导入文件

从文件中读取数据

```bash
mysql > load data infile '文件名' into table 表名 选项的描述;
```

非csv文件, 我们也能指定数据格式导入

```bash
fileds terminated by 分隔符(默认是'\t': Tab)
lines terminated by 换行符(默认是'\n', 换行)
ignore 最开始跳过的行 lines (默认为0)
```

### 14.1.4 将数据写入文本文件(导出)

`select * into outfile '文件名' 选项的描述 from 表名`

## 14.2 从文件中读取并执行SQL命令

### 14.2.1 通过mysql中断执行sql语句

`source 文本文件名`

### 14.2.2 通过命令提示符执行sql命令

`mysql 数据库名 -u 用户名 -p密码 -e "mysql监视器的命令"`

## 14.3 将SQL的执行结果保存到文件中

### 14.3.2 使用tee命令将SQL语句的执行结果保存到文件中

```bash
mysql > tee log3.txt
mysql > select * from tb;
mysql > notee //停止输入到文件中
```

## 14.4 备份和恢复数据库

### 14.4.2 使用mysqldump导出

`mysqldump -u 用户名 -p密码 数据库名 > db1_out.txt`

### 14.4.3 恢复转储文件

```bash
> mysqladmin -u root -proot create db2
> mysql -u root -proot db2 < db1_out.txt
```

### 14.4.4 字符编码问题

建议恢复时确认并制定字符集 

`mysqldump -u 用户名 -p密码 数据库名 > db1_out.txt --default-charater-set=utf8`

```bash
> mysql -u root -proot db2 < db1_out.txt --default-charater-set=utf8
```

> 锁表

lock tables 表名 锁的类型

|锁的类型|限制内容|
|---|---|
|read|只能select|
|read local|只能select|
|write|没有加锁的客户端不能进行任何操作, 拥有锁的客户端可以执行操作|

> 给表解锁

`unlock tables` 解除当前所有表的锁定


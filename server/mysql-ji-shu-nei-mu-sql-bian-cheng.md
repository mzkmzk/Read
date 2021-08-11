# MySQL技术内幕-SQL编程

# 序

案例中的sql表结构和数据可通过`https://github.com/datacharmer/test_db`安装

# 2. 数据类型

## 2.3 日期和时间类型

|类型|所占空间|存储说明|
|---|---|---|
|datetime|8字节|Eight bytes: A four-byte integer for date packed as YYYY×10000 + MM×100 + DD and a four-byte integer for time packed as HH×10000 + MM×100 + SS|
|date|3字节| A three-byte integer packed as YYYY×16×32 + MM×32 + DD|
|timestamp|4字节|A four-byte integer representing seconds UTC since the epoch ('1970-01-01 00:00:00' UTC)|
|year|1字节|一个字节的整数|
|time|3字节|A three-byte integer packed as DD×24×3600 + HH×3600 + MM×60 + SS|

[参考链接](https://dev.mysql.com/doc/internals/en/date-and-time-data-type-representation.htm])

### 2.3.1 datetime和date

datetime可以表达的日期为`1000-01-01 00:00:00`~`9999-12-31 23:59:59`

date则可以表达`1000`~`9999`

### 2.3.2 timestamp

存储的范围`1970-01-01 00:00:00` UTC到`2038-01-19 03:14:07`, 起实际存储的值是`1970-01-01 00:00:00`到当前时间的毫秒数

timestamp和datetime的区别

- 线上范围不一样
- 建表时, timestamp的日期可以设置一个默认值, 而datetime不行
- 在更新表时, 可以设置timestamp类型的列自动更新时间为当前时间

创建表时, 可以用`default`设置默认时间, 也可以用`on update` 设置当更新时取当前时间

```bash
mysql > create table t (
    -> a int,
    -> b timestamp default on update current_timestamp
    ->) engine=InnoDB;
)
```

### 2.3.3 year和time

year(4)可以显示的年份范围是1901~2155

year(2)可以显示的范围是1970~2070, 其中00~69代表2000~2069年

```bash
mysql> create table t (a year(2));
Query OK, 0 rows affected (0.07 sec)

mysql> insert into t select '1990';
Query OK, 1 row affected (0.06 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> select * from t ;
+------+
| a    |
+------+
|   90 |
+------+
1 row in set (0.00 sec)
```

### 2.3.4 与日期和时间相关的函数

> now、current_timestamp和sysdate

```bash
mysql> select now(), current_timestamp(), sysdate(), sleep(2), now(), current_timestamp(), sysdate()\G;
*************************** 1. row ***************************
              now(): 2021-07-21 09:35:15
current_timestamp(): 2021-07-21 09:35:15
          sysdate(): 2021-07-21 09:35:15
           sleep(2): 0
              now(): 2021-07-21 09:35:15
current_timestamp(): 2021-07-21 09:35:15
          sysdate(): 2021-07-21 09:35:17
1 row in set (2.00 sec)

ERROR:
No query specified

```

可以看到只有sysdate是每次调用去取时间的

而now和current_timestamp都是执行语句时就确认了

> 时间加减函数

```bash
mysql > select now(), date_add(now(), interval 1 day), date_sub(now(), interval 1 day)\G;

*************************** 1. row ***************************
                          now(): 2021-07-21 09:37:27
date_add(now(), interval 1 day): 2021-07-22 09:37:27
date_sub(now(), interval 1 day): 2021-07-20 09:37:27
1 row in set (0.00 sec)

ERROR:
No query specified
```

除了day 还有关键字 microsecond, second, minute, hour, week, month

## 2.4 关于日期的经典SQL编程问题

### 2.4.1  生日问题

根据某个用户的出生日期和当前日期, 计算他最近的生日, 问题是如何正确处理闰月

在生日问题中, 一般对闰月29号生日的处理如下:

- 如果是闰月, 那么返回2月29日
- 如果不是闰月, 则返回3月1日


若当年无29号时

```bash
+--------------------+------------+------------+
| name               | birthday   | birth_day  |
+--------------------+------------+------------+
| Lanphier Xiaopeng  | 1964-02-28 | 2021-02-28 |
| Doering Monique    | 1954-02-28 | 2021-02-28 |
| Peak Owen          | 1963-02-28 | 2021-02-28 |
| Rusterholz Bikash  | 1958-02-28 | 2021-02-28 |
| Stasinski Fumitaka | 1956-02-29 | 2021-03-01 |
| Haddadi Fei        | 1954-02-28 | 2021-02-28 |
| Taubman Rafols     | 1960-02-29 | 2021-03-01 |
| Fargier Macha      | 1964-02-28 | 2021-02-28 |
| Benzaken Jamaludin | 1960-02-29 | 2021-03-01 |
| Gubsky Neven       | 1959-02-28 | 2021-02-28 |
+--------------------+------------+------------+
```

若当年有29号时
```bash
+--------------------+------------+------------+
| name               | birthday   | birth_day  |
+--------------------+------------+------------+
| Lanphier Xiaopeng  | 1964-02-28 | 2020-02-28 |
| Doering Monique    | 1954-02-28 | 2020-02-28 |
| Peak Owen          | 1963-02-28 | 2020-02-28 |
| Rusterholz Bikash  | 1958-02-28 | 2020-02-28 |
| Stasinski Fumitaka | 1956-02-29 | 2020-02-29 |
| Haddadi Fei        | 1954-02-28 | 2020-02-28 |
| Taubman Rafols     | 1960-02-29 | 2020-02-29 |
| Fargier Macha      | 1964-02-28 | 2020-02-28 |
| Benzaken Jamaludin | 1960-02-29 | 2020-02-29 |
| Gubsky Neven       | 1959-02-28 | 2020-02-28 |
+--------------------+------------+------------+
```

下面我们来写下大概sql

- 查询今年当年多少岁
- 按mysql逻辑计算今年的生日日期
- 因为mysql当非闰年时, 生日为2月29号的则会被改为2月28日, 所以需要进行判断是否需要加一天

```bash
select name, birthday,
    if (cur>today, cur, next) as birth_day
from (
    select name, birthday, today ,
        date_add(cur, interval if (day(birthday)=29 && day(cur)=28, 1, 0 ) day ) as cur,
        date_add(next, interval if (day(birthday)=29 && day(cur)=28, 1, 0) day ) as next
    from (
        select name, birthday, today, 
            date_add(birthday, interval diff year) as cur,
            date_add(birthday, interval diff + 1 year ) as next
        from (
            select concat(last_name, ' ', first_name) as name, 
                birth_date as birthDay,
                (year('2020-01-26 09:02:09') - year(birth_date)) as diff ,
                '2020-01-26 09:02:09' as today
            from employees 
                where month(birth_date) = 2 && ( day(birth_date) =28 || day(birth_date) = 29  ) 
                limit 10
            ) as a
        ) as b
) as c ;
```

### 2.4.2 重叠问题

示例数据表-sessionid

```bash
mysql> select * from sessions where  app = 'app1' and usr = 'user1';
+----+------+-------+-----------+----------+
| id | app  | usr   | starttime | endtime  |
+----+------+-------+-----------+----------+
|  1 | app1 | user1 | 08:30:00  | 10:30:00 |
|  3 | app1 | user1 | 09:00:00  | 09:30:00 |
|  5 | app1 | user1 | 09:15:00  | 09:30:00 |
|  7 | app1 | user1 | 10:45:00  | 11:30:00 |
| 17 | app1 | user1 | 10:15:00  | 10:40:00 |
+----+------+-------+-----------+----------+
```

这张表可以理解为90年代宽带按时间收费的表

> 标示重叠

查询哪个时间段有重叠

```bash
mysql > select a.app, a.usr, a.starttime, a.endtime, b.starttime, b.endtime 
    from 
        sessions a, sessions b
    where a.app = b.app and a.usr = b.usr and a.app = 'app1' and a.usr = 'user1'
    and( b.endtime >= a.starttime and b.starttime <= a.endtime);

+------+-------+-----------+----------+-----------+----------+
| app  | usr   | starttime | endtime  | starttime | endtime  |
+------+-------+-----------+----------+-----------+----------+
| app1 | user1 | 08:30:00  | 10:30:00 | 08:30:00  | 10:30:00 |
| app1 | user1 | 09:00:00  | 09:30:00 | 08:30:00  | 10:30:00 |
| app1 | user1 | 09:15:00  | 09:30:00 | 08:30:00  | 10:30:00 |
| app1 | user1 | 10:15:00  | 10:40:00 | 08:30:00  | 10:30:00 |
| app1 | user1 | 08:30:00  | 10:30:00 | 09:00:00  | 09:30:00 |
| app1 | user1 | 09:00:00  | 09:30:00 | 09:00:00  | 09:30:00 |
| app1 | user1 | 09:15:00  | 09:30:00 | 09:00:00  | 09:30:00 |
| app1 | user1 | 08:30:00  | 10:30:00 | 09:15:00  | 09:30:00 |
| app1 | user1 | 09:00:00  | 09:30:00 | 09:15:00  | 09:30:00 |
| app1 | user1 | 09:15:00  | 09:30:00 | 09:15:00  | 09:30:00 |
| app1 | user1 | 10:45:00  | 11:30:00 | 10:45:00  | 11:30:00 |
| app1 | user1 | 08:30:00  | 10:30:00 | 10:15:00  | 10:40:00 |
| app1 | user1 | 10:15:00  | 10:40:00 | 10:15:00  | 10:40:00 |
+------+-------+-----------+----------+-----------+----------+
```

> 分组查询

服务商允许多个session的连接, 并把其计费统计为1次

先创建查询不重复的开始时间的视图

```bash
mysql > create view v_s_s as
    select distinct app, usr, starttime as s 
    from 
    sessions as a 
    where not exists (
        select * 
        from
        sessions as b 
        where a.app = b.app
        and a.usr = b.usr
        and a.starttime > b.starttime
        and a.starttime <= b.endtime 
    ) and a.app = 'app1' and a.usr = 'user1';

+------+-------+----------+
| app  | usr   | s        |
+------+-------+----------+
| app1 | user1 | 08:30:00 |
| app1 | user1 | 10:45:00 |
+------+-------+----------+
```

同样的方法查询结束时间

```bash
mysql > create view v_s_e as
    select distinct app, usr, endtime as e 
    from 
    sessions as a 
    where not exists (
        select * 
        from
        sessions as b 
        where a.app = b.app
        and a.usr = b.usr
        and a.endtime >= b.starttime
        and a.endtime < b.endtime 
    ) and a.app = 'app1' and a.usr = 'user1';

+------+-------+----------+
| app  | usr   | e        |
+------+-------+----------+
| app1 | user1 | 11:30:00 |
| app1 | user1 | 10:40:00 |
+------+-------+----------+
```

合并两张表, 并通过min函数取得结束时间

```bash
mysql > select distinct s.app, s.usr, s.s,
(select min(e) from v_s_e as s2
where s2.e>s.s and s.app = s2.app and s.usr = s2.usr) as ee
from v_s_s as s, v_s_e as e
where s.app = e.app and s.usr = e.usr;

+------+-------+----------+----------+
| app  | usr   | s        | ee       |
+------+-------+----------+----------+
| app1 | user1 | 08:30:00 | 10:30:00 |
| app1 | user1 | 10:45:00 | 11:30:00 |
+------+-------+----------+----------+
2 rows in set (0.00 sec)
```

> 最大重叠会话数

```bash
mysql > select app, max(count) as max
from (
    select app, s,
    (
        select count(1) from sessions as b
            where a.app = b.app and s>=starttime and s < endtime
    ) as count
    from (
        select distinct app, starttime as s
        from sessions
    ) as a
) as bc
group by app;

+------+------+
| app  | max  |
+------+------+
| app1 |    4 |
| app2 |    3 |
+------+------+
```

## 2.5 数字类型

### 2.5.1 整形

|类型|占用空间(字节)|最小值(signned/unsigned)|最大值(signned/unsigned)|
|---|---|---|---|
|tinyint|1|-2^7/0|(2^7-1)/(2^8-1))|
|smallint|2|-2^15/0|(2^15-1)/(2^16-1))|
|mediumint|3|-2^23/0|(2^23-1)/(2^24-1))|
|int|4|-2^31/0|(2^31-1)/(2^32-1))|
|bigint|8|-2^63/0|(2^63-1)/(2^64-1))|


# 3. 查询处理

## 3.1 逻辑查询处理

sql中第一个被处理的子句总是from子句

mysql顺序


```
(8) select (9) distinct<select_list>
(1) from <left_table>
(3) <join_type> join <right_table>
(2)   on <join_condition>
(4) where<where condition>
(5) group by<group_by_list>
(6) with{cube|rollup}
(7) having<having_condition>
(10) order by<order_by_list>
(11) limit <limit_number>
```

每一步都会生成一个虚拟表

* from子句中的左表`<left_table>`和右边`<right_table>`执行笛卡尔积产生虚拟表VT1
* on: 对虚拟表VT1应用ON筛选, 只有符合`<join_condition>`的行才被插入虚拟VT2中
* join: 如果指定了`outer join`, 那么保留表中未匹配的行作为外部行添加到虚拟表VT2中, 产生虚拟表VT3, 如果FROM子句包含两张表以上, 则对上一个连接生成的结果表VT3和下一个表重复执行步骤1~步骤3, 直到所有表结束
* where: 对虚拟表VT3应用where过滤条件, 只有符合`<where_condition>`的记录才被插入到虚拟表V4中
* group by: 根据group by子句中的列, 对VT4中的记录进行分组产生, 产生VT5
* cube | rollup: 对VT5表, 进行CUBE或ROLLUP操作产生表VT6
* having: 对虚拟表VT6应用having过滤器, 只有符合`<having_condition>`的才会被插入到VT7中
* select: 第二次执行select操作, 选择指定的列, 插入到虚拟表VT8中
* distinct: 去除重复数据, 产生虚拟表VT9
* order by: 将VT9按照`<order_by_list>`进行排序操作, 产生虚拟表VT10
* limit: 取出指定行的记录, 产生虚拟表VT11


```bash
mysql> select * from customers;
+-------------+----------+
| customer_id | city     |
+-------------+----------+
| 163         | HangZhou |
| 9you        | ShangHai |
| baidu       | HangZhou |
| TX          | HangZhou |
+-------------+----------+

mysql> select * from orders;
+----------+-------------+
| order_id | customer_id |
+----------+-------------+
|        1 | 163         |
|        2 | 163         |
|        3 | 9you        |
|        4 | 9you        |
|        5 | 9you        |
|        6 | TX          |
|        7 | NULL        |
+----------+-------------+
```

查询来自杭州的订单少于2的客户

```bash
mysql > select c.customer_id, count(o.order_id) as total_orders
from customers as c 
left join orders as o
on c.customer_id = o.customer_id
where c.city = 'HangZhou'
group by c.customer_id
having count(o.order_id) < 2
order by total_orders desc;

```

### 3.1.1 执行笛卡尔积

如果from子句前包含a行数据, from子句包含b行数据, 那么虚拟表VT1将包含a*b行数据

### 3.1.2 应用on过滤器

对于普通表达式而言, 逻辑表达式的值只有两种 true和false

```bash
mysql> select 1 = null;
+----------+
| 1 = null |
+----------+
|     NULL |
+----------+
1 row in set (0.00 sec)

mysql> select null = null;
+-------------+
| null = null |
+-------------+
|        NULL |
+-------------+
1 row in set (0.00 sec)
```

对于比较返回值为NULL, 用户应该将其视为UNKNOWN

对于ON过滤条件下的NULL比较, 此时比较结果是UNKNOWN, 但会被视为false来处理

但在下面两种情况, 认为两个NULL值的比较是相等的

- 在group by子句中, 所有NULL值分配到同一个组
- 在order by子句中, 所有NULL值排列在一起

```bash
mysq >  select * from t;
+------+
| a    |
+------+
| a    |
| NULL |
| b    |
| c    |
| NULL |
+------+
```

对其order by

```bash
mysql> select * from t order by a ;
+------+
| a    |
+------+
| NULL |
| NULL |
| a    |
| b    |
| c    |
+------+
```

对其group by

```bash
mysql> select a, count(1) from t group by a;
+------+----------+
| a    | count(1) |
+------+----------+
| NULL |        2 |
| a    |        1 |
| b    |        1 |
| c    |        1 |
+------+----------+

```

所以生成虚拟表VT2则显示为

```
mysql> select *
    -> from customers as c
    ->  join orders as o
    -> on c.customer_id = o.customer_id;
+-------------+----------+----------+-------------+
| customer_id | city     | order_id | customer_id |
+-------------+----------+----------+-------------+
| 163         | HangZhou |        1 | 163         |
| 163         | HangZhou |        2 | 163         |
| 9you        | ShangHai |        3 | 9you        |
| 9you        | ShangHai |        4 | 9you        |
| 9you        | ShangHai |        5 | 9you        |
| TX          | HangZhou |        6 | TX          |
+-------------+----------+----------+-------------+
```

### 3.1.3 添加外部行

这一部值在连接类型为outer join 时发生

添加外部行的工作就是在VT2表的基础上添加保留表中被过滤条件过滤掉的数据

非保留表中的数据被赋予NULL值, 最后生成虚拟表V3

```bash
mysql> select *
    -> from customers as c
    -> left join orders as o
    -> on c.customer_id = o.customer_id;
+-------------+----------+----------+-------------+
| customer_id | city     | order_id | customer_id |
+-------------+----------+----------+-------------+
| 163         | HangZhou |        1 | 163         |
| 163         | HangZhou |        2 | 163         |
| 9you        | ShangHai |        3 | 9you        |
| 9you        | ShangHai |        4 | 9you        |
| 9you        | ShangHai |        5 | 9you        |
| baidu       | HangZhou |     NULL | NULL        |
| TX          | HangZhou |        6 | TX          |
+-------------+----------+----------+-------------+
```

### 3.1.4 应用where过滤器

在当前用用where过滤器中, 有两种过滤是不被允许的

- 由于数据未分组, 因此现在还不能在where过滤器中使用`where_condition=min(col)`这类数据进行过滤
- 由于没有进行行的选取, 因此在select中使用列的别名也是不被允许的, 例如`select city as c from t where c='ShangHai'`

```bash
mysql> select *
    -> from customers as c
    -> left join orders as o
    -> on c.customer_id = o.customer_id
    -> where c.city = 'HangZhou';
+-------------+----------+----------+-------------+
| customer_id | city     | order_id | customer_id |
+-------------+----------+----------+-------------+
| 163         | HangZhou |        1 | 163         |
| 163         | HangZhou |        2 | 163         |
| baidu       | HangZhou |     NULL | NULL        |
| TX          | HangZhou |        6 | TX          |
+-------------+----------+----------+-------------+
```

### 3.1.5 分组

```bash
mysql>  select *
    -> from customers as c
    -> left join orders as o
    -> on c.customer_id = o.customer_id
    -> where c.city = 'HangZhou'
    -> group by c.customer_id;
+-------------+----------+----------+-------------+
| customer_id | city     | order_id | customer_id |
+-------------+----------+----------+-------------+
| 163         | HangZhou |        1 | 163         |
| baidu       | HangZhou |     NULL | NULL        |
| TX          | HangZhou |        6 | TX          |
+-------------+----------+----------+-------------+
```

### 3.1.7 应用having过滤器


```bash
mysql> select  *
    -> from customers as c
    -> left join orders as o
    -> on c.customer_id = o.customer_id
    -> where c.city = 'HangZhou'
    -> group by c.customer_id
    -> having count(o.order_id) < 2;
+-------------+----------+----------+-------------+
| customer_id | city     | order_id | customer_id |
+-------------+----------+----------+-------------+
| baidu       | HangZhou |     NULL | NULL        |
| TX          | HangZhou |        6 | TX          |
+-------------+----------+----------+-------------+
```

注意这里不能使用`count(1)`或`count(*)`

因为这会把outer join 添加的行 统计入内 而导致最终查询结果与预期结果不同

假如使用 `count(*)`


```bash
mysql>  select c.customer_id, count(*) as total_orders
    ->      from customers as c
    ->     left join orders as o
    ->      on c.customer_id = o.customer_id
    ->     where c.city = 'HangZhou'
    ->      group by c.customer_id
    ->      having count(*) < 2;
+-------------+--------------+
| customer_id | total_orders |
+-------------+--------------+
| baidu       |            1 |
| TX          |            1 |
+-------------+--------------+
```

tips: 子查询不能作为分组的聚合函数, 如 having count(select ...) <2 是不合法的

### 3.1.10 应用order by 子句

```bash
mysql>  select c.customer_id, count(o.order_id) as total_orders
    -> from customers as c
    -> left join orders as o
    -> on c.customer_id = o.customer_id
    -> where c.city = 'HangZhou'
    -> group by c.customer_id
    -> having count(o.order_id) < 2
    -> order by total_orders desc;
+-------------+--------------+
| customer_id | total_orders |
+-------------+--------------+
| TX          |            1 |
| baidu       |            0 |
+-------------+--------------+
2 rows in set (0.00 sec)
```

没有order by子句时, 数据并非总是按照主键进行排序的

### 3.1.11 limit子句

一般分页都用limit n,m, 这种效率是十分低的

## 3.2 物理查询处理

实际的查询不一定完全按照逻辑处理的步骤实现, mysql会根据索引来优化,

# 4. 子查询

## 4.1 子查询概述

### 4.1.1 子查询的优点和限制

一个子查询会返回单一值、一行、一个列、或一个表, 这些子查询被称为标量, 列, 行和表子查询

### 4.1.2 使用子查询进行比较

最常见的查询方式

`non_subquery_operand comparison_operator (subquery)`

comparison_operator 可以是`=`, `>`, `<`, `>=`, `<=`, `<>`

例如找出t1表中含有两个id相同的列

```bash
mysql > select * from t1 as t
    where 2 = (select count(*) from t1 where t1.id = t.id )
```

### 4.1.3 使用any, in, 和some进行子查询

查询语法

`operand comparison_operator any (subquery)`

`operand in (subquery)`

`operand comparison_operator some (subquery)`

例如: `select s1 from t1 where s1 > any (select s1 from t2)`

### 4.1.4 使用all进行子查询

查询语法

`operand comparison_operator all (subquery)`

通常包含NULL值的表和空表都是"边缘情况", 在写子查询都要考虑这两种可能

not in 是 `<> all`的别名


## 4.2 独立子查询

假如想要知道每个美国员工都至少处理过一个订单的所有客户

假如我们知道美国员工有5个, id为1,2,3,4,8

那么sql可以写为

```bash
select customer_id
from orders
where employee_id in (1, 2, 3, 4, 8)
group by customer_id
having count(distinct employee_id) = 5
```

现在替换为子查询

```bash
select customer_id
from orders
where employee_id in (select employee_id from employees where country = 'USA')
group by customer_id
having count(distinct employee_id) = (select count(*) from employees where country = 'USA')

```

另外一个查询是返回每个约个最后订单日期发生的时间


```bash
select * from orders
where order_date in ( select max(order_date) from orders group by (data_format(order_date, '%Y%M')))
```

mysql会对in子查询进行优化

`select ... from t1 where t1.a in (select b from t2)` 优化未

`select ... from t1 where exists ( select 1 from t2 where t2.b =t1.a)`

所以我们之前的语句理论上扫行为O(M+N), 但是这样变为相关子查询后扫描就变成了O(N+M*N)

我们的语句会被错误的优化未

```bash
select * from orders as A
where exists 
    ( select * 
        from orders
        group by (data_format(order_date, '%Y%M'))
        having Max(order_data) = A.OrderDat
    )
```

我们可以改变写法改为

```bash
select  * from orders A
where exists (select * from ( select max(order_data) as order_data from orders 
                                    group by ( data_format(order_data, '%Y%M'))  ) B
                            where A.order_data = B.order_date
             );
                            
```

虽然这样改了还是相关子查询, 但是减少了外部查询与子查询的匹配次数

## 4.3 相关子查询

相关子查询: 子查询会对外部查询的每一行进行一次计算

查询每个员工最后订单日期的订单

```bash
select * from orders as a
where order_date = ( select max(order_date) from orders as b
                        where a.employees_id = b.employees_id)
```

可以用派生表来优化


```bash
select a.order_id, b.order_date from orders as a, 
(select employee_id, max(order_date) as order_date from orders
group by employee_id  ) as b
where a.employee_id=b.employee_id and a.order_date = b.order_date
```

## 4.4 exists谓词

### 4.4.1 exists

exists 会认为unknown为false

查询来自西班牙且发生过订单的消费者

```bash
select customer_id, company_name from
customers as a
where country = 'Spain'
    and exists 
        (select * from orders as b
            where a.customer_id = b.customer_id)
```

通常不建议在sql中使用*, 但是在exists子查询可以放心使用, 因为exists只关心行是否存在, 而不会去取值

### 4.4.2 not exists

exists与in的小区别在于, exists总返回true和false, 而in返回true, false, unknown

但在过滤器中 unknown 会被当做false, 所以 in和exists一样, sql优化器会选择相同的执行计划

因为在包含null值时, in 总是返回true和unknown, 那么not in就是总返回false, unknown

而对于not exists 其返回总是true和false, 这就是not in和not exists的区别

```bash
mysql> select null not in ('a', 'b', null);
+------------------------------+
| null not in ('a', 'b', null) |
+------------------------------+
|                         NULL |
+------------------------------+

mysql> select 'c'  not in ('a', 'b', null);
+------------------------------+
| 'c'  not in ('a', 'b', null) |
+------------------------------+
|                         NULL |
+------------------------------+
```
查询来自西班牙且没有订单的客户信息

```bash
select customer_id, company_name from
customers as a
where country = 'Spain'
    and customer_id not in 
        (select customer_id from orders  )
```

这个时候 如果orders里没有customer_id为null的行, 则没问题

但是一旦有,

则这个查询语句恒定返回空结果

解决办法可以在子查询增加where过滤NULL的行, 或者使用not exists

## 4.5 派生表

使用派生表有以下规则

- 列的名称必须是唯一的
- 某些情况不支持limit

```bash
mysql> select 'c' as a, 'b' as a ;
+---+---+
| a | a |
+---+---+
| c | b |
+---+---+

mysql> select * from ( select 'c' as a, 'b' as a  ) as t;
ERROR 1060 (42S21): Duplicate column name 'a'
```

部分情况不支持limit

```bash
mysql > select customer_id, company_name
    from customers as a
    where customer_id in (select customer_id from orders limit 5);
Error 1235: this is version of mysql doesn't yet support 'Limit & in/all/any/some subquery'
```

## 4.6 子查询可以解决的经典问题

### 4.6.1 行号

```bash
mysql> select * from sales;
+-------+-------+-----+
| empid | mgrid | qty |
+-------+-------+-----+
| A     | Z     | 300 |
| B     | X     | 100 |
| C     | X     | 200 |
| D     | Y     | 200 |
| E     | Z     | 250 |
| F     | Z     | 300 |
| G     | X     | 100 |
| H     | Y     | 150 |
| I     | X     | 250 |
| J     | Z     | 100 |
| K     | Y     | 200 |
+-------+-------+-----+
```

对单个列进行统计行号

```bash
mysql > select empid, 
    (select count(*) from sales as t2
    where t1.empid >= t2.empid) as row_num
from sales as t1
```

这个运行次数很慢

每条记录都需要在子查询进行一次查找, 因为empid已经有索引了

所以第一行需要扫描1行记录, 第N条需要扫描N条记录, 总数为N*(N-1)/2条数据

而如果没有索引, 则需要扫描N^2条

```bash
mysql> explain select empid,      (select count(*) from sales as t2     where t1.empid <= t2.empid) as row_num from sales as t1;
+----+--------------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
| id | select_type        | table | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
+----+--------------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
|  1 | PRIMARY            | t1    | index | NULL          | PRIMARY | 42      | NULL |   11 | Using index              |
|  2 | DEPENDENT SUBQUERY | t2    | index | PRIMARY       | PRIMARY | 42      | NULL |   11 | Using where; Using index |
+----+--------------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
```

以上统计行号都是建立在数据不重复的情况

假如要给以下表弄排序呢

```bash
mysql> select * from 4_6_1_t;
+------+
| a    |
+------+
| X    |
| X    |
| X    |
| Y    |
| Y    |
| Z    |
+------+
```

辅助的nums表

```bash
> select * from nums;
+------+
| a    |
+------+
|    1 |
|    2 |
|    3 |
|    4 |
|    5 |
|    6 |
|    7 |
|    8 |
|    9 |
+------+
```

先找到重复元素的个数总数以及元素在非重复元素的开始序号

```bash
mysql>  select a, count(*) as count,
    ->         (select count(*) from 4_6_1_t as b
    ->             where b.a < a.a ) as smaller
    ->     from 4_6_1_t as a
    ->     group by a;
+------+-------+---------+
| a    | count | smaller |
+------+-------+---------+
| X    |     3 |       0 |
| Y    |     2 |       3 |
| Z    |     1 |       5 |
+------+-------+---------+
```

然后借助nums表, x需要复制3次, y需要复制2次, z需要复制一次, 来生成最终行号

最终sql

```bash
mysql > select nums.a+smaller as rownum, c.a from (
    select a, count(*) as count,
        (select count(*) from 4_6_1_t as b
            where b.a < a.a ) as smaller
    from 4_6_1_t as a
    group by a
) as c, nums
where nums.a <= count
order by rownum
        
)

+--------+------+
| rownum | a    |
+--------+------+
|      1 | X    |
|      2 | X    |
|      3 | X    |
|      4 | Y    |
|      5 | Y    |
|      6 | Z    |
+--------+------+
```

# 5. 联接与集合操作

## 5.1 联接查询

MySQL的主要联合查询如下

- CROSS JOIN
- INNER JOIN
- OUTER JOIN

INNER JOIN没指定on的话 跟CROSS JOIN基本一致

### 5.1.2 CROSS JOIN

CROSS JOIN 对两个表执行笛卡尔积

### 5.1.3 INNER JOIN

- 产生笛卡尔积
- 进行ON过滤

### 5.1.4 OUTER JOIN

- 产生笛卡尔积
- 进行ON过滤
- 对保留表未找到匹配数据的行, 添加进新的虚拟表

outer join 必须制定on, 否则mysql报错

# 6. 聚合和旋转操作

### 6.4 Pivoting

### 6.4.1 开放架构

表数据

```
mysql> select * from 6_4_1_t;
+----+-----------+------------+
| id | attribute | value      |
+----+-----------+------------+
|  1 | attr1     | bmw        |
|  1 | attr2     | 100        |
|  1 | attr3     | 2010-01-01 |
|  2 | attr2     | 200        |
|  2 | attr3     | 2010-03-04 |
|  2 | attr4     | M          |
|  2 | attr5     | 50.60      |
|  3 | attr1     | suv        |
|  3 | attr2     | 10         |
|  3 | attr3     | 2011-11-11 |
+----+-----------+------------+
```

查询表数据


```bash
mysql> select id,
    max(case when attribute='attr1' then value end) as attr1,
    max(case when attribute='attr2' then value end) as attr2,
    max(case when attribute='attr3' then value end) as attr3,
    max(case when attribute='attr4' then value end) as attr4,
    max(case when attribute='attr5' then value end) as attr5
from 6_4_1_t
group by id;

+----+-------+-------+------------+-------+-------+
| id | attr1 | attr2 | attr3      | attr4 | attr5 |
+----+-------+-------+------------+-------+-------+
|  1 | bmw   | 100   | 2010-01-01 | NULL  | NULL  |
|  2 | NULL  | 200   | 2010-03-04 | M     | 50.60 |
|  3 | suv   | 10    | 2011-11-11 | NULL  | NULL  |
+----+-------+-------+------------+-------+-------+
```

这里的max只是用来取值而已, 用min也是可以的

# 7 游标

# 8 事务编程

## 8.1 事务概述

ACID:

- A(atomicity): 原子性
- C(consistency): 一致性
- I(isolation): 隔离性
- D(durability): 持久性

## 8.4 隐式提交的SQL语句

以下SQL语句会产生一个隐式的提交操作

- DDL语句: alert table, rename table, truncate table....
- 隐式地修改MySQL架构的操作, create user ...
- 管理语句: analyze table, ...

以上语句无法回滚

## 8.7 不好的事务编程习惯

### 8.7.1 在循环中提交


# 9 索引

## 9.1 缓冲池、顺序读取与随机读取

对于innodb存储引擎, 变量innodb_buffer_pool_size决定缓冲池的大小

在innodb存储引擎中, 一个区是连续的64个页

## 9.2 数据结构和算法

### 9.2.2 二叉查找树和平衡二叉树

二叉查找树: 

- 左子树的键值少于根的键值
- 右子数的键值大于根的键值

平衡二叉树:

- 二叉查找树
- 任何节点的两颗子树的高度最大差为1

## 9.3 B+树

- 平衡查找树
- 所有记录节点都按照键值大小放在同一层的叶子节点

### 9.3.1 B+树的插入操作

|Leaf Page是否为满|Index Page是否为满|操作|
|---|---|---|
|No|No|直接将记录插入到叶子节点|
|Yes|No|- 拆分LeafPage<br>- 将中间的节点放入到Index Page中<br>- 小于中间节点的记录放在左边<br>- 大于等于中间节点的放在右边|
|Yes|Yes|- 拆分Leaf Page<br>- 小于中间节点的记录放在左边<br>- 大于等于中间节点的记录放在右边<br>- 拆分Index Page<br>- 小于中间节点的记录放在左边<br>- 大于中间节点的记录放在右边<br>- 中间节点放入上一层的Index Page|

动画模拟`https://www.cs.usfca.edu/~galles/visualization/Algorithms.html`

### 9.3.2 B+树的删除操作

B+数使用填充因子来控制树的删除变化, 填充因子可设的最小值是50%

|叶子节点小于填充因子|中间节点小于填充因子|操作|
|---|---|---|
|No|No|直接将记录从叶子节点删除, 如果该节点还是Index Page的节点, 用该节点的右节点代替|
|Yes|No|合并叶子节点和他的兄弟节点, 同时更新Index Page|
|Yes|Yes|- 合并叶子节点和他的兄弟节点<br>- 更新Index Page<br>- 合并Index Page和他的兄弟节点|

## 9.4 B+树索引

在InnoDB索引中, 每个页的大小为16KB

### 9.4.1 InnoDB B+树索引

聚集索引是根据主键创建的一颗B+树

聚焦索引的叶子节点存放了表中的所有记录

辅助索引是根据索引键创建的一颗B+树

其叶子节点仅存放索引键值以及该索引键值指向的主键

### 9.5 Cardinality

### 9.5.1 什么是Cardinality

在访问表中很小一部分行时, 使用B+树索引才有意义

对于性别、地区等字段, 没必要添加索引

怎么查看索引是否高选择性, 可以通过`show index`语句中的cardinality观察

cardinality / n_rows_in_table 应该尽可能解决1, 如果非常小, 则考虑是否要建索引

### 9.5.2 InnoDB 存储引擎怎么统计Cardinality

InnoDB更新Cardinality更新时的策略

- 表中1/16数据已变化
- stat_modified_counter > 2 000 000 000

stat_modified_counter表示发生变化的次数

InnoDB计算Cardinality过程

- 取得B+树索引中叶节点的数量即为A
- 随机取得B+树索引中的8个叶节点, 统计每个页不同记录的个数, P1, P2 ...P8
- 返回(P1+P2+...P8)*A/8

## 9.6 B+树索引的使用

### 9.6.2 联合索引

创建索引

```
create table t (
    a int,
    b, int,
    primary key (a),
    key idx_a_b (a, b)
) engine = innodb
```

对于 查询`select * from table where a =xxx and b=xxx` 显然是可以使用(a,b)这个联合索引的

对于单个a列的查询是可以用(a,b)这个索引的

对于单个b列的查询是不可以用(a,b)这个索引的

初始化demo

```bash
create table 9_6_2_buy_log (
    userid int unsigned not null,
    buy_date date
) engine = innodb;

insert into 9_6_2_buy_log values (1, '2009-01-01');
insert into 9_6_2_buy_log values (2, '2009-01-01');
insert into 9_6_2_buy_log values (3, '2009-01-01');
insert into 9_6_2_buy_log values (1, '2009-02-01');
insert into 9_6_2_buy_log values (3, '2009-02-01');
insert into 9_6_2_buy_log values (1, '2009-03-01');
insert into 9_6_2_buy_log values (1, '2009-04-01');

alter table 9_6_2_buy_log add key ( userid );
alter table 9_6_2_buy_log add key (userid, buy_date);
```

根据userid查询

```bash
mysql> explain select * from 9_6_2_buy_log where userid=2;
+----+-------------+---------------+------+-----------------+--------+---------+-------+------+-------+
| id | select_type | table         | type | possible_keys   | key    | key_len | ref   | rows | Extra |
+----+-------------+---------------+------+-----------------+--------+---------+-------+------+-------+
|  1 | SIMPLE      | 9_6_2_buy_log | ref  | userid,userid_2 | userid | 4       | const |    1 |       |
+----+-------------+---------------+------+-----------------+--------+---------+-------+------+-------+
```

- possible_keys 表示有两个可用的索引
- 哟与花旗最终选择了`key`为userid的索引

而加上了order之后则会选择含有buy_date的索引

```bash
mysql>  explain select * from 9_6_2_buy_log where userid=1 order by buy_date ;
+----+-------------+---------------+------+-----------------+----------+---------+-------+------+--------------------------+
| id | select_type | table         | type | possible_keys   | key      | key_len | ref   | rows | Extra                    |
+----+-------------+---------------+------+-----------------+----------+---------+-------+------+--------------------------+
|  1 | SIMPLE      | 9_6_2_buy_log | ref  | userid,userid_2 | userid_2 | 4       | const |    4 | Using where; Using index |
+----+-------------+---------------+------+-----------------+----------+---------+-------+------+--------------------------+
```

因为用这个索引的 buy_date是排好序的, 无需对buy_date进行再次排序

### 9.6.3 覆盖索引

覆盖索引: 从辅助索引就可以得到查询的记录, 而不需要查询聚集索引中的记录

使用覆盖索引的好处是辅助索引不包含整行记录的所有信息

```bash
mysql> explain select count(*) from 9_6_2_buy_log ;
+----+-------------+---------------+-------+---------------+--------+---------+------+------+-------------+
| id | select_type | table         | type  | possible_keys | key    | key_len | ref  | rows | Extra       |
+----+-------------+---------------+-------+---------------+--------+---------+------+------+-------------+
|  1 | SIMPLE      | 9_6_2_buy_log | index | NULL          | userid | 4       | NULL |    7 | Using index |
+----+-------------+---------------+-------+---------------+--------+---------+------+------+-------------+
```

possible_key为null, 但是优化器还是选择了userid的索引, `Extra`的Using index 代表了优化器进行了覆盖索引操作

一般对于(a,b)这样的联合索引, 一般不可以选择b列为查询条件的, 但是对于统计操作, 如果是覆盖索引, 则优化器会进行选择

```bash
mysql> explain select count(*) from 9_6_2_buy_log where buy_date >='2011-01-01';
+----+-------------+---------------+-------+---------------+----------+---------+------+------+--------------------------+
| id | select_type | table         | type  | possible_keys | key      | key_len | ref  | rows | Extra                    |
+----+-------------+---------------+-------+---------------+----------+---------+------+------+--------------------------+
|  1 | SIMPLE      | 9_6_2_buy_log | index | NULL          | userid_2 | 8       | NULL |    7 | Using where; Using index |
+----+-------------+---------------+-------+---------------+----------+---------+------+------+--------------------------+
```

### 9.6.4 优化器选择不适用索引的情况

```bash
mysql > select * from orderdetails
    where orderid>10000 and orderid< 102000
```

在这种查询条件, 即使有orderid的单个索引,

sql优化器并不会按照orderId的索引来查找数据, 而是通过primary聚集索引

因为orderid索引不能覆盖到我们要查找的信息, 还需要对orderid索引进行查询后, 再去聚合索引里找信息

虽然orderid索引是有序的, 但是再去找聚合所以就是无序的

当访问数据量很少的时候 还是会选择辅助索引, 而当访问很大数据量时(一般是20%左右), 优化器会选择通过聚集索引来查找数据

当然也可以强制索引orderid索引

```bash
mysql > select * from orderdetails force index(orderId)
    where orderid>10000 and orderid< 102000
```

### 9.6.5 index hint

填充demo

```bash
create table 9_6_5_t (
    a int,
    b int,
    key (a),
    key (b)
) engine = innodb;

insert into 9_6_5_t  select 1, 1;
insert into 9_6_5_t  select 1, 2;
insert into 9_6_5_t  select 2, 3;
insert into 9_6_5_t  select 2, 4;
insert into 9_6_5_t  select 1, 2;

```

```bash
mysql> explain select * from  9_6_5_t where a=1 and b=2;
+----+-------------+---------+-------------+---------------+------+---------+------+------+------------------------------------------------+
| id | select_type | table   | type        | possible_keys | key  | key_len | ref  | rows | Extra                                          |
+----+-------------+---------+-------------+---------------+------+---------+------+------+------------------------------------------------+
|  1 | SIMPLE      | 9_6_5_t | index_merge | a,b           | b,a  | 5,5     | NULL |    1 | Using intersect(b,a); Using where; Using index |
+----+-------------+---------+-------------+---------------+------+---------+------+------+------------------------------------------------+
```

Using intersect(b,a)表示根据两个索引得到的结果进行求交的运算

使用 use index进行index hint

```bash
mysql> explain select * from  9_6_5_t use index(a) where a=1 and b=2;
+----+-------------+---------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table   | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+---------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | 9_6_5_t | ALL  | a             | NULL | NULL    | NULL |    5 | Using where |
+----+-------------+---------+------+---------------+------+---------+------+------+-------------+
```

优化器并不一定会按照我们的用a的index, 而使用了扫表的方式

如果要强制则需要使用 force index

index hit语法

```
tbl_name[ [as] alias ] [index_hint_list]
index_hint_list:
index_hint [, index_hint] ...
index_hint:
use {index|key}
[{for {join|order by| group by}] ([index_List])
| ignore {index|key}
[{for {join|order by| group by}] ([index_List])
| force {index|key}
[{for {join|order by| group by}] ([index_List])
index_list
index_name [, index_name]...
```

## 9.7 Multi-Range Read

MRR优化的目的: 减少磁盘的随机访问, 并且将随机访问转换为较为顺序的数据访问

MRR优化适用于: range, ref, eq_ref类型的查询





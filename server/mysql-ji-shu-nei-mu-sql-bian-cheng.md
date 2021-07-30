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
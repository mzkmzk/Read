# MySQL必知必会

## 1. 了解SQL

SQL: 结构化查询语言(Structured Query Language)


## 3. 使用MySQL

### 3.3 了解数据库和表

查询创建数据库的语句

```mysql
show create database mzk_test;
```

```
+----------+-------------------------------------------------------------------+
| Database | Create Database                                                   |
+----------+-------------------------------------------------------------------+
| mzk_test | CREATE DATABASE `mzk_test` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+-------------------------------------------------------------------+
```

查询创建表的语句

```bash
mysql> show create table 1_items\G;
*************************** 1. row ***************************
       Table: 1_items
Create Table: CREATE TABLE `1_items` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

## 4. SELECT语句

### 4.2 检索单个列

SQL语句大小写, SQL语句不区分大小写, 很多SQL开发人员习惯把SQL关键字大写, 列名和表名使用小写, 方便代码阅读

### 4.5 检索不同的行

假如我们想排查相同结果的列应该如何做, 

现有数据

```bash
mysql> select iid from 1_items_links;
+-----+
| iid |
+-----+
|   1 |
|   1 |
|   1 |
|   2 |
+-----+
```

排除相同数据

```bash
mysql> SELECT DISTINCT iid from 1_items_links;
+-----+
| iid |
+-----+
|   1 |
|   2 |
+-----+
```

​	tips: DISTINCT 应用于所有列, 而不只是前置它的列

当DISTINCT后面跟着多个字段, 则需多个字段的value都一样才算`重复值`

### 4.6 限制结果

```mysql
SELECT prod_name FROM products LIMIT 5,7
```

表示从第6(默认值为0, 第一行为行0)行开始, 检索7个符合结果的列(包括第6行)

但这个从0行开始算比较容易弄混淆, 一般都用OFFSET表示从第几行开始

OFFSET制定的数字就是从第1行开始

```mysql
SELECT prod_name FROM products LIMIT 7 OFFSET 6
```

## 6. 过滤数据

### 6.2 Where子句操作符

> 6.2.1 检查某个值
字符串`=`和`!=`比较是忽略大小写的

```mysql
SELECT prod_name FROM products WHERE prod_name = 'fuses';
```

这个SQL能匹配`fuses`也能匹配`Fuses`

> 6.2.3 范围值检查

```mysql
SELECT prod_name FROM products Where prod_price BETWEEN 5 AND 10
```

找出`prod_price`大于等于5和小于等于10的列

> 6.2.4 空值检查

创建表时, 可以设定表是否可以不包含值, 不包含的话就是NULL

找出空值
```mysql
select prod_name from products where prod_price is NULL;
```

## 7. 数据过滤

### 7.1 组合Where子句

> 7.1.3 计算次序

当包含任意数目的`OR`和`AND`, 会先处理AND操作, 然后再处理OR操作, 没有`()`的情况下

## 8. 用通配符过滤

### 8.1 like操作符

> 8.1.2 下划线`_`通配符

`_`表示只匹配单个字符

## 9.用正则表达式进行搜索

## 9.2 使用MySQL正则表达式

> 9.2.2 进行OR匹配

```sql
select prod_name from products where prod_name regexp '[123] Ton'
```

> 9.2.5匹配特殊字符

为了匹配特殊字符, 必须用`\\`作为前导, `\\-`为了找`-`, `\\.`为了找`.`

## 13 分组数据

### 13.3 过滤分组

GROUP BY用于分组数据, 可以用HAVING过滤分组

HAVING和WHERE非常类似, WHERE过滤的是行, HAVING过滤的是分组

例如要过滤加个大于10的商品, 最后的分组要过滤大于2个的分组

```sql
SELECT vend_id, count(*) as num prods
from products
where prod_price >= 10
group by vend_id
having count(*) >=2;
```

### 13.5 select自举顺序

- select
- from
- where
- group by
- having
- order by
- limit

## 15 联结表

### 15.2 创建联结

> 15.2.2 内部联结

基于两张表之间相等测试, 这种联结称为内部联结

```sql
select vend_name, prod_name, prod_price
from vendors inner join products
on vendors.vend_id = products.vend_id
```

## 16 创建高级联结

### 16.2 自联结

一般情况下 自联结的查询速度会比子查询要快很多

> 16.2.3 外部联结

以下场景会使用到外部联结

- 每个客户下了多少订单进行计数, 包括至今未下单的从客户
- 列出所有产品以及订购数量, 包括没人订购的产品

包含了那些相关表中没有关联行的行, 称为外部链接

外部联结有外部左链接和外部右联结

外部联结还包括没有关联行的行, 外部左联结表示会左边表里的所有行

```sql
select customers.cust_id, orders.order_num
from customers right outer join orders
on orders.cust_id = customers.cust_id;
```

## 17 组合查询

### 17.1 组合查询

以下两种基本情况, 需要使用组合查询

- 在单个查询中从不同的表返回类似结构的数据
- 对单个表执行多个查询, 按单个查询返回数据

### 17.2 UNION规则

- UNION中每个查询必须包含相同的列, 表达式或聚合函数(次序可不同)
- 列数据类型必须兼容, 类型不必完全相同, 但必须是DBMS可以隐式转换的类型



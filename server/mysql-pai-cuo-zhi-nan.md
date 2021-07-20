# MySQL排错指南

## 1. 基础

### 1.1 语法错误

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

### 1.2 SELECT返回错误结果

如果一个SELECT查询没有按预期返回结果, 那么可以拆成一小段, 逐段分析

例子一个`items`与`items_links` 多对多的关系

items表
```
+-------+---------+------+-----+---------+----------------+
| Field | Type    | Null | Key | Default | Extra          |
+-------+---------+------+-----+---------+----------------+
| id    | int(11) | NO   | PRI | NULL    | auto_increment |
+-------+---------+------+-----+---------+----------------+
```

items_links表

```
+--------+---------+------+-----+---------+-------+
| Field  | Type    | Null | Key | Default | Extra |
+--------+---------+------+-----+---------+-------+
| iid    | int(11) | NO   | MUL | NULL    |       |
| linkid | int(11) | NO   | MUL | NULL    |       |
+--------+---------+------+-----+---------+-------+
```


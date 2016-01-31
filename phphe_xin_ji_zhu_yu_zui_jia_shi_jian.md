# PHP核心技术与最佳实践

##1. 面向对象思想的核心概念

###1.1 源码定义变量和对象

变量源码定义:

```c
#zend/zend.h
typedef union_zvalue_value{
    long lval;/*long value*/
    double dval; /* double value */
    struct {
        char *val;
        int len;
    };
    HashTable *ht ; /* hash table value*/
    zend_object_value obj;
}zvalue_value;
```

对象源码定义:

```c
#zend/zend.h
typedef struct_zend_object {
    zend_class_entry *ce;//类的入口
    HashTable *properties; //属性组成的HashTable
    HashTable *guards;/*防止递归调用的 protects from __get/__set ...recursion */
}zend_object
```

对象的组成:

![对象的组成](QQ20160129-1.png)

###1.2 对象和数组

序列化后对象和数组的区别

```php
//对象
$str = serialize($student);
    0:6:"Student":2:{s:4:"name";s:3:"Tom";s:6:"gender";s:"mail";}

$student_arr = array("name"=>"Tom","gender"=>"male");

//数组
$str_arr = serialize($student_arr);
     a:2:{s:4:"name";s:3:"Tom";s:6:"gender";s:"mail";}
```

序列化后,对象和数组的区别只是对象多一个指针指向它所属的类.

##2. PHP与数据库

###2.1 SQL基本优化

1. 避免在列上,会导致索引失败

    ```sql
    select * from t where year(d) >=2011;
    
    //优化为
    select  * from t where d > = '2011-01-01';
    ```
2. 使用JOIN时,应该用小结果集驱动大结果集,同时把复杂的JOIN查询拆分为多个Query,因为JOIN多个表,可能导致更多的锁定和堵塞.
    
    ```sql
    select * from a join on a.id = b.id
    left join c on c.time = a.date
    left join d on c.pid = b.aid
    left join e on e.cid = a.did
    ```
3. 模糊like查询,避免%%

    ```sql
    select * from t where name like = '%de%';
    
    //优化为: 但是中文就不知道怎么玩了......
    select  * from t where name > = 'de' and name < 'df';
    ```
4. 仅列出所需的查询字段,少用*号,对速度不会有明显影响,但会影响内存.
5. 使用批量插入语句节省交互.
6. limit的基数比较大的使用使用between.

    ```sql
    select * from t order by id limit 1000000,10;
    
    //优化为
    select * from t where id between 1000000 and 1000010 order by id;
    ```
    
    但是between也有缺陷,如果id中间有断行,或者中间部分id不读取的话,总读取的数量会少于预计数量,在取比较厚的数据用desc方向查找.
7. 避免使用NULL
8. 不要使用count(id),而是count(*)
9. 不要做无谓排序,应该在索引时中完成排序.
10. 多explain select * ..查询

    ```sql
    explain select id from patients where created_at > '2015-11-11';
    ```
    ![explain](QQ20160131-2.png)
    ```sql
    explain select id from patients where id > 0 ;
    ```
    ![explain](QQ20160131-3.png)
    
    注意,主要优化一般都看type,索引和没索引,查询时最少应该达到range级别,最好达到ref,下面列举下type从好到坏的结果
    
    1. system(系统表)
    2. const(读常量)
    3. eq_ref(最多一条匹配结果,通过是通过主键访问)
    4. ref(被驱动表索引引用)
    5. fulltext(全文索引检索)
    6. ref_or_null(带有空值的索引查询)
    7. index_merge(合并索引结果集)
    8. unique_subquery(子查询中返回的字段是唯一组合或索引)
    9. index_subquery(子查询返回的是索引,但非主键)
    10. range(索引范围扫描)
    11. index(全索引扫描)
    12. ALL(全表扫描).
    
    建索引一般的原则: 
    1. 我在关键字段设计索引
    2. 建索引的字段和结果集最好分布均匀,或许符合正态分布
    3. 不太结果集中结果单一的列上建立索引(例如性别列,一般只有01)
    
###2.2 MySQL服务器调整优化措施

1. 关闭不必要的日志

    ```sql
    //查看日志的打开情况.
    show variables like '%slow%';
    
    //定期打开慢查询条数,方便优化.
    show global status like '%slow%';
    ```
2. 增加MySQL允许最大连接数

    ```sql
    //查看最大连接数
    show variables like 'max_connections';
    ```
    
##2.3 MySQL瓶颈及应对措施

MySQL单表达到千万级以上,无论如何优化,查询都会很慢.

1. 增加MySQL配置的buffer和Cache,
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

##@. PHP与数据库


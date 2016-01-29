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
    
}
```
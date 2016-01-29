# PHP核心技术与最佳实践

##1. 面向对象思想的核心概念

###1.1 源码定义变量和对象

```c
#zend/zend.h
typedef union_zvalue_value{
    long lval;/*long value*/
    
}
```
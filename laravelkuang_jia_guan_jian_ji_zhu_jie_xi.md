# Laravel框架关键技术解析

# 1. 组件化开发与composer使用

主要手把手教 Route Controller Model View 逐个安装和使用

# 2. Laravel框架安装与调试环境建立

额..怎么说..这里教了怎么装Laravel..

# 3. Laravel框架中常用的PHP语法

## 3.1 组件化开发语法条件

### 3.1.2 文件包含

1. include和require基本相同,只是处理失败方式不同,require会抛出E_COMPILE_ERROR语法错误,脚本会终止,而include只会E_WARNING错误,程序会继续执行

## 3.3 PHP特殊语法

### 3.3.1 魔术方法

1. __construct()
2. __destruct()
3. __set()
4. __get()
5. __isset(): 对不可访问属性调用isset()或empty()时,__isset被触发
6. __unsset(): 当对不可访问属性调用unsset()时
7. __sleep(): serialize()检查类中是否有魔术方法__sleep(),如果存在,该函数将在任何序列化之前运行,它可以清除对象并返回一个包含该对象呗序列化的所有变量名的数组,如果该函数没有返回什么数据，则不会有什么数据被序列化，并且会发出一个 E_NOTICE 错误。
8. __wakeup(): 
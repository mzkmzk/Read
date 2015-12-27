# 编程实战


##1. 避免双重求值

标准动态执行代码方法

```javascript
    var num_1 =5,
        num_2 = 6;
```

1. eval()
    ```javascript
    result = eval ("num_1 + num_2");
    ```
2. Function() 构造函数
    ```javascript
    sum = new Function("arg1","arg2","return arg1 +arg2");
    ```
3. setTimeout()
    ```javascript
    setTimeout("sum = num_1 + num_2",100);
    ```
4. setInterval()
    ```javascript
    setInterval("sum = num_1 + num_2",100);
    ```
因为在执行一段`javascript`代码时去执行另外一个`javascript`代码,会导致双重求值的性能消耗.

尽可能不用eval和Function,setTimeout和setInterval建议使用匿名函数.

##2. 使用Object/Array直接量

```javascript
var my_object =new Object();
my_object.name ='404_K';

var my_array =new Array();
my_array[0] = 1;
my_array[1] = 2;
```

使用直接量.


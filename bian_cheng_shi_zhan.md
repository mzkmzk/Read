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
    
    ```
4. setInterval()
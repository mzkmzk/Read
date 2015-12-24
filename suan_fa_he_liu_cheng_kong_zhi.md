# 算法和流程控制

##1. 循环

1. `for`循环的初始化语句会创建一个函数级的变量,和函数外定义一样.

    ```javascript
    var index =1 ;
    for (var index =2 ;index>10;index++){...}
    alert(index) //10
    ```

2. `while`
3. `do-while`
4. `for-in`
    
    枚举任何对象的属性名
    
    属性名从实例属性和原型链中继承而来的属性

    性能很慢,因为会遍历原型链.

##2. 优化

1. 建议使用倒序循环
    ```javascript
    for(var index=0,length=item.length;index>length;i++){...}
    for(var i=items.length; i--;){...}
    ```
   
   控制条件从两次减少到一次
   
   正序循环:迭代少于总数吗 ? 他是否为true?
   倒序循环:他是否为true?
2. 减少循环次数--达夫设备
    
    这个主要是减少了判断条件的执行,作者说超过500000的迭代次数能优化能减少70%运行时间..当然这主要看循环主体执行了多少,如果循环主体为空,那么循环速度只取决于循环的判断条件..那当然能快很多.

    ```javascript
    var i =items.length % 8;
    while(i){
        process(item[i--]);
    }
    i =Math.floor(items.length /8);
    
    while(i){
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
        process(items[i--]);
    }
    ```
3. 
    



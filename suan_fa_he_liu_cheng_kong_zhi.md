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
    
    将上述方法设置一个通用的Utils,然后要循环时用这个Utils方法,应该还不错,不过要把执行的主题封装为方法.还是不错的.
3. `if-else` vs `swith`

    建议多使用swith,因为swith使用的是全等比较符,不会产生类型转换的消耗.
    
4. 优化`if-else`

    这个不太建议,易读性会变差.
    ```javascript
    if(value==0 ){
        return result0;
    }else if(value ==1){
        ...
    }...else if(value ==10){
        ...
    }
    ```
    这样每次到10都要10次判断,可以根据实际情况优化
    
    ```javascript
    if(value<6){
        if(value <3){
            if(value ==0){
                ...
            }...
        }
        else {
            if(value ==3){
                ...
            }...
        }
    } else ...
    ```

5. 查找表

    通过数组/普通对象构建表,比`if-else` & `swith`快多了.
    
    还是value = 0~10的判断
    
    ```javascript
    var result[result0...result10];
    return result[value];
    ```
    但这比较适合`key:value`一一对应时,当每一个`swith`都对应不同的操作,就不能用这个了.
    
6. 迭代 vs 递归

    尽量能迭代不递归,因为容易爆栈
    
    在不爆栈的情况下,递归性能更优,因为省去了迭代的参数记录.
    
7. Memoization
    
    使用缓存的递归..

    以`factorial`为例.
    
    ```javascript
    function memfactorial(n){
        if(!mefactorial.cache){
            memfactorial.cache = {
                "0" : 1,
                "1" : 1
            };
        }
        
        if(!memfactorial.cache.hasOwnProperty(n)){
            memfactorial.cache[n] = n * memfactorial(n-1);
        }
        
        return memfactorial.cache[n];
    }
    
    var fact6 =memfactorial(6);
    var fact6 =memfactorial(5);
    var fact6 =memfactorial(4);
    ```
    
    这是一个越用越优的循环.
    
    差一点,但能通用的Memoization.
    
    ```javascript
    function memoize(fundamental,cahce){
        cache = cache || {};
        var shell = function(arg){
            if(!cache.hasOwnProperty(arg)){
                cache[arg] = fundamental(arg);
            }
        }
        return shell;
    }
    
    //缓存
    var memfactorial =memoize(factorial,{"0":1,"1":1});
    var fact6 =memfactorial(6);
    var fact6 =memfactorial(5);
    var fact6 =memfactorial(4);
    ```
    
    这个比之前专门的优化要差一点,因为memoize只会存储最终的结果,中间的计算过程并不会存储.
    
    在这个例子中,6,5,4的调用,后者循环作用基本是没有的,只有缓存`0`和`1`用到了.
    
    但是第一个方法在计算memfactorial(6)就把前面中间计算的5,4,3,2都缓存下来了,所以可以直接查询.
    



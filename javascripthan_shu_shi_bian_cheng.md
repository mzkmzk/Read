# JavaScript函数式编程

## 1. JavaScript函数式编程简介

函数式编程: 通过使用函数来将值转换成抽象单元,接着用于构建软件系统

实践中的函数式编程并不以消除状态改变为主要目的,而是将任何已知系统中突变的出现尽量压缩到最小区域中去

函数式编程

1. 一个队`存在`的抽象函数的定义
2. 一个建立在存在函数智商的,对`真`的抽象函数的定义
3. 通过其他函数来使用上面的两个函数,以实现更多的行为  

## 2. 一等函数与Application编程

用100个函数操作一个数据结构,要比10个函数控制10个数据结构要好

高阶函数至少会有的动作

1. 以一个函数为参数
2. 以返回一个函数作为结果 

函数式编程最常用的方法

1. map: 遍历集合并对每一个值调用一个函数,返回结果的集合
2. reduce: 利用函数将值的集合合并成一个值,该函数接受一个累计值和本次处理的值
3. filter: 对集合每一个值调用一个返回boolean的函数,抽取返回true的值的集合
4. find: 接受集合和返回Boolean的函数,返回第一个为true的元素   
5. reject: 与find反向查找
6. all: 接受一个集合和return Boolean的函数,全部true则最终返回true
7. any: 接受一个集合和return Boolean的函数,有一个true则返回true
8. sortBy: 接受一个集合和一个返回比较条件的函数
9. groupBy: 接受一个集合和一个条件函数,并返回一个对象,键为传入函数所返回的条件,值为与其对应的元素

      ```javascript
      var albums = [
        {title: "K_title_1",genre: "Metal"},
        {title: "K_title_2",genre: "Metal"},
        {title: "F_Title_1",genre: "Fuck"},
      ]
      _.groupBy(albums,function(a){ return a.genre});
      //返回值为 =>
      {
        Metal: [
          {title: "K_title_1",genre: "Metal"},
          {title: "K_title_2",genre: "Metal"},
        ]
        Fuck: [
          {title: "F_Title_1",genre: "Fuck"},
        ]
      }
      
      ```
10. countBy:  返回groupBy对象的结果总和数

      ```javascript
      _.countBy(alums, function(a) {return a.genre});
      //=> {Metal: 2,Fuck: 1}
      ```
11. keys: 获取所有key
12. values: 获取所有value
13. pluck: 获取指定字段
14. omit: 筛选不要的字段
15. pick: 获取要的指定的一个/多个字段
16. findWhere: 可指定多个字段的值,筛选出第一个与之对应的值
17. where: 与findWhere泪水,但返回所有符合的值

## 3.变量的作用域和闭包

### 3.1 全局作用域

没用var声明/在顶级的变量都是全局变量

 ### 3.2 词法作用域
 
 词法作用域值一个变量的可见性,及其文本表示的模拟值.
 
 ### 3.3 动态作用域
 
 谁调用的,谁就是this
 
 ### 3.4 函数作用域
 
 函数内的var不能被外部访问
 
 ### 3.5闭包
 
 闭包: 闭包就是一个函数,捕获作用域内的外部绑定(例如: 不是自己的参数),这些绑定是为了之后(即使在该作用域已结束)使用而被定义的
 
 最简单的闭包
 
 ```javascript
 function whatWasTheLocal(){
   var CAPTURED ="oh hai";
   
   return function() {
     return "The local was: "+ CAPTURED;
   }
 }
 var reportLocal = whatWasTheLocal();
 reportLocal(); //"The local was oh hai"
 ```
 
简单地说: 在调用完whatWasTheLocal后,还能使用她内部定义的变量

自由变量是指能通过闭包比外部访问的变量.

闭包作用: 闭包允许你在创建函数时做一些配置

例如

```javascript
function createScaleFunction(FACTOR) {
  return function(v) {
    return _.map(v.function(n)) {
      return (n * FACTOR);
    });
  }
}

var scale10 = createScaleFunction(10);
scale10([1,2]); //=>[10,20]

var scale20 = createScaleFunction(20);
scale10([1,2]); //=>[20,40]
```

## 4. 高阶函数

使用函数 而不是值

最常用的函数式编程设计

忽略其参数并只返回一个常量的函数

```javascript
function always(VALUE) {
  return function() {
    return VALUE;
  }
}
```

可以在调用闭包时传递不一样的函数

```javascript
var f = always(function(){});
var g = always(function(){});

f() === f() //true
f() === g() //false
```


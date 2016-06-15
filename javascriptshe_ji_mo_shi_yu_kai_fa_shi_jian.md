# JavaScript设计模式与开发实践

多态思想为了解决`做什么`和`谁去做`分离开来.

JavaScript不存在类型耦合,像鸡和鸭要用同一个方法发出声音.

Java的做法:

1. 继承相同父类
2. 各子类实现方法
3. 在测试代码调用同一方法

JavaScript

1. 各模型实现方法
2. 在测试代码中调用同一方法

因为JavaScript不存在类型判断,只取决于你有/没有这个方法即可.

一般在调用方法时候,判断有无这个方法即可

```javascript
类.方法 instanceof Function

or

typeof 类.方法 === 'function'
```

如果`true`那就放心的调用吧.

#1. 原型模式

原型模式是吧本身克隆一下,然后在原型加功能

使用`Object.create`克隆原型.

```javascript
Object.create =  Object.create || function(obj){
    var F = function(){};
    F.prototype = obj;
    return new F();
}
```

原型判断

```javascript
var obj = {};
Object.getPrototypeOf(obj) === Object.prototype; //true
```

#2 this call apply

##2.1 构造器

如果构造器显式的返回一个object类型对象,那么此次运算最终返回这个对象,而非this,但是返回非对象的数据还是还会返回this

```javascript
var My_Class = function (){
    this.name = 'k';
    return {
        name : '404';
    }
}
var obj = new My_Class();
console.log(obj.name);//404

var My_Class2 = function (){
    this.name = 'k';
    return '404';
}
var obj_2 = new My_Class_2();
console.log(obj_2.name);//'k'
```

##2.2 绑定this

有些浏览器自带`bind`,这里实现一下

```javascript
Function.prototype.bind = function(){
    var self =this,
        context = [].shift.call(arguments),
        args = [].slice.call(arguments);
    return function(){
        //args代表func函数bind后面的参数1,2,而[].slice.call(arguments),为克隆3,4参数.
        return self.apply(context,[].concat.call(args,[].slice.call(arguments)));
    }
}

var abj = {
    name : 'sven';
}

var func = function(a,b,c,d){
    alert(this,name); // sven
    alert([a,b,c,d]); //[1,2,3,4]
}.bind(obj,1,2);

func(3,4);
```
#3 闭包

```javascript
for(var index = nodes.length ;index--;){
    nodes[index].onclick = function(){
        console.log(index);
    }
}
```
但是最后出来结果全是5,因为onclick是异步执行的.

利用闭包

```javascript
for (var index = nodes.length;index--;){
    (function(index){
        nodes[index].onclick =function(){
            console.log(index);
        }
    })(index);
}
```

## 3.1闭包

##3.1.3 延长局部变量寿命

```javascript
var report = function(src){
    var img = new Image();
    img.src=src;
}
```
上传经常会不成功,因为在上传过程中,在img数据还没发出去之间,方法就销毁了.
```javascript
var report = function(){
    var imgs = [];
    return function(src){
        var img =new Image();
        imgs.push(img);
        img.src=src;
    }
}
```

##3.1.4 面向闭包开发

```javascript
var Extent =function (){
    this.value = 0;
}

Extent.prototype.call = function(){
    this.value++;
    console.log(this.value);
}

var extent = new Extent();
extent.call(); //1
extent.call(); //2
extent.call(); //3
```
## 3.2 高阶函数

高阶函数至少满足一下条件之一

1. 函数可以作为参数被传递
2. 函数可以作为返回值输出

### 3.2.4 高阶函数的应用

1. currying 函数柯里化

  例如每日都会记录利润,然后需要总利润输出时,再输出.
  
  ```javascript
  //令函数柯里化通用方法
  var curring = function(fn) {
    var args = []; // 用于保存调用过的参数.
    return function(){
      if (arguments.length === 0) {
        return fn.apply(this,args); //把历史性的参数都调用起来
      } else {
        [].push.apply(args,arguments);
        return argumets.callee; //返回函数本身,即return的这个闭包 ES5严格模式已不允许使用该方法
      }
    }
  }
  
  var cost = ( function() {
    var money = 0 ;
    
    return function(){
      for (var i = 0; l = arguments.length;i < 1 ; i++){
        money +=arguments[i];
      }
      return money
    }
  })();
  
  //应用
  var cost = curring(cost);
  cost(100);
  cost(200);
  cost(300);
  
  const(); //600
  ```
2. uncurrying: 反柯里化  

  ```javascript
  //实现
  Function.prototype.uncurring = function() {
    var self = this ; //保存当时的this这里是Array.prototype.push
    return function() {
      var obj = Array.prototype.shift.call(arguments);//去除并返回第一个参数
      return self.apply(obj,arguments);
    }
  }
  //运用
  var push = Array.prototype.push.uncurring();
  var obj = {
    "length": 1,
    "0": 1
  };
  push(obj,2);
  console.log(obj);//{0: 1,1: 2,length: 2}
  ```
3. 函数节流

   例如,resize函数触发N多次,但是我们可能只需1或0.5秒执行一次
   
   ```javascript
   var throttle = function (fn,interval) {
     var __self = fn,
     timer,
     fisrtTime = true,
     
     return function() {
       var args = arguments,
       __me = this,
       
       if(firstTime) { //第一次不延迟
         __self.apply(_me,args);
         return firstTime = false;
       }
       
       if (timer) { //如果有定时器,则说明上次没完成,取消这次行为
         return false;
       }
       
       timer = setTimeout(function() {
         clearTimeout(timer);
         timer = null;
         __self.apply(__me,args);
       }, interval || 500)
     }
   }
   ```
4. 分时函数

    eg: 一次性加载1000000次循环时(实时不应该怎么)
    
    ```javascript
    var timeChunk = function(ary,fn,count){
      var obj,
        t,
        len = ary.length;
        
        var start = function() {
          for (var i = 0;i< Math.min(count || 1 ,arr.length);i++) {
            var obj =ary.shift();
            fn(obj);
          }
        }
    };
    ```

#15 装饰者模式

装饰者模式: 往对象动态添加职责

## 15.5 用AOP装饰函数

```javascript
Function.prototype.before = function(beforn_fn){
  var __self = this; //保存原函数的引用
  return function(){
    before_fn.apply(this,arguments); //执行新函数
    return __self.apply(this,arguments); //返回原函数并返回原函数的执行结果.
  }
}

Function.prototype.after = function(after_fn) {
  var __self = this;
  return function() {
    var ret = __self.apply(this,arguments);
    after_fn.apply(this,arguments);
    return ret;
  }
}
```

这种AOP,因为before和原来执行的函数公用arguments,那么before还可以动态给原函数添加参数.

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
        
        return function(){
          t = setInterval(function(){
            if (ary.length === 0) { //假如数组已经执行完毕
              return clearInterval(t);
            }
            start();
          },200)
        }
    };
    ```
    //这个方法如果fn里含有this,会有问题.
    
5. 惰性加载函数

     例如绑定时间时,不同浏览器要使用window.addEventListener(正常刘看齐) || window
     .attachEvent(IE8及其以下浏览器)
     
     ```javascript
     var addEvent = function(elem,type,handler) {
       if (winodw.addEventListener) {
         addEvent = function(elem,type,handler) {
           elem.addEventListener(type,handler,false);
         }
       } else if (window.attachEvent) {
         addEvent = function(elem,type,handler) {
           elem.attachEvent('on'+type,handler);
         }
       }     
       addEvent(elem,type,handler); 
     }
     ```
     
     这样以后执行addEvent都不需要再去判断IE还是其他浏览的绑定方式
     
     
#4 单例模式

## 4.6 通用惰性模式

用于创建一次,但是又不需要刚打开页面就执行的函数

```javascript
var getSingle = function(fn) {
  var result; //形成闭包,在return后的函数仍可访问result
  return function(){
    return result || (result = fn.apply(this.arguments));
  }
}

//例如全局只有一个的登陆弹出框
var createLiginLayer = function(){
  var div = document.createElements("div");
  div.innerHTML = "我是登录框";
  div.style.display = 'none';
  document.body.appendChild('div');
  return div;
}

var createSingleLoginLayer = getSingle(createLiginLayer);

document.getElementById("loginBtn").onclick = function(){
  var loginLayer = createSingleLoginLayer(); //第一次点击 创建div,第二次点击返回第一次的div
  loginLayer.style.display = 'block';
}
```

# 5 策略模式

策略模式: 定义一系列的算法,把他们一个个封装起来,并且使他们可以相互切换

## 5.7 策略模式有点

1. 可以利用组合,委托,和多台,有效避免多重条件的选择

# 6 代理模式

你要约明显啪啪啪.你不可能直接约到她啊..先找它的经理人(代理),谈好价格后,然后上.ok,这就是代理模式


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

 //缓存 factorial是具体的实现方法
 var memfactorial =memoize(factorial,{"0":1,"1":1});
 var fact6 =memfactorial(6);
 var fact5 =memfactorial(5);
 var fact4 =memfactorial(4);
```

在这里我们没有显示的调用factorial算法,

而是给它加了一层缓存,递归到以前查询过的数字,直接读缓存即可.

# 7 迭代器模式

迭代器模式: 提供一种顺序访问一个聚合对象中的各个元素,又不需要暴露该对象的内部实现

# 8 发布-订阅模式

一般可以用于

ajax,当ajax有返回后,需要执行多个任务,就可以触发一个发布,其他函数订阅即可

下面代码支持

1. 全局事件
2. 可先发布,后订阅

## 8.11 

### 8.11.1 使用

稍后会解释源码,现在先看怎么使用

```javascript
//先发布后订阅
Event.trigger("click",1);
Event.listen('click',function(a){
  console.log(a);
})

//使用命名空间
Event.create("namespace1").listen('click',function (a) {
  console.log(a);//1
})

Event.create("namespace1").tigger("click",1);
```

### 8.11.2 Event全局事件

```javascript
var Event = (function(){
  var global = this,
  Event,
  _default = 'default';
  Event = function(){ //初始化
    var _listen,
        _trigger,
        _remove,
        _slice = Array.prototype.slice, //截取数组
        _shift = Array.prototype.shift, //去除第一个元素并返回第一个元素
        _unshift = Array.prototype.unshift,
        namespaceCache = {},
        _create,
        find,
        each = function (ary,fn) {
          var ret;
          for (var i= 0,i = ary.length;i<l,i++) {
            var n = ary[i];
            ret = fn.call(n,i,n);
          }
          return ret;
        }
        
        _listen = function(key,fn,cache) {
          if (!cache[key]) {
            cache[key] = [];
          }
          cache[key].push(fn);
        };
        
        _remove = function(key,cache,fn) {
          if(cache[key]) {
            if (fn) {
              for (var i = cache[key].length;i>=0;i--) {
                if (cache[ key ] === fn ) {
                  cache[key].splice(i,1);
                }
              }
            } else {
              cache[ key ] = [];
            }
          }
        };
        
        _tigger = function() {
          var cache = _shift.call(arguments),
                key = _shift_call(arguments),
                arg = arguments,
                _self = this ,
                ret,
                stack =cache[key];
          if( !stack || ! stack.length) {
            return ;
          }
          return each(stack,function(){
            return this.apply(_self,args);
          });
        };
        
        _create = function(namespace) {
          var namespace || _default;
          var cache = {},
          offlineStack = [], //离线事件
          ret = {
            listen: function( key, fn, last) {
              _listen(key,fn,last);
              if(offlineStack === null ) {
                return ;
              }
              if( last === 'last') {
                offlineStack.length && offlineStack.pop(){};
              } else {
                each( offlineStack, function(){
                  this();
                });
              }
              offlineStack = null;
            },
            one: function(key,fn,last) {
              _remove(key,cache);
              this.listen(key,cachemfn);
            },
            remove: function(key,fn) {
              _remove(key,cache,fn);
            },
            tigger: function() {
              var fn,
                  args,
                  _self = this;
                  
                  _unshift.call(arguments,cache);
                  args = arguments;
                  fn = function() {
                    return _trigger.apply(_self,args);
                  };
                  
                  if (offlineStack) {
                    return offlineStack.push(fn);
                  }
                  return fn();
            }
          };
          
          return namespace ? 
                ( namespaceCache[namespace] ? namespaceCache[namespace] : namespaceCache[ namespace] = ret) : ret;
        };
        
  return {
    create: _create,
    one : function(key,fn,last){
      var event = this.create();
      event.one(key,fn,last);
    },
    remove: function (key,fn) {
      var event = this.create();
      event.remove(key,fn);
    },
    listen: function(key,fn,last) {
      var event = this.create();
      event.listen(key,fn,last);
    },
    tigger: function(){
      var event = this.create();
      event.trigger.apply(this,arguments);
    }
  };      
  }();
  return Event;
})()
```

#9 命令模式

命令模式最常见的应用场景是: 有时候需要向某些对象发送请求,但是并不知道请求的接受者是谁,也不知道请求的操作是什么

设计模式总是把不变的事物和变化的事物分开

主要示例有: 执行命令、撤销命名、批量执行命令、队列

#10 组合模式

应用情况

1. 树形结构

#11 模板方法模式

就是方法尽量粒子化,公用提升到父类

但是javascript没有接口这么方法重写检查,解决办法

1. 鸭子模型接口检查
2. 在父类中抛出异常.

#12 享元模式

主要用于性能优化,当系统创建大量类似的对象,可以使用享元模式

主要区分内部状态和外部状态,内部状态可共享,外部状态不可共享

1. 内部状态存储于对象内部
2. 内部状态可以被一些对象共享
3. 内部状态独立于具体的场景,通常不会变
4. 外部状态取决于具体的场景,并根据场景变化而变化,外部状态不能共享

示例: 对象池

```javascript
var objectPoolFactory =  function(createObjFn) {
  var objectPool = [];
  return {
    create: function(){
      var obj = objectPool.length === 0 ?
        createObjFn.apply(this,arguments) : objectPool.shift();
    },
    recover: function(obj) {
      objectPool.push(obj);
    }
  }
};

//应用
装在一些iframe对象池
var iframeFactory = objectPoolFacotry(function(){
  var iframe = document.createElements("iframe");
    document.body.appendChild(iframe);
    
    iframe.onload = function(){
      iframe.onload = null //防止iframe重复加载
      iframeFactory.recover(iframe); // iframe加载完成后回收节点
    }
});

var iframe1 = iframeFacotry.create();
iframe.src = "http://baidu.com"

var iframe2 = iframeFactory.create();
iframe.src = "http://qq.com"
```

#13 职责链模式

可以用于解决一大顿if else

封装为特定的职责

#14 中介者模式

如果多个对象耦合太严重

引用中介者来中心化处理



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

# 16 状态模式

状态模式的关键是区分事务内部的状态,事务内部状态的改变往往会带来事物的行为改变

把状态的各种行为封装为状态类,而本体只记录相关的状态,当本体状态改变时,触发想对应的行为

状态机库: github: javascript-state-machine

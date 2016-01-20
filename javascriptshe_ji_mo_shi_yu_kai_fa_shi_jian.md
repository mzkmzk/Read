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

##1. 原型模式

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

##2 this call apply

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
##3 闭包

```javascript
for(var l)

```

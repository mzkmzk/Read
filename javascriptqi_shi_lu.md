# JavaScripte启o示f录

# 1. JavaScript对象

## 1.2 JavaScript构造函数并返回对象实例

只要使用new调用某函数,就会将该函数的this设置为正在构建的新对象,并默认返回新创建的对象(this)

## 1.16 typeof操作符

注意点

```javascript

typeof null //object

var myFunction = new Function('x','y','return x* y')
typeof myFunction //function 

var myRegExp = new RegEXP('\abc\')
typeof myRegExp // function

```

其他一般情况下 typeof基本类型 返回的是基本类型 非基本类型的都是object

基本类型一般是指非new出来的基本类型例如

1. var a = String('123') //基本类型 typeof 后为string
2. var b = new String('123') //复杂类型 typeof 后为object

## 1.18 构造函数实例都拥有指向其构造函数的Constructor属性

所有字变量/new方式出来的变量都会对应其constructor

```javascript
var myNumber = new Number('23')
var myNumberL = 23

myNumber.constructor === Number //true
myNumberL.constructor ===Number //true
```

## 1.19 验证对象是否是特定构造函数的实例

和constructor的验证不一样

自变量的instanceof 不会对应着包装类

```javascript
111 instanceof Number //fasle

var num = Number(111)

num instanceof Number //false

var numNew = new Number('111')

numNew instanceof Number //true
```

# 2 对象与属性

## 2.4 删除对象属性

delete不会删除在原型上找到的属性

# 4 Function

## 4.10 函数实例的length属性和arguments.length

```javascript
var myFunction = function (a,b,c) {
  return arguments.length
}

muFunction() //0

var myFunction1 = function (a,b,c) {
  return myFunction1.length 
}

myFunction1() //3

```

## 4.18 函数可以嵌套

在ES3和javascript1.5的函数嵌套中的this是window

```javascript
var a= function () {
   console.log('a')
   console.log(this)//window
    var b= function (){
      console.log('b')
        console.log(this)//window
      var c = function (){
        console.log('c')
        console.log(this)//window
      }
      c()
    }
     b()
     
}
```

其实这个比较好懂 如果在最外层

a的this是window

而后面this就一直没变

## 4.19 函数提升

被定义为函数表达式的函数没有被提升,只有函数语句被提升


```javascript
sum() //1
function sum (){
  return 1
}

sum2() //Uncaught TypeError: sum2 is not a function(…)

var sum2 = function(){
  return 2
}
```

# 5. 全局对象

显示调用全局对象方法比隐式要慢

```javascript
window.alert(1)//慢
alert(2)//快
```

笔者认为在自定义的全局函数里,必须要显式的写.

因为如果自定义的函数绑定在全局上

而不显示的写window,代码会变得非常难读

这点性能应该可以忽略


# 6. this

## 6.3 在嵌套函数中的this关键引用head对象

在ES3中函数嵌套容易丢失this

```javascript
var myObject = {
  func1: function() {
    console.log(this) //myObject
    var func2 = function() {
      console.log(this)//ES3: window /ES5 :myObject 
    }();
  }
}
myObject.func1()
```
但是笔者在chrome试还是丢失了this的,

## 6.6 在自定义构造函数中使用this

如果没有使用new 构造函数(),this将变成window

```javascript

var Person = function (){console.log(this)}

Person()//window

new Person()//Person{}

var a = {
  b: function(){
  console.log(this) //Object{} 
    Person() //window 
  }
}

a.b()

```

# 7 作用域和闭包

## 7.6 函数定义时确定作用域,而非调用时确定

# 8. 函数原型属性

## 8.4 默认的prototype属性是object对象

```javascript
var myFunction = function(){}

myFunction.prototype = {} //浏览器自动执行的

console.log(myFunction.prototype) //object{}
```

## 8.5 讲构造函数创建的实例链接至构造函数的prototype属性

原型链将每个实例都链接至构造函数的prototype属性

即实例.__proto === 构造函数.prototype

```javascript
Array.prototype.foo = 'foo';
var myArray = [];
console.log(myArray.__proto__ === Array.prototype) //true
console.log(myArray.__proto__.foo) //foo
```







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

笔者认为这里是存在争议的.
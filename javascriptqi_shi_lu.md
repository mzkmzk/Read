# JavaScript启示录

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



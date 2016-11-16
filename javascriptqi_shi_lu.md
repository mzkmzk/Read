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
# JavaScript高级程序设计

Javascript组成

1. ECMAScript
2. DOM(文档对象模型)
3. BOM(浏览器对象模型)


##1. 基础

#1.1 `<script>`元素

1. async: 下载脚本时,异步执行,不阻塞其他页面渲染.
2. defer: 是否延迟到文档完全解析和显示完再执行(但下载还是会顺序下载).

##1.2 typeof可能返回的值

1. undefined
2. boolean
3. string
4. number
5. object
6. function

`typeof null` 大部分浏览器会返回`object`,而部分返回`function`

##1.3 undefined

未声明和未定义

```javascript
var message;
alert(message);//undefined
alert(age); //报错

alert(typeof message);//undefined
alert(typeof age);//undefined;
```
##1.4 Object

1. hasOwnProperty(propertyName),判断属性是否在当前实例当中,而非原型链.
2. isPrototypeOf(obbject),检测参数对象是否为本对象的原型.

##1.5 逻辑或

1. 如果第一个操作数是对象,返回第一个操作数
2. 如果第一个操作数值为false,返回第二个操作数
3. 如果两个操作数都是对象,返回第一个参数.

##1.6 关系操作符

1. 当操作数是对象时,先执行valueOf(),如果没有此方法执行toString().

##1.7 没有块状作用域

体现在if for当中

```javascript
if(true){
    var name = 'K';
}
console.log(name); //K ,name作为了全局变量 for 也一样.
```

##1.7 数组

1. join('分隔符') toString()
    
    toString方法默认输出,分割的数组字符串,join就可以控制分隔符
2. push和pop方法实现栈,后进先去
3. push和shift(取出最前端的任意元素.)实现队列,先进先出.而pop和unshift(在最前端加任意元素..)能实现相反方向的队列.
4. 排序,当一个数组都存着人名和age的对象,要根据age升序排序

    ```javascript
    var items = [
        {name : 'K' , age :12},
        {name : '404' , age =21}
    ];
    items.sort(function(a,b)){
        return a.age-b.age;
    }
    ```
5. concat方法,参数为空,复制副本,若有参数,参数放在数组后面并返回,不会影响原来的数组.
6. slice(开始位置,结束位置)截取字符串
7. splice(起始位置,删除的项数,要插入的任意多项)
8. 
    

# JavaScript高级程序设计

Javascript组成

1. ECMAScript
2. DOM(文档对象模型)
3. BOM(浏览器对象模型)


#1. 基础

##1.1 `<script>`元素

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
    
    哎呀我操,那我是不是比较每一个属性都写一个这样的匿名函数啊...我不...
    
    设置一个函数,每次只要传递给他要比较的属性即可
    
    ```javascript
    function createComparisonFunction(propertyName){
        return function(object1,object2){
            var value1 = object1[propertyName];
            var value2 = object2[propertyName];
            return value1 - value2;
        }
    }
    
    items.sort(createComparisonFunction('age'));
    ```
5. concat方法,参数为空,复制副本,若有参数,参数放在数组后面并返回,不会影响原来的数组.
6. slice(开始位置,结束位置)截取数组,不影响原来的数组
7. splice(起始位置,删除的项数,要插入的任意多项),原数组会改变,调用方法会返回删除的项.
8. every,filter,forEach,map,some方法参数组项的值,该项在数组的位置,数组本身
9. reduce,reduceRight方法,第一个参数为前一项,第二个参数为当前项,第三个参数为当前项索引,第四个为数组本身
    
##1.8正则

Regexp的实例属性

1. lastIndex,表示开始下一次搜索匹配字符的起始位置
2. source,返回字面量形式的正则表达式字符

Regexp方法

1. exec方法,返回一个数组,数组的元素包含2个额外属性,index:匹配字符串的位置,input: 应用正则表达式的字符串.
2. test:匹配返回true/false

##1.9 函数表达式和函数声明的区别

调用函数表达式必须在函数之后,而调用函数声明的函数则与位置无关.

##1.10 字符串的模式匹配方法

重要的是replace方法

```javascript
var text = 'cat,bat';
var result = text.replace('/at/g','K'); //cK,bK
```

第二个参数还可以是特殊的字符序列

| 字符序列 | 替换文本                     |
|----------|------------------------------|
| $$       | $                            |
| $&       | 匹配整个模式的子字符串       |
| $'       | 匹配的子字符串之前的子字符串 |
| $`       | 匹配的子字符串之后的子字符串 |
| $n       | 匹配的第n个捕获组子字符串    |
| $nn      | 匹配的第nn个捕获组的子字符串 |

```javascript
var text = 'cat,bat';
var result = text.replace('/at/g','K $1'); //K cat,K bat
```

下面演示过滤html字符过滤

```javascript
function htmlEscape(text){
    returntext.replace([/[<>"&]]/g,function(match,pos,originalTex){
        swith(match){
            case "<":
                return "&lt";
            case ">"
                return "&gt;";
            case "&":
                return "&amp;";
            case "\""
                return "&quot;";
        }
    });
}
```

##1.11 Math 获取随机数
```javascript
function selectForm(lowerValue,upperValue){
    var choices = upperValue - lowelValue +1 ;
    return Math.floor(Math.random() * choices +lowerValue);
}
var num = selectFrom(2,10);//获取包括2和10的一个数值.
```

##1.12 对象属性


1. 原型继承:如何取决在prototype还是在构造方法中定义哪些属性,构造函数定义实例间不同的属性,而prototype定义实例间共享的属性和方法

#2. 继承

##2.1 组合继承

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ['red','blue','green'];
}

SuperType.propertype.sayName = function (){
    alert(this.name);
};

function SubType(name,age){
    //继承属性
    SuperType.call(this,name);
    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age)
}

var instance1 = new SubType("K",21);
instnace1.colors.push("black");
console.log(instance1.colors); //red,blue,green,black
instance1.sayName();//K
instance1.sayAge(); //21

var instance2 = new SubType("404",22);
console.log(instance2.colors);//red,blude,green
instance2.sayName();/404
instance2.sayAge(); //22
```

##2.2 寄生组合继承

```javascript
function inheritPrototype(subType,superType){
    var prototype = Object(superType.prototype);
    prototype.constructor = subType;
    subType.prototype = prototype;
}

function SuperType(name){
    this.name =name;
    this.colors = ['red','blue','green'];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name,age){
    SuperType.call(this,name);
    this.age =age;
}

inheritPrototype(SubType,SuperType);

SubType.prototype.sayAge = function(){
    alert(this.age);
};
```

#3. 函数表达式

递归保险的写法

```javascript
var factorial = (function f(num){
    if(num<=1){
        return 1;
    }else {
        return num * f(num-1);
    }
});
```

模仿块级作用域

```javascript
(function(){
    块级作用域
})();
```
类似的写法很多,实际上立即运行里面的匿名函数,这样做事为了防止函数里的代码变量污染到全局变量.

第一个大的()代表包含的函数表达式,后面的()代表调用这个函数.和普通函数一样,可以在里面放参数,里面的函数即可访问到这些参数

这样做也可以防止闭包占用内存问题.因为函数一旦完成,里面的作用域链就会销毁.

# 4. 客户端检测

# 5. DOM

# 13. 事件

## 13.1 事件流

### 13.1.1 事件冒泡

事件冒泡: 即事件开始时由具体的元素(文档中嵌套层次最深的那个节点)接受,然后逐级向上传播到较为不具体的节点(文档)

IE9 FF Chrome Safari 都是将事件一直冒泡到window对象

### 13.1.2 事件捕获

捕获是从window开始捕获事件,从window一直捕获到具体的节点,但是IE8及其更早的版本不兼容

规范要求是从ducmnent开始捕获,但是浏览器一般从window就开始获取了

### 13.1.3 DOM事件流

事件捕获阶段->处于目标节点->事件冒泡阶段

DOM2级事件规范要求捕获阶段不要涉及事件目标,但IE9和FF和Chrome和Safari和Opera9.5及更高版本都会在捕获阶段触发事件对象上的事件.结果就有两个机会再目标对象上面操作事件

## 13.2 事件处理程序

### 13.2.1 HTML事件处理程序

html里写JS事件弊端主要有,当html刚显示出来,但当时事件处理程序可能还不具备执行的条件,就容易报错

### 13.2.2 DOM0级事件处理程序

这个是直接指定属性,优势有两: 1.简单,2: 浏览器兼容较好

但是这种绑定事件不能在chrome的`EventListeners`面板中看到

但是这个好像跟加载的时机有关,第一次打开页面的话,加载事件没问题,但是刷新后再加就加不上了

```javascript
//绑定事件
btn.onclick = function(){};
//取消绑定事件 除了可以取消DOM0事件,还可以取消HTML事件
btn.onclick = null;
```

### 13.2.3 DOM2级事件处理程序

DOM2的话主要有addEventListener()和removeEventListener

第一个参数为要绑定的事件,第二个是处理函数,第三个为`true(捕获阶段处理函数)|false(冒泡阶段执行函数)`

通过`addEventListener`添加的事件只能通过和removeEventListener移除

这意味着,addEventListener中的第二个参数如果是匿名函数,则无法移除

绑定多个事件,会按绑定顺序来触发

IE9及FF Chrome Opera都支持DOM2

### 13.2.4 IE事件处理程序

attachEvent和detachEvent方法

注意点

1. 这两个方法的this是window,而不是元素本身
2. 绑定多个事件,是按倒序来触发的

支持性 IE和Opera

## 13.3 事件对象

| 属性/方法                | 类型         | 读/写 | 说明                                                                                   |
|--------------------------|--------------|-------|----------------------------------------------------------------------------------------|
| bubbles                  | Boolean      | 只读  | 是否为冒泡                                                                             |
| cancelable               | Boolean      | 只读  | 表明是否可以取消事件的默认行为                                                         |
| currentTarget            | Element      | 只读  | 其事件处理程序当前正在处理事件的那个元素                                               |
| defaultPrevented         | Boolean      | 只读  | 为true表明已经调用了preventDefault(),DOM3级事件中新增                                  |
| detail                   | Integer      | 只读  | 与事件相关的细节信息                                                                   |
| eventPhase               | Integer      | 只读  | 调用事件处理程序的阶段: 1表示捕获阶段, 2 表示"处理目标", 3 表示冒泡阶段                |
| preventDefault           | Function     | 只读  | 取消事件的默认行为,如果cancelable是true,则可以使用这个方法                             |
| stopImmediatePropagation | Function     | 只读  | 取消事件进一步的捕获或冒泡,同时阻止任何事件处理程序被调用(DOM3级事件中新增)            |
| stopPropagation          | Function     | 只读  | 取消事件进一步的捕获或冒泡,如果bubbles为true,可以使用这个方法                          |
| target                   | Element      | 只读  | 事件的目标                                                                             |
| trusted                  | Boolean      | 只读  | 为true表示事件是浏览器生成的,为false表示事件时有开发者通过javascript创建的(DOM3级新增) |
| type                     | String       | 只读  | 被触发事件的类型                                                                       |
| view                     | AbstractView | 只读  | 与事件关联的抽象视图,等同于发生事件的window对象                                        |

this,currentTarget和target的关系

1. this和currentTarget永远相等
2. target指的是具体点击的最底层的标签.



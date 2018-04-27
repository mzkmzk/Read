# JavaScript权威指南

# 基础

# 2. 词法结构

## 2.5 可选的分号

只有在缺少了分号就无法正确解析代码的时候,`javascript`,才会在一行的最后自动添加`;`

```javascript
a = 3 //自动填充
b = 4;

var a
a
=
3
console.log(a)

//自动填充为 var a ; a = 3; console.log(a);

var y =x +f 
(a+b).toString()

//解析后 var y =x+f(a+b).toString();

//当然 return break continue除外后面会紧跟分号
return
ture;

//解析为 return ; ture;

//++ -- 例外
x
++
y

//解析为 : x; ++y;

```

# 3. 类型、值和变量

## 3.1 二进制浮点数和四舍五入错误
`javascript`只能表示1/2,1/8,1/1024等浮点数,但是1/10等是不精确的

```javascript
var x =.3 -.2;
x == .1 //false
```

日期和时间

```javascript
var then =new Date(2011,0,1);
var later =new Date(2011,0,1,17,10,30);
var now = new Date();
```

## 3.2 文本

模式匹配

```javascript
var text = "texting : 1 ,2, 3";
var pattern = /\d/g; //匹配所有包含一个/多个数字的实例
pattern.test(text) ; //=> true 匹配成功
text.search(pattern); // =>9:首次匹配成功的位置
text.match(pattern); //=>["1","2","3"] 所有匹配组成的数组
text.replace(pattern,"#"); // =>"testing: #,#,#"
text.split(/\D+/); // =>["","1","2","3"]:用非数字字符截取字符串.
```

## 3.3 布尔值

```javascript
undefined
null
0
-0
NaN
""
```

以上自动转为布尔为`false`

其他所有值,包括对象和数组都为转为`true`

## 3.4 null和undefined

```javascript
alert(typeof null) //=>object
alert(typeof undefined) // ES5中 =>undefined
null == undefined // => true
null === undefined // => false
```

只有`null`和`undefined`无法拥有方法的值

## 3.5 全局对象

Window对象定义了核心全局属性,它也针对浏览器和客户端`javascript`定义了一少部分其他全局属性.

## 3.6 包装对象

```javascript
var s = "test"
s.len =4;
var t = s.len // =>undefined;
```

在读取字符串/数组/布尔的属性和方法是可行的,

但你给她们赋属性值,就是不可行的,因为他们只是临时对象,你给她们赋值只是给她们临时值赋值.而且他们是只读的,不能修改

存取字符串/数组/布尔的属性时创建的临时对象叫做包装对象.

String(),Number(),Boolean(),构造函数就是用来显示创建包装对象的.

```javascript
var s = "test"; //字符串值
var S = new String(s); //字符串对象
s == S //true;
s === S //false;
```

## 3.7 不可变的原始值和可变的对象引用

原始值 : undefined null 布尔值 数字 字符串
对象引用 : 对象 数组

```javascript
var s = "hello";
s.toUpperCase();
s                // hello

var a =[];
var b = a;
b[0] = 1;
a[0]  // 1
a === b //true
```

## 3.8 类型转换

```javascript
"7" * "4" //28
var n = 1-"x" //NaN "x"无法转为数字的
n + "object" //NaN object
```

数组转String 会使用join()方法

转换和相等性

```javascript
null == undefined //true 以下皆为true
//以下相等是因为false转为了数字
"0" == 0
0 == false
"0" == false
```


但是`==`从不会将操作数转为布尔值
```javascript
undefined == false //false
```

### 3.8.2显示的类型转换

```javascript
Number("3");
String(false); //false.toString()
Boolean([]); //true
Object(3); //new Number(3);

x+"" // String(x);
+x or -x //Number(x)
!!x //Boolean(x);
```

进制转换
```javascript
var n =17;
binary_string =n.toString(2); //10001
octal_stirng = "0" + n.toString(8) //021
hex_string ="0x"+n.toString(16); //0x11
```
数字 => 字符串 处理
```javascript
var n =123456.789;
n.toFixed(0); //"123457"
n.toFixed(2); //"123456.79"
n.toExponential(1) //"1.2e+5"
//toPrecision方法将有效数字为转换为字符串,如果有效数字少于数字证书部分的位数,则转为指数形式
n.toPrecision(4); //"1.235e+5"
n.toPrecision(10); //"123456.7890"
```
字符串 => 数字

```javascript
parseInt("3 blind mice") //3 会自动跳过前导空格和忽略后面的内容
parseInt("0xFF") //255
parseInt(".1") //NaN
parseInt("0.1") //0
parseFloat(".1") //0.1

//指定基数
parseInt("11",2) //3
parseInt("ff",16) //255
```

对象转换为原始值

```javascript
[1,2,3].toString() //"1,2,3"
(function(x) { f(x) ;}).toString(); //function(x) {\n f(x); \n}
/\d+/g.toString() //"/\\d+/g"
new Date(2010,0,1).toString() // Fri Jan 01 2010 00:00:00 GMT-0800 (PST)

var d= new Date(2010,0,1);
d.valueOf(); //1262332800000
```

对象转字符串 有toString就执行toString,无toString就执行valueOf,若两者都无,报错.

数组转字符串,同理.不过先尝试valueOf

## 13作用属性的变量

声明一个全局变量时,用var声明的话是不可配置的

但是没用var 是可以配置的

```javascript
var truevar = 1; 
fakevar = 2;
this.fakevar2 = 3;
delete truevar //false
delete fakevar // true
delete this.fakevar2 // true
```
##14 `+`运算符

1. 如果其中一个操作数是对象,则 日期对象通过toString()方法转换,其他对象通过valueOf();
2. 如果其中一个曹所长是字符串,另外一个操作数也会变成字符串.
3. 否则,都转为数字进行运输

```javascript
1 + {} //1[object Object]
true + true //2
2+null = 2;
2+undefined = NaN
```

##15 比较运算符

`>=`和`<=`不依赖于`==`和`===`,而只是简单的不小于和不大于这样判断,除了出现NaN,都会返回false;

##16 `typeof`运算符

| x                    | typeof x                                               |
|----------------------|--------------------------------------------------------|
| undefined            | "undefiend"                                            |
| null                 | "object"                                               |
| true/false           | "boolean"                                              |
| 任意数字和NaN        | "number"                                               |
| 任意字符串           | "string"                                               |
| 任意函数             | "function"                                             |
| 任意内置对象(非函数) | "object"                                               |
| 任意宿主对象         | 编译器各自实现,但不是"undefined boolean number string" |

# 16 脚本化CSS

## 16.2 重要的CSS属性

### 16.2.1 用CSS定位元素

positon: 

1. static: 默认值,top、left等属性无用
2. absolute: 会找最近的定位祖先元素
3. fixed
4. relative: 按照正常的文档流

z-index: 

z-index的设置只对兄弟元素起作用

##17 `delete`运算符

```javascript
var o = {x:1};
delete 0.x;
"x" in o //false

var a = [1,2,3,];
delete a[2];
2 in a; //false
a.length //3 会留个洞

```
`delete`只能删除自身的属性,不能删除继承的`属性

`delete`删除不存在的属性也返回`true`

`delete`不能删除全局属性

##18 `use strict`

在不支持ECMA 5的浏览器下`use strict`无任何作用.

支持的话表示代码执行`严格模式`,满足

1. 禁止使用`with`
2. 所有变量都要先声明,否则报错.
3. 调用函数(不是方法)中的`this`值是`undefined`,(非严格`this`值总数全局对象)
    ```javascript
    //判断是否严格模式
    var has_strict_mode = (function() {"use stricut"; return this === undefiend});
    ```
4. 通过`call`或`apply`调用函数时,`this`值就是通过call和apply传入的第一个参数;(非扬模式,null和undefined值会被全局对象转换为对象的非对象值代替)
5. 给补课操作的值创建新成员会报错(非严格止只是操作失败,不会报错)
6. `eval()`的代码不能在调用程序所在的上下文声明/定义函数,非严格模式是可以的,相反,变量和函数定义在eval()创建的新作用域中,这个作用域在eval()返回时弃用了
7. 函数中的arguments对象拥有传入函数值的静态副本,在非严格模式下,arguments里的数组元素和函数参数都是指向同一个值的引用.
8. delete运算符后面跟非法标识符(变量,函数,函数参数)时,报错,非严格模式下什么也没做,只返回false
9. 试图删除不可配置的属性将报错
10. 不允许不进制速
11. 严格模式下arguments.caller和arguments.callee,会报错.

# 18. 脚本化HTTP

## 18.1 使用XMLHttpRequest

### 18.1.1 指定请求 

不能设定Content-Length Date Referrer User-Agent头,XMLHttpRequest会自动添加这些头而防止伪造他们

对相同的头信息设置SetRequestHeader,新值不会覆盖旧值,而是HTTP请求包含这个头的多个副本或这个头指定多个值

### 18.1.2 取得响应

如果返回有cookie相关的头信息,浏览器会自动处理并过滤掉他们









##19 对象

对象类型

1. 内置对象: ECMAScript定义的对象/类
2. 宿主对象: JavaScript解析器嵌入的宿主环境(浏览器)
3. 自定义对象

##20 `in` `hasOwnPreperty` `propertyIsEnumerable`

1. in 检测自有属性/继承属性 返回true
2. hasOwnPreperty 检测自身属性
3. propertyIsEnumberable 检测自身属性 & 可枚举

##21 `getter` `setter`

```javascript
var p = {
    x:1,
    get r(){return Math(this.x*this.x + this.y*this.y)},
    set r(new_value){
        var old_value = Math.sqrt(this.x*this.x + this.y*this.y );
        var ratio = new_value/old_value;
        this.x *=ratio;
        this.y *=ratio;
    },
    //只读
    get theta(return Math.atan2(this.y,this.x));
}
```
##22 属性的特性

ECMAScript5新增

1. 可以给原型对象添加方法,并设置成不可枚举
2. 给对象定义不可删除的属性

属性的特性
1. 值 `value`
2. 可写性 `writable`
3. 可枚举性 `enumerable`
4. 可配置性 `configurable`

可通过`Object.getOwnPropertyDescriptor`访问

```javascript
//{value: 1,writable:true,enumerable:true,configurable:true}
Object.getOwnPropertyDescriptor({x:1},"x");

配置属性描述符
var o ={}
Object.defineProperty(o,"x",{
                        value :1,
                        writable :true,
                        enumerable:false,
                        configurable:true
                    });
                    
o.x //1
Object.keys(o) //[]不可枚举

//修改可写性
Object.defineProperty(o,'x',{writeable:false})

o.x =2 //报错

Object.definePropery(o,'x',{value : 2})//这样修改是OK的

//修改读写器
Object.definePropery(o,'x',{get: function(){return 0;}});

//当`Object.defineProery`要修改多个属性
var p =Object.definePropery({},{
        x :{ value:1},//其他符默认为false或undefined
        y :{value :2}
    }
);
```

##23 对象的三个属性

1. 原型属性

    原型属性实在实例对象创建之初就设置好的.
    
    1. 将对象直接量创建的对象使用Object.prototype作为它们的原型.
    2. 通过new创建的对象使用构造函数的prototype属性作为它们的原型.
    3. 通过Object.create()创建的对象使用第一个参数(也可以是null)作为它们的原型
    
    
2. 类属性

    类属性只可以通过`toString`方式访问.
    
    返回类似的字符串`[object class]`
    
    以下方法可获取class
    
    ```javascript
    function class_of(o){
        if (o === null) return "null";
        if(o === undefiend) return "undefiend";
        return Object.prototype.toString.call(o).slice(8,-1);
    }
    ```

3. 可拓展性

##24 数组

###24.1 数组方法
1. 遍历数组之forEach

    ```javascript
    function logArrayElements(element, index, array) {
        console.log('a[' + index + '] = ' + element);
    }

    // Note elision, there is no member at 2 so it isn't visited
    [2, 5, , 9].forEach(logArrayElements);
    // logs:
    // a[0] = 2
    // a[1] = 5
    // a[3] = 9
    ```
2. 数组方法

    1. join() : 将数组中的所有元素转换为字符串连接在一起,方法第一个参数可以设置分隔符.
    2. reverse() : 返回逆序数组
    3. sort() : 默认按照字母表排序.
        
    ```javascript
    //自定义排序
    var k =[33,4,111,222];
    k.sort(); //1111 222 33 4
    //想第一个参数在前面,返回负数,想在后面,返回正数.
    k.sort(function(a,b){ // 4 33 222 1111 
        return a-b;
    });
    ```
    4. concat() : 创建并返回一个新数组.
    
    ```javascript
    var a = [1,2,3];
    a.concat(4,5); // [1,2,3,4,5]
    a.concat([4.5]) // [1,2,3,4,5]
    a.concat([4,5],[6,7]) //[1,2,3,4,5,6,7]
    a.concat(4,[5,[6,7]]) //[1,2,3,4,5,[6,7]];
    ```
    5. slice() : 返回指定数组的一个片段/子数组
    
    ```javascript
    var a = [1,2,3,4,5];
    a.slice(0,3); //[1,2,3]
    a.slice(3); //[4,5]
    a.slice(1,-1) // [2,3,4]
    a.slice(-3,-2) //[3]
    ```
    6. splice() : 在数组插入/删除元素的通用方法
    
    ```javascript
    var a = [1,2,3,4,5,6,7,8];
    a.splice(4); //返回[5,6,7,8] a为[1,2,3,4]
    a.splice(1,2) //返回[2,3] a为[1,4]
    a.splice(1,1) //返回[4] a为[1]
    
    var a = [1,2,3,4,5];
    a.splice(2,0,'a','b');// 返回[] ,a为[1,2,'a','b',3,4,5]
    a.splice(2,2,[1,2],3) //返回[1,2,[1,2],3,3,4,5]
    ```
    
    concat和splice插入时的区别在于splice会插入数组本身.
    
    7. push pop 在栈尾操作
    8. unshift & shift  : 在栈头操作. 注意unshift中的参数是按顺序插入的.
    9. toString toLocaleString
    
        toString和不使用参数的join作用一致
        
        toLocacleString 能返回自定义的本地化方法返回字符串.
        
3. ECMAScript 5的数组方法    

    这些数组方法一般有3个参数,一个个参数
    
    1. forEach()
    
    ```javascript
    var data = [1,2,3,4,5];
    var sum =0;
    data.forEach(function(value){
        sum += value;
    });
    
    data.forEach(function(value,index,array){
       a[i] = v+1; 
    });
    data //[2,3,4,5,6]
    ```
    
    注意`forEach`无法break停止遍历.,除非放在try里,然后抛出异常
    
    2. map() : 和forEach类似,但是会把结果放入到一个新数组返回,不会影响旧数组
    
    ```javascript
    a = [1,2,3,];
    b =a.map(function(x){
        return x*x;
    });
    ```
    
    3.fliter() 返回过滤数组,当return`true`时,不过滤此元素
    
    ```javascript
    a = [5,4,3,2,1];
    small_values = a.filter(function(x){ //[2,1]
        return x< 3 ;
    });
    ```
    
    4. every() 和 some() : 判断数组是否全部满足/至少一个满足
    
    ```javascript
    a = [1,2,3,4,5];
    a.every(function(x){ //true
        return x <10;
    });
    
    a.some(function(x){ //true
        return x%2 ==0;
    });
    ```
    这两个方法当能觉得返回什么结果时就结束循环
    
    5. reduce() 和 reduceRight()
    6. indexOf() 和 lastIndexOf() : 返回索引

###24.2 数组类型

判断数组类型,`typeof`对对象除了`function`都返回`object` 

ECMAScript5 提供 Array.isArray(参数)判断是否为数组

 ##25 函数
 
 当函数挂载在一个对象上,作为对象的一个属性,当通过这个对象来调用这个函数,该对象就是此次函数调用的上下文,即`this`
 
 ###25.1 创建函数
 
 1. 函数表达式
 
    ```javascript
    var f =function(){...}
    
    //定义时即调用一次
    var tensquared = (function(x){return x*x;}(10));
    ```
    
    该方式无法在定义之前的语句使用.
 2. 函数声明式
 
    ```javascript
    function distance(x1,y1,x2,y2){...}
    ```
    该方式可以在定义之前的语句中调用.

###25.2 函数调用

1. 作为函数
    
    ```javascript
    distance(1,2,3,4,);
    ```
    ECMAScript3和非严格ECMAScript5 `this`的值为全局对象
    
    严格的ECMAScript5无法调用`this`
    
    ```javascript
    //判断是否为严格的ECMAScript5
    var strict (function(){return !this;}());
    ```
2. 作为方法

    ```javascript
    object_k.method_k();
    ```
    作为函数和作为方法调用最大的区别是`this`
    
    作为方法调用`this`为对象的上下文
    
    ```javascript
    var calculator = {
        operand_1 :1,
        operand_2 :1,
        add :function(){
            this.result = this.apperand_1 + this.aperand_2;
        }
    }
    calculator.add();
    //or 下面这种调用好处在于动态选择调用的方法.
    calculator["add"]();
    calculator.result ; //2
    ```
    每次用方法调用,都会隐式传入一个实参(调用者的上下文);
    
3. 作为构造函数

    ```javascript
    var o =new Object();
    //构造函数先创建一个新的空对象
    //这个对象继承构造函数的prototype
    //这个对象的上下文为本对象,而非o
    var new o.m(); 
    ```
4. 通过它们的call()和apply()调用

    在严格模式下,这两个方法第一个实参都会变为this的值.
    
    ```javascript
    //将o中的m方法替换为新方法
    function trace(o,m){
        var =original =o[m];
        o[m] = function(){
            //增加log操作. this指调用trace的对象.
            return original.apply(this,arguments);
        };
    }
    
    ```

###25.3命名空间

```javascript
(function(){
    模块代码;
}());
```
实例: 检测在IE下是否缺少某些变量,有就返回补丁

```javascript

var extend = (function(){
    for (var p in {toString:null}){
        return function extend(o){
            for(var i =1 length = arguments.length;i<length;i++){
                var source =arguments[i];
                for (var prop in source) o[prop] = source[prop];
            }        
            return o;
        };
    }
    
    return function patched_extend(o){
        for(var i =1 length = arguments.length;i<length;i++){
            var source =arguments[i];
            for (var prop in source) o[prop] = source[prop];
        }
        
        for(var j=0 ;j<protoprops.length;j++){
            prop =protoprops[j];
            if(source.hasOwnProperty(prop)) o[prop] = source[prop];
        }
        return o;
    };
    
    var protoprops = ["toString","valueOf","constructor","hasOwnProperty",
        "isPrototypeof","propertyIsEnumerable","toLocalString"
    ];
}());
```

###25.4 闭包

```javascript
function counter(){
    var n = 0;
    return {
        count:function(){return n++;}
    };
}

var c =counter(), d =counter();
c.count() //0
d.count() //0
d.count() //1
c.count() //1
```
闭包主要作用是为了让内部嵌套的函数能使用外部的局部变量,并该对象能保持局部变量.当创建了一个对象以后,因为作用域链的原因,对象执行完没有被释放.

# 14. Window对象

### 14.2 浏览器定位和导航

winodw.location === document.location //true

document也有一个属性URL,但是该属性在扎入文档后就不会改变,而Location对象会因为`#`片段识别符而变化

# 22. 客户端存储

## 20.2 cookie

### 20.2.1 cookie属性: 有效期和作用域

有效期

当浏览器进程被彻底杀死时,会话cookie就没有了

作用域

1. path属性: 默认作用域为path同级页面和子页面有效
2. domain属性: 设置可以访问的域名,只能是当前服务器的域
3. secure: 表明是否只允许https或其他安全协议传输

### 20.2.2 保存cookie

cookie的名/值不允许包含分号 逗号 空白符

所以存的时候一般需要encodeURIComponent

取出来的时候需要decodeURIComponent

### 20.2.3 读取cookie

只有ducoument.cookie获取

### 20.2.4 cookie的局限性

现在大部分浏览器允许cookie总数超过300个,但是部分浏览器对单个cookie大小仍有4kb的限制

## 20.3 利用IE的userdata持久化数据

和cookie有点像,不过比cookie的局限性更大,作用域限制在当前文档同目录的文档中

优势就是IE下5.5就支持了,而且不会像cookie那样要给服务器发请求的时候都带上吧.

存储的容量比cookie要大,比localStroge要小

应用场景应该是在不支持localStroge的IE8及其以下

最好这样使用

```javascript
<!--[if IE]>
<script src="UserDatStorage.js"></script>
<![endif]-->
```

使用得比较少,就不写代码了,书上/网上有对应写好的API的JS

# 脚本化文档

`Document Object Model`

![文档节点的部分层次结构](QQ20151230-0.png)


每个Window对象都有一个document属性指向`Document`

Document对象表示窗口的内容.

##1. 获取文档元素
```javascript
//ID
//返回`element`
document.getElementById('ID_K');

//Name
//返回NodeList
document.getElementsByName("name_K");

//根据标签 
//返回HTMLCollection 在<<JavaScript权威指南>>第六版第367页中写的是返回NodeList,但是查了官网API是返回HTMLCollection的.
//因为HTML不区分大小写,所以这个也不区分大小写.
var first_p = document.getElementsTagName("p")[0];
first_p_span = first_p.getElementsTagName("span");

//通过Class
document.getElementsByClassName("K container");

//通过CSS选择器 返回NodeList
document.querySelectorAll("#K div");
document.querySelector("#K div"); //返回第一个匹配的.

```

`HtmlDocument`还定义了一些快捷的访问方式.

只读的`img`,`form`,`a(包含href属性的)`等元素的集合.(返回HTMLCollection)

因为在`Document`对象中为`form` `img` `iframe` `applet` `embed` `ojbect`元素设置name属性,既在Document对象中创建以此name属性值为名字的属性.

不太建议这样的使用方法,以下都返回的是`HTMLColection`
```javascript
document.images;

//假如women定义了一个ID/name属性为K_404的form 
document.forms.K_404;
//or
document.K_404;

document.links
```

##2. Node属性

1. parentNode : 该节点的父节点,类似Document对象此属性为null.
2. childNode : 只读的类数组对象(NodeList对象),实时表示
3. firstChild lastChild
4. nextSibling previoursSibling
5. nodeType : 节点类型
    1. 9表示Document节点
    2. 1表示Element节点
    3. 3表示Text节点
    4. 8表示Comment节点
    5. 11表示DcoumentFragment节点
6. nodeValue : Text节点/Commetn节点的文本内容.
7. nodeName : 元素的标签名,以大写形式表示


##3. HTML属性作为Element的属性

```javascript
var image = document.getElementByID("K_404");
var iamge_url = "../image/404_K.jpg";

///关键字 会默认前面加html 除class ,其他驼峰
document.getElementsByTagName('lable')[0].htmlFor = "404_K";
document.getElementById('K_404').className = "container";
```

##4. 获取和设置非标准HTML属性

Element定义的方法

1. setAttribute
2. getAttribute
3. hasAttribute
4. removeAttribute

```javascript
var image = document.images[0];
var width = parseInt("image.getAttribute('width')");
image.setAttribute('class',"container");

```

##5. `data-` 数据集属性

因为在`第四节`中定义了非标准的HTML属性,导致HTML不再是合法的HTML,所以

在`HTML5`新增了任意以`data-`为前缀的小谢属性名字都是合法的,并且HTML也是合法的

HTML5在`Element`对象定义了`dataset`属性,指向一个对象,它的各个属性对应于去掉前缀`data-`属性.

```html
<span class="sparklinke" data-ymin="0" data-ymax="10">
     1 1 1 2 2 3 4 5 5 4 3 5 6 7 7 4 2 1
</span>     
```
```javascript
var spanrkline = document.getElementsByClassName('sparkline');
for(var i=0,length = sparklines.length;i< length ; i++){
    var dataset =sparkline[i].dataset;
    var ymin =parseFloat(dataset.ymin);
    var ymax =parseFloat(dataset.ymax);
   ...
}

```

##6. 作为Attr节点的属性

Node定义了`attributes`属性.非Element对象,该属性为null

对应`Element`对象,attributes属性是只读的类数组对象.它代表元素的所有属性,并且是实时的.

```javascript
document.body.attributes[0]
document.body.attributes.bgcolor
document.body.attributes["onload"]
```

当索引attributes对象得到的都是Attr对象,Attr有name和value属性

##7. 作为HTML的元素内容

1. innerHTML
2. outerHTML

    outerHTML返回标签的开始标签和结束标签,还有内容.当替换该属性时,标签也被替换
3. insertAdjacenHTML
    
    该方法在HTML5标准化

    有两个参数
    
    1. 为依赖的参数位置
        
        `beforebegin` `afterbegin` `beforeend` `afterend`
        
        ```javascript
        1<div>2This3</div>4
        ```
        以上的1234和参数插入的未知对应
    2. 为HTML标记字符串
    
##8. 作为纯文本的元素内容

```javascript
var k = document.getElementsByTagNames('p')[0];
var text = k.textContent;
```

textContent和innerText的区别

1. textContent只是把Text节点串联在一起.
2. innerText不返回`<script>`元素,和忽略多余的空白.,`table` `trbody` `tr`的innerText是只读属性.


##9 创建节点
1. createElement;
2. createTextNode;
3. createComment;
4. createDocumentFragment;
5. cloneNode(true/false) : 是否深层复制
6. importNode() 如果它传递另一个文档的一个节点,它将返回一个适合本文档插入的节点的副本,第二个参数是布尔选择是否深层复制.

##10 插入节点

1. appendChild() 插在调用者的最后一个子节点
2. insertBefore() 
    
    有两个参数
    
    1. 待插入的节点.
    2. 父节点的子节点. 待插入节点会插在他前面.

```javascript
//将child节点插入到parent,使其成为第n个节点
function insert_at(parent,child,n){
    if(n<0 || n> parent.childNodes.length) throw new Error("invalid index");
    else if (n == parent.childNodes.length) parent.appendChild(child);
    else parent.inserBefore(child,parent.childNodes[n]);
}
```

这两个方法还可以帮节点移位,文档中的一个节点再次插入,会删除旧的.

##11. 删除和替换节点

1. removeChlid

    ```javascript
    n.parentNode.removeChild(n);
    ```
2. replaceChild
    
     ```javascript
     //b节点替换n节点.
    n.parentNode.replaceChild(b,n);
    ```
    
##12. 其他文档属性

1. document
    
    1. cookie
    2. domain
    3. lastModified
    4. location
    5. referrer
    6. title
    7. URL

##13.查询选取文本

判定用户选择了什么文本

```javascript
function get_select_text(){
    if(window.getSelection){ //HTML5标准
        return window.getSelection().toString();
    } else if (document.selection){ //IE特有
        return documnet.selection.createRange().text;
    }
}
```

##14. 可编辑内容

1. HTML设置contenteditable属性
2. 设置JavaScript
    
    ```javascript
    some_div.contentDocument.designMode = "on";
    ```
    
# 事件处理

##1. `addEventListener` 和 `attachEvent`

前者支持IE9及其他主流浏览器,或者为支持IE8及其之前的IE.

区别.

1. `attacheEvent`不支持事件捕获
2. `attacheEvent`要添加`on`前缀
3. `attacheEvent`允许同一函数注册多次,而`addEventListener`同一函数只能注册一次
4. `attachEvent`注册的处理程序可能按任何顺序调用(~_~!!!!),`addEventListener`按照注册顺序调用.
5. `addEventListener`第三个参数为true,则注册为捕获事件处理,作为事件传播第一个阶段调用.



    

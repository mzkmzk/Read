# 基础

##1. 可选的分号

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

##2. 二进制浮点数和四舍五入错误
`javascript`只能表示1/2,1/8,1/1024等浮点数,但是1/10等是不精确的

```javascript
var x =.3 -.2;
x == .1 //false
```

##3. 日期和时间

```javascript
var then =new Date(2011,0,1);
var later =new Date(2011,0,1,17,10,30);
var now = new Date();
```

##4. 模式匹配

```javascript
var text = "texting : 1 ,2, 3";
var pattern = /\d/g; //匹配所有包含一个/多个数字的实例
pattern.test(text) ; //=> true 匹配成功
text.search(pattern); // =>9:首次匹配成功的位置
text.match(pattern); //=>["1","2","3"] 所有匹配组成的数组
text.replace(pattern,"#"); // =>"testing: #,#,#"
text.split(/\D+/); // =>["","1","2","3"]:用非数字字符截取字符串.
```

##5. 布尔值

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

##6 null和undefined

```javascript
alert(typeof null) //=>object
alert(typeof undefined) // ES5中 =>undefined
null == undefined // => true
null === undefined // => false
```

只有`null`和`undefined`无法拥有方法的值

##7 全局对象

Window对象定义了核心全局属性,它也针对浏览器和客户端`javascript`定义了一少部分其他全局属性.

##8 包装对象

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
s == S //false;
s === S //true;
```

##9 不可变的原始值和可变的对象引用

原始值 : undefined null 布尔值 数字 字符串
对象引用 : 对象 数组

```javascript
var s = "hello";
s.toUpperCase();
s                // hello

var a =[];
var b = 啊;
b[0] = 1;
a[0]  // 1
a === b //true
```

##10 类型转换

```javascript
"7" * "4" //28
var n = 1-"x" //NaN "x"无法转为数字的
n + "object" //NaN object
```

数组转String 会使用join()方法

##11 转换和相等性

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

##12 显示的类型转换

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
n.toFixed(2); //"12345679"
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
new Date(2010,0,1).toString*( // Fri Jan 01 2010 00:00:00 GMT-0800 (PST)

var d= new Date(2010,0,1);
d.valueOf(); //1262332800000
```

对象转字符串 有toString就执行toString,无toString就执行valueOf,若两者都无,报错.

数组转字符串,同理.不过先尝试valueOf

##13作用属性的变量

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
1. 值
2. 可写性
3. 可读性
4. 可配置性


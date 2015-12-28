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
```
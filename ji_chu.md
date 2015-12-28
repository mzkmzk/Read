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

4. 模式匹配

```javascript
var text = "texting : 1 ,2, 3";
var pattern = /\d/g; //匹配所有包含一个/多个数字的实例

```

只有`null`和`undefined`无法拥有方法的值
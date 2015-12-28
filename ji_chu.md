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

`javascript`只能表示1/2,1/8,1/1024等浮点数,但是1/10等是不精确的



只有`null`和`undefined`无法拥有方法的值
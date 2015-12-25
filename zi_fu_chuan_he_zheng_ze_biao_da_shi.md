# 字符串和正则表达式

##1. 字符串

本章的字符串链接建议,对IE8及以前性能可能不增反退.

```javascript
str += "one" + "two"
```

字符串链接顺序及原理

1. 在内存中创建一个临时字符串
2. 连接后的"onetwo"被赋值给该临时字符串
3. 临时字符串与str当前的值连接
4. 结果付给str

优化
```javascript
str += "one";
str += "two";

or

str =str + "one" +"two";

//str = "one" + str + "two"; 这样优化无作用.
```

这两种都可以避免临时字符串的创建.

为什么`str = "one" + str + "two";`这样的优化会失效呢?

因为除了IE外,其他浏览器都会给最左边的字符串分配更多的内存空间,这样后面字符串要加进来,就可以减少内存的扩增时间.

如果把`one`放在第一个,`str`一般比它要长的多,所以还是要分配额外的内存空间,时间操作上和新增临时字符串无区别.

IE8实现字符串链接,只是记录现在字符串的引用,当你要用它的时候,才把他们连接起来.,代替之前的记录的字符串引用.

IE7更加糟糕.每连接一对字符串都要把他`复制`到一个新内存中.

###1.1 Firefox的编译期合并.

Firefox在赋值便打算中所有要链接的字符串都属于编译器常量.

```javascript
function folding_demo(){
    var str = "compile" + "time" + "folding";
    str += "this" + "work" + "too";
    str = str +"but" +"not" + "this"
}

//同效
function folding_demo(){
    var str ="compiletimefolding";
    str += "thisworktoo";
    str = str +"but" +"not" + "this"
}
```

在复制语句中含有..变量时...他就歇菜了,,所以很多时候其实这种优化不起什么作用.

`YUI Compressor`代码压缩也会做这样的优化.
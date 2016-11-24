# EffectiveJavaScript编写高质量JavaScript的68个有效方法

# 第一章 让自己习惯JavaScript

## 第3条 当心隐式的强制转换

验证一个值是否是真正的NaN

isNaN不能判断是否为真的NaN

```javascript

var x = NaN
isNaN(x)//true
isNaN('foo')//true
```

验证是否为真的NaN....怪异的来了

```javascript
function isReallyNaN(x) {
  return x!==x
}
```

## 第5条 避免对混合类型使用==运算符

对于类型转换

1. Date对象会先尝试toString再尝试valueOf
2. 非Date就会先尝试valueOf再尝试toString

## 第6条 了解分号插入的阶段

1. 仅在"}"标记之前,一行的结束和程序的结束处推导分号
2. 仅在紧接着的标记不能被解析时推导分号
3. 在以`( [ + - /`字符开头的语句前决不能省略分号
4. 当脚本连接时,在脚本之间显式地插入分号
5. 在return throw break continue ++ --参数之前决不能换行
6. 分号不能作为for循环的头部或空语句的分隔符而被推导出

# 第二章 变量作用域

## 第8条 尽量少用全局对象

先这样写,以后再调整.虽然会比较方便

但是优秀的程序员会不断留意程序结构,持续地归类相关的功能以及分离不相关的组件,并将这些行为作为变成过程中的一部分

使用全局对象来做平台特性检测



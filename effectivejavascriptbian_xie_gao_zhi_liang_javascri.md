# EffectiveJavaScript编写高质量JavaScript的68个有效方法

# 让自己习惯JavaScript

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


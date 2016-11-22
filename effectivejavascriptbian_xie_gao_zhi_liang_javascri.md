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
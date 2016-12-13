#白帽子将Web的安全

# 1. 基础

安全三要素

1. 机密性
2. 完整性
3. 可用性

# 3. 跨站脚本攻击(xss)

## 3.1 XSS简介

XSS根据效果不同分类

1. 反射型XSS

  需要引诱用户点击一个恶意链接,链接上的参数对页面逻辑处理有影响
2. 存储型XSS

   把恶意的javascript存储在数据库

加以开启HTTPOnly

成功获取XSS漏洞后

可以`帮`用户发出GET/POST请求

可以通过JS画出登录框....从而获取用户的账号密码......

可以通过 visited属性判断用户是否浏览过某些网站

location.hash不会被服务器记载,能很好的达到隐藏的功能

## 3.3 XSS的防御

### 3.3.1 四两拨千斤: HTTPOnly

可以针对某一key开启HttpOnloy

### 3.3.2 输入检查

过滤黑名单

前后端共同校验

### 3.3.3 输出检查

相应的html转义方法

```
& -> &amp;
< -> &lt;
> -> &gt;
" -> &quot;
' -> &#27; //不建议&aops;
/ -> &#x2F; //以防闭合HTML entity

```

1. PHP有htmlentities和htmlspecialchars
2. javascript有JavascriptEncode

### 3.3.5 处理富文本

遵循白名单而非黑名单

例如富文本肯定不包含 iframe script base form等

我们应该尽量使用白名单`a img div span`等比较`安全的标签`

php腿脚使用的XSS Filter<https://github.com/ezyang/htmlpurifier>


# 4 跨站点请求伪造(CSRF)




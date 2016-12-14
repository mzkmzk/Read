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

## 4.3 CSRF的防御

### 4.3.1 验证码

最有效的防御CSRF

### 4.3.2 Referer Check

验证referer

但是例如HTTPS跳到HTTP,出于安全原因,浏览器不会发Referer

这个方法可以辅助防止CSRF,就是有的时候验证,没有的话,让其走其他通道

### 4.3.3 Anti CSRF Token

### 4.3.3.1 CSRF的本质

CSRF攻击的成功: 重要参数都被攻击者猜测到

一般的token都是放在隐藏的input或者session当中

### 4.3.3.2 Token使用原则

1. 切勿放在url上,或者发出的所有请求(img),很大几率把url放在refer泄露
2. 有XSS的情况下,CSRF形同虚设

# 5. 点击劫持

点击劫持的前提

1. 流氓运营商
2. 钓鱼网址

## 5.1 什么是点击劫持

使用一个不可见的全屏的元素覆盖在网页上(就是一般黄色网址都有的,点击先跳过去一个游戏网页之类的)

还可以通过点击劫持打开摄像头哦...不过浏览器一般都会访问权限


## 5.6 防御ClickJacking

### 5.6.2 X-FRAMT-Options

这个我觉得防止运营商插入的iframe广告还有点用..因为运营商一般放iframe广告比较多,,敢点击劫持的还是少数

当值为 

1. DENY: 阻止任何FRAME
2. SAMEORIGIN: 同域
3. ALLOW-FROM: 定义允许frame加载的网页标签








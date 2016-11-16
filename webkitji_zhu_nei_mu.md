# WebKit技术内幕

# 1. 浏览器和浏览器内核

## 1.2 浏览器内核及特性

### 1.2.2 内核特征

渲染引擎模块及其依赖的模块

HTML解释器 CSS解释器 布局(layout) JavaScript引擎

网络 存储 2D/3D图形 音频和视频 图片解码器

操作系统支持(如多线程、文件等)

# 3. WebKit架构和模块

# 4. 资源加载和网络栈

## WebKit资源加载机制

### 4.1.4 过程

当前的主线程被阻塞时,WebKit会启动另外一个线程去遍历后面的HTML网页,收集需要的资源URL,然后发送请求

### 4.1.5 资源的生命周期

资源池采用LRU

# 4.3 网络栈

chrome://net-internals/#dns 可以查看dns的缓存记录

有时候切换hosts,即使情况缓存也没清掉dns的,就可以来这里clear hosts cache掉

chrome://view-http-cache/

能看到缓存结构

# 4.3.6 高性能网站栈

一次DNS查询平均时间大概60~120ms

三次握手平均也是30ms+

可以通过预dns加载

```
<link rel="dns-prefetch" href="http://404mzk.com"
```

chrome://dns/可以查看预取得dns域名

# 5. HTML解析器和DOM模型

## 5.2 HTML解析器

### 5.2.8 JavaScript的执行

在HTML解析器的工作过程中,可能会有javascript代码(全局作用域的代码)需要执行,它发生在将字符串解析成词语后、创建各种节点的时候,这也是为什么全局执行的javascript不能访问DOM树,因为DOM还没创建完

## 5.4 影子(Shadow)DOM

例如video audioy一些元素,里面的dom是不可见的,但JS无法访问

# 6. CSS解释器和样式布局

chrome默认的样式

```css
html,body,div{display: block}

body{margin: 8px}

div:focus,span:focus{outline:auto 5px -webkit-focus-ring-color}

a:-webkit-any-link{color:-webkit-linkl text-decoration:underline}

a:-webkit-any-link:active{color:-webkit-activelink}

```

# 8. 硬件加速机制

## 8.2 Chromium的硬件加速机制

### 8.2.5 实践: 减少重绘

网页加载后,每当重新绘制新的一帧的时候,一般需要三个阶段

1. 布局
2. 绘图
3. 合成





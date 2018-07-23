# CSS世界

# 1. 概述

## 1.4 CSS世界的开启从IE8开始

本书说的CSS世界是CSS2.1 世界, 并不包括CSS3.0

现在前端技术发展迅猛，加上气氛略显浮躁, 有必要让广大前端开发者静下来认识CSS2.1的世界

否则面对CSS3的真正到来, 只能是浅水游弋, 搬砖打杂

## 1.5 table自己的世界

table比css还要老

流的CSS世界并不包括table, table有自己的世界

本书并不阐述

# 2. 需提前了解的术语和概念

# 3. 流、元素与基本尺寸

## 3.1 块级元素

常见的块级元素有`<div>` `<li>` `<table>`等

需要注意的是 `块级元素`和`disaply为block`的元素不是一个概念

例如 `<li>`元素默认的display为list-item, `<table>`的默认display为`table`

但是他们都是`块级元素`

因为符合块级元素的基本特征: 在一个水平流上智能单独显示一个元素, 多个块级元素则换行显示

正是因为`块级元素`具有换行特性，因此理论上它都可以配合clear属性来清除浮动带来的影响

```css
.clear:after{
    content: '';
    display: block;
    clear: both;
}
```

`tips: IE浏览器包括(IE11)不支持伪元素的display设置为list-item`

> 为什么list-tiem会出现项目符号

一开始只有块级盒子和内联盒子

块级盒子负责 结构

内联盒子负责 内容

突然来了一个list-item, 浏览器不知道怎么归类. 

就叫做`附加盒子`

也就是所有块级元素都有一个主块级盒子, 除此之外还有一个list-tem'附加盒子'

而IE浏览器是因为伪元素不支持list-item或许就是无法创建这个`标记盒子`导致的

之后...

又来了一个display: 'inline-block'

这个就又无法解释了

后来造物主认为每个元素都有两个盒子: `外在盒子`和`内在盒子(容器盒子)`

外在盒子负责元素是可以一行显示还是换行显示

内在盒子负责 宽高 内容显示

现在就比较好理解为什么inline-block即可图文一行展示又可以设置width/height了吧

实际上

display: 'block'是display: block-block

display: table是display: block-table

鑫三无原则: 无宽度, 无图片, 无浮动

## 3.2 width/height作用的具体细节

### 3.2.3 CSS流体布局下的宽度分离原则

所谓`宽度分离原则`, 就是CSS中的width属性不与营销宽度的padding/border(有时还包括margin)属性共存, 也就是不能出现以下的组合

```css
width: 100px; padding: 20px;
```

而是width独立占用一层标签, 而padding border margin利用流动性在内部自适应呈现

```css
.father{
    width: 180px;
}
.son{
    margin:0 20px;
    padding: 20px;
    border: 1px solid;
}

```

### 3.3.3 超越!importtant, 超越最大

min-width/max-width和min-height/max-height属性间和width/height

若同时存在会是怎么样一个情况, 谁覆盖谁呢

规则有两个

1. 超越!important
2. 超越最大

max-width会覆盖width

而且width加了!important 还是会被max-width覆盖

而min-width, max-width同时存在时, 假如min-width比max-width大

max-width会被min-width覆盖

## 3.4 内联元素

### 3.4.3 幽灵空白节点

在HTML5中(这样声明html`!doctype html`)

所有的内联函数中都会存在幽灵空白节点

例如`<div><span></span></div>`

这里的span高度是0 , 但是div的高度又是18px

为什么18 后续会说到, 跟line-hieght和vertical-align有关

# 4. 盒尺寸四大家族

## 4.1 深入理解content

### 4.1.1 content与替换元素

替换元素

1. 内容可替换, 例如img可替换src更改内容
2. 有自己的默认尺寸
3. 在很多CSS属性上有自己一套表现规则

> 替换元素的计算规则

1. 最外层CSS层 指定width/height max-width/min-width等设置尺寸
2. 中间层HTML层: 例如textarea指定的cols和rows等
3. 固有尺寸: 替换元素自身的尺寸

> 替换元素和非替换元素的距离是多远

1. 观点1: 替换元素和非替换元素之间只隔了一个src属性
2. 观点2: 替换元素和非替换元素之间只隔了一个CSS content属性

> content 与替换元素关系剖析

content属性生成的对象称为'匿名替换元素'

content属性生成的内容都是

## 4.2 温和的padding属性

### 4.2.1 padding与元素尺寸

当box-sizing: border-box

并且 width < padding 则 宽度失效 出现`首选最小宽度` 宽度等于padding的计算值

```css
.box{
    width: 80px;
    padding: 20px 60px;
    box-sizing: border-box;
}
```

则最后的宽度为120px

# 9. 元素的装饰与美化

## 9.2 CSS世界的background很单调(CSS2.0而言)

### 9.2.1 隐藏元素的background-image到底加不加载

IE8~IE11 依然发生图片请求 

firefox不会发送 

chrome和safari 会判断父元素display是否为none 是则不发 

# 9.2.2 与众不同的background-position百分比计算方式

background-position的值 主要有几种类型 数字 百分比 或者 left|top|right|bottom|center 

支持1~4个数值(IE8时 最多有两个)

比较特殊的是 当值为百分比的计算方式 

positionX = ( 容器的宽带 - 图片的宽度 ) * percentX

positionY = ( 容器的高度 - 图片的高度 ) * percentY



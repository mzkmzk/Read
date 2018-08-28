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

可以借助padding实现一个`登录 | 注册`的效果

例如

```css
a + a:before{
content: "";
font-size: 0;
padding: 10px 3 px 1 px;
margin-left: 6px
}
```

又或者我们用瞄点定位 希望其上面留点空隙

`<h3><span id="hash">标题</span></h3>`

```css
h3 {
line-height: 30px;
font-size: 14px;
}

h3 > span {
padding-top: 58px;
}
```

### 4.2.2 padding的百分值

1. 不支持负值
2. 无论是水平方向还是垂直方向 都是相对于宽度计算的

### 4.2.3 标签元素内置的padding

input button textarea select radio checkbox 都内置了padding

button的padding 最难搞 例如firefox里设置padding 0 不起作用

### 4.2.4 padding与图形绘制

```css
.icon-menu{
display: inline-block;
width: 140px;
height: 10px;
padding: 35px 0;
border-top: 10px solid;
border-bottom: 10px solid;
background-color: currentColor;
background-clip: content-box;
}
.icon-dot{
display: inline-block;
width: 100px;
height: 100px;
padding: 10px;
border: 10px solid;
border-radius: 50%;
background-color: currentColor;
background-clip: content-box;
}
```

http://demo.404mzk.com/css/padding/

## 4.3 激进的margin属性

### 4.3.1 margin与元素尺寸以及相关布局

先理一下 各类尺寸

1. 元素尺寸: content+padding+border $().width和$().height() 原生中是offsetWidth和offsetHeight 也叫元素偏移尺寸
2. 元素内部尺寸: content+padding $().innerWidth和$().innerHeight() 原生是clientWidth和clientHeight 页脚元素可视尺寸
3. 元素外部尺寸: content+padding+border+margin 没有直接对应的API

外部尺寸有个很奇特的现象 可能是负的

> 3 margin与元素的外部尺寸

实现等高布局

![实现等高布局](/assets/QQ20180814-112146.png)

http://demo.404mzk.com/css/margin/denggao.html

```css
.container{
margin: auto;
max-width: 600px;
overflow: hidden;
}


.column-left,
.column-right {
width: 50%;
float: left;
margin-bottom: -9999px;
padding-bottom: 9999px;
color: #fff;
}
.column-left {
background-color: #34538b;
}
.column-right {
background-color: #cd0000;
}
```

### 4.3.3 正确看待CSS世界里的margin合并

什么是margin合并

块级元素的上外边距和下外边距有会合并成单个边距

1. 块状元素: 但不包含浮动和绝对定位的元素
2. 只发生在垂直方向

> margin合并的3种场景

一: 相邻兄弟元素

```css
p{ margin: 1em 0 }
```

```html
<p>one</p>
<p>two</p>
```

第一个元素会和第二个元素margin合并 导致两元素的僵局只有1em

二: 父级元素和第一个/最右一个子元素

```html
<div>
<div style="margin-top:80px;">
</div>

<div style="margin-top:80px;">
<div>
</div>

<div style="margin-top:80px;">
<div style="margin-top:80px;">
</div>

```

上面3种形式 margin-top是等效的

防止margin合并的方法 满足一个即可

防止margin-top合并

*. 父元素设置为块状格式化上下文元素
*. 父元素设置border-top值
*. 父元素设置padding-top值
*. 父元素和第一个子元素之间加内联元素进行分隔

防止margin-bottom合并

*. 父元素设置为块状格式化上下文元素
*. 父元素设置border-bottom值
*. 父元素设置padding-top值
*. 父元素和最后一个子元素之间加内联元素进行分隔
*. 父元素设置height、min-height或max-height

三: 空块级元素的margin合并

```css
.father{ overflow: hidden; }
.son{ margin: 1em 0; }
```

```html
<div class="father">
<div class="son"></div>
</div>
```

father的高度只有1em

因为son的margin-top和margin-bottom合并了

这种情况如何防止margin合并

*. 设置垂直方向的border
*. 设置垂直方向的padding
*. l里面添加内联元素(加空格没用)
*. 设置height或minx-height

> 3. margin合并的计算规则

*. 正正取大
*. 正负相加
*. 负负取小

### 4.3.4 深入理解CSS中的margin: auto

margin填充规则

1. 如果一侧定值, 一侧auto, 则auto为剩余空间的大小
2. 如果两侧都是auto, 则平分剩余空间

### 4.3.5 margin无效情形

(1) display计算值inline的非替换元素的垂直margin是无效的

(2) 表格中tr和太多元素或者设置display计算值是table-cell或table-row的元素margin是无效的

(3) margin合并时 更改的margi值可能是没有效果的

## 4.4 功勋卓越的border属性

### 4.4.3 border-color 和 color

默认border-color就是color的色值

类似的属性还有 outline box-shadow text-shadow

### 4.4.4 border与透明边框技巧

> 三角形绘制

http://demo.404mzk.com/css/border/sanjiaoxing.html

![三角形绘制](/assets/QQ20180816-201349.png)

```css

.box {
width: 0;
border: 10px solid;
margin: 30px;
}
.sanjiaoxing{
border-color: #f30 transparent transparent;
}

.sanjiaoxing2{
border-color: transparent transparent #f30 ;
}

.sanjiaoxing3{
border-color: transparent #f30 transparent ;
}

.sanjiaoxing4{
border-color: #f30 transparent ;
}

.sanjiaoxing5{
width: 10px;
height: 10px;
border-color: #f30 #00f #396 #0f0 ;
}

.sanjiaoxing6{
width: 10px;
height: 10px;
border-color: #f30 transparent transparent ;
}

.sanjiaoxing7{
border-width: 10px 20px;
border-style: solid;
border-color: #f30 #f30 transparent transparent ;
}
```

###4.4.6 border等高布局技术

http://demo.404mzk.com/css/border/denggao.html

原理就是

border-left 设置150的宽度

nav设置margin-left -150px float:left

然后在box上清除浮动

核心CSS

```css
.box{
border-left: 150px solid #333;
background-color: #f0f3f9;
}
/* 清除浮动影响，不能使用overflow:hidden */
.box:after{
content: '';
display: block;
clear: both;
}

.box > nav{
width: 150px;
margin-left: -150px;
float: left;
}
```

# 5. 内联元素与流

## 5.1 字母x CSS世界中隐匿的举足轻重的角色


CSS中的基线: 小写字母x的下边缘(线)

### 5.1.2 字母x与CSS中的x-height

vertical-align: middle 指的是1/2 x-height 就是x中间交叉的位置

### 5.1.3 字母x与css的ex

1ex指的就是 x-height的高度

所以用1ex来垂直和文本对齐是极好的

```css
.icon-arrow{
display: inline-block;
width: 20px;
height: 1ex;
background: url('../img/arrow.png') no-repeat center;
}
```
![1ex来垂直和文本对齐](/assets/QQ20180817-182406.png)

http://demo.404mzk.com/cssworld/img/icon-duiqi.html

## 5.2 line-height内联元素的基石

### 5.2.1 内联元素的高度之本- line-hieght

默认空的div高度是0

写了几个字之后 就有高度了 请问为什么

是因为font-size吗

不是的 主要是因为line-hieght决定的 但有时也跟font-size有关

行距 = line-height - font-size

而行距分为上下行距 是平分的

### 5.2.2 为什么line-height可以让内联元素'垂直居中'

例如

http://demo.404mzk.com/cssworld/5/juzhong.html

```css
.box{
width: 280px;
line-height: 120px;

background-color: #f0f3f9;
}

.content{
display: inline-block;
line-height: 20px;
vertical-align: middle;
}
```

```html
<div class="box">
    <div class="content">基于行高实现的...</div>
</div>
```

是因为.content的display: inline-block 生成了一个幽灵节点

继承了.box的line-height 120px 撑开了高度

然而和设置vertical-align: middle 居中

### 5.2.3 深入line-height的各类属性值

line-height的默认值是normal, 还支持数值 百分比 长度值

normal具体的值 只和当时的字体有关系

所以一般line-height都建议是css赋值的

数值: 例如line-height: 1.5 最终效果是 1.5 乘以 当前font-size

百分比: 例如line-height: 150% 乘以 150% 乘以 当前font-size

长度值: 具体的长度值

问题:

line-height: 1.5 或者 1.5em 或者 150% 有何不同

同: 

对于当前元素的最后计算值都是一样的

异:

150%和1.5em 子元素继承的line-height 是最后的计算值

而 1.5 子元素继承的line-height 就是1.5 

关于重置line-height

因为line-height具有继承性

我们是否 在body上设置line-height 就全部元素覆盖了呢

非也

因为替换元素并不继承父元素的line-height

需要

```css
body {
    line-height: 150%;
}

input, button {
    line-height: inherit;
}
```

### 5.2.4 内联元素 line-height 的大值特性

```html
<div class="box box1">
    <span>span: line-height:20px</span>
</div>
<div class="box box2">
    <span>span: line-height:20px</span>
</div>
```

```css
.box {
    width: 280px;
    margin: 1em auto;
    outline: 1px solid #beceeb;
    background: #f0f3f9;
}
.box1 {
    line-height: 96px;
}
.box1 span {
    line-height: 20px;
}
.box2 {
    line-height: 20px;
}
.box2 span {
    line-height: 96px;
}
```

问题其实 就是 最后.box1的高度为多高 是20还是96?

之前 内联盒模型都是 存在一个空白的幽灵节点的

空白幽灵节点继承了父元素的line-height 

所以当 父元素lien-height > span的line-height

空白幽灵节点 会撑开div 高度为96

当 父元素lien-height < span的line-height

span则撑开了 div的高度 也为96px

所以称内联模型 父元素和子元素设置了不同的line-height

则最后 表现出来span元素的line-height 都是两者中大的那个 作者称 line-height是'大值特性'

## 5.3 line-height 的好朋友 vertical-align

line-height起作用的地方 vertical-align也一定起作用

### 5.3.1 vertical-align家族基本认识

抛开inherit 这类全局属性值不谈

*. 线性: baseline(默认值 对准基线) top middle bottom
*. 文本类: text-top text-bottom
*. 上标下标类: sub super
*. 数值百分比: 20px 20%, 相作用与基线, 正值在基线之上xx, 负值在基线之下xx

基线即英文字母x的下边缘线

baseline 相当于 vertical-align: 0

凡是百分比值 都有个参考系的

例如 padding margin 是相对于宽度计算的

而line-height是相对于font-size计算的

vertical-align的百分比则是根据line-height计算的

### 5.3.2 vertical-align作用的前提

只能作用于 内联元素 或 display为table-cell的元素

也相当于是 只作用于 display为 inline inline-block inline-table table-cell

所以默认的情况下

span strong em 等内联元素

img button input 等替换元素

非HTML规范的自定义标签元素 以及td单元格 都是支持vertical-algin的

一些浮动和绝对会让元素块状化

```css
.example{
    float: left;
    vertical-align: middle;
}

.example{
    position: absolute;
    vertical-align: middle;
}
```

以上的middle都是无作用的

还有一些是因为行高高度不足 导致vertical-align无效

<http://demo-404mzk-com/cssworld/5/vertical-align-wuxiao.html>

```css
.box{
    height: 128px;
    /* line-height: 128px; 需要设置此属性vertical-align才生效 */
}

.box > img {
    height: 96px;
    vertical-align: middle;
}
```

```html
<div class="box">
    <img src="../../img/1.pic.jpg" class="i_img"/>
</div>
```

此时box的lineHeight是normal

和img的lineHeight 也是normal

line-height 是normal的话 大小由当前字体决定

所以 需要设置.box的line-height为128px

让img内联元素的幽灵空白节点 撑开高度

table-cell作用的是元素本身 而非子元素

![table-cell作用的是元素本身 而非子元素](/assets/QQ20180823-1245591.png)


```css
.cell{
    height: 128px;
    display: table-cell;
    vertical-align: middle;
}

.cell > img {
    height: 96px; 
}
```

vertical-align设置在img是无效的 需要设置在table-ceel同一元素

那为什么.cell不需要设置lien-height为128px 撑开呢? 因为它是块级元素

### 5.3.3 vertical-align 和 line-height之间的关系

大家先来看下以下的.box 高度会是多少

```css
 .box {
    line-height: 32px
}

.box > span { font-size: 24px }
```

![line-height高度](/assets/QQ20180823-140715.png)

实际上的高度会比32px 高一点

为什么呢

span内联盒子存在一个幽灵节点

幽灵节点的font-size是继承.box的 .box默认是16px

而span里的font-size是24px

因为line-height是一致的 

字号不一致的两个文字在一起时 彼此会发生上下偏移

再来看个图片的案例

<http://demo-404mzk-com/cssworld/5/vertical-align-hight.html>

```css
.example{
    width: 280px;
    outline: 1px solid #aaa;
}

.example > img {
    height: 96px;
}
```

![图片-line-height](/assets/QQ20180823-195019.png)

为什么下面会多出几px

因为在笔者的chrome font-size是16 

而幽灵节点(这里拿x来做代表) line-hieght 基本是20 = 16 + 2 * 半行距(2)

line-height对img是不起作用的

所以下面空白其实就是被幽灵节点撑开的

需要解决此类问题

*. 图片块状化, 可以一口气干掉 幽灵空白节点 line-height vertical-align
*. 容器line-height足够小 只要半行距小到字母x的下边缘位置或者再往上, 自然就没有了撑开底部间隙了
*. 容器font-size足够小
*. 图片设置其他vertical-align属性值 间隙产生原因之一就是基线对齐 所以vertical-align设置top middle botoom都可以的

此前有介绍 内联特性导致margin无效

```css
.box > img {
    hegiht: 96px;
    margin-top: -200000px
}
```

```html
<div class="box">
    <img src="1.jpg">
</div>
```

因为存在幽灵空白节点的vertical-align:baseline给限制死了

所以图片为了对齐基线 所以会一动不动

### 5.3.4 深入理解 vertical-align线性属性值

> inline-block 与 baseline

vertical-align默认是baseline 

对于文本来说是字母x的下边缘线

对于图片 是图片下边缘

但是如果是inline-block 就有个规则了

```javascript
if ( 元素是inline-block ){
    if ( 没有内联元素 || overflow !== 'visable' ){
        基线 = margin底边缘
    }else {
        基线 = 元素里最后一个内联元素的基线
    }
}
```

![inline-block 与 baseline](/assets/QQ20180823-201939.png)


左边是没有内联元素的 margin的下底线 = 右边x字母的下边缘

> 了解vertial-align: top/bottom

具体定义

vertical-align: top

*. 内联元素: 元素底部和当前行框盒子的顶部对齐
*. table-cell元素: 元素底padding边缘和表格行的顶部对齐

bottom则对应的是底部和下边缘

> vertical-algin: middle 与近似垂直居中

*. 内联元素: 元素的垂直中心点和行框盒子基线往上1/2 x-height处对齐
*. table-cell元素: 单元格填充盒子相对于外面的表格行距中对齐

一般字体的x往下偏点

所以x中间并不是中垂线

可以设置font-size为0来让x-hieight的中间 会是中垂线

### 5.3.5 深入理解vertical-algin文本类属性值

vertical-align: text-top: 盒子的顶部和父级内容区域的顶部对齐

vertical-align: text-bottom: 盒子的底部和父级内容区域的底部对齐

内容区域可以理解为默认状态 就是浏览器文本选中的背景区域

理论上说的 就是 font-size和font-family下应有的内容区域大小

可以通过这个demo http://demo.cssworld.cn/5/3-9.php 来体验

![vertical-align: text-top](/assets/QQ20180825-083223.png)

这两个属性在实际开发中 其实基本没有作用

因为

*. 使用场景缺乏
*. 文本类垂直对齐理解成本高
*. 内容区域不直观且易变

### 5.3.6 简单了解 vertical-align上标下标类属性值

*. vertical-align: super: 提高盒子的基线到父级合适的上标基线位置 作用和`<sup>`类似
*. vertical-algin: sub: 降低盒子的基线到父亲合适的下标基线位置 作用和`<sub>`类似

作用基本就是 公式里写平方 和化学公式的下标 的作用了

### 5.3.7 无处不在的vertical-align

有不理解的 先考虑下是否是幽灵空白节点的影响

vertical-algin: top/bottom 作用的对象是 对齐看边缘看行框盒子

vertical-algin: baseline/midlle 对齐看x-height

多个内联元素放在一起 可能会有人懵逼 

但是分析的时候 还是分析自身即可

vertical-align 可以说是CSS最难点

### 5.3.8 基于vertical-align属性的水平垂直居中弹框

为什么这个框垂直居中了

首先用fixed top right bottom left 把.container拉满屏幕

然后通过font-size 把middle的近似居中问题解决掉

text-align: center 决定内联元素在水平居中

如果没有这个伪元素

那么这个.dialog 的中心在界面的最底部

而伪元素设置了vertical-align: middle 硬生生把内联元素的基线拉到中部

这就是为什么框能居中

这里为什么框会跟着基线做

因为dialog设置了inline-block

而为了让dialog的中心对准container的基线

dialog也需要设置vertical-algin:middle

体验链接: <http://demo.cssworld.cn/5/3-10.php>

```css
.container {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAABtJREFUeNpiZGBgaGAgAjAxEAlGFVJHIUCAAQDcngCUgqGMqwAAAABJRU5ErkJggg==);
    background: rgba(0,0,0,.5), none;
    text-align: center;
    white-space: nowrap;
    /* font-size: 0; */
    z-index: 99;
}
.container:after {
    content: "";
    display: inline-block;
    height: 100%;
    vertical-align: middle;
}
.dialog {
    display: inline-block;
    vertical-align: middle;
    border-radius: 6px;
    background-color: #fff;
    font-size: 14px;
    text-align: left;
    white-space: normal;
}
.content {
    width: 240px;
    height: 120px;
    padding: 20px;
}
```

```html
<div class="container">
    <div class="dialog">
        <div class="content">内容占位</div>
    </div>
</div>
```

# 6 流的破坏与保护

## 6.1 魔鬼属性float

### 6.1.1 float的本质与特性

浮动的本质就是为了实现文字环绕效果

float的特性

*. 包裹性
*. 块状化并格式化上下文
*. 破坏文档流
*. 没有任何margin合并

块状化的意思是float属性值不为none 则display计算值为block或者table

除了设定的display为inline-table 其设置了float后 display计算值为table 其余都是block

### 6.1.2 float的作用机制

子元素设置了float 会让 父元素的高度值为0

高度塌陷 只是让跟随内容可以合浮动元素在一个水平线上

但是这只是条件之一

想要真正环绕效果

需要行框盒子在正常定位状态下只会跟随浮动元素 而不会发生重叠

### 6.1.3 float更深入的作用机制

float相关术语

*. 浮动瞄点: 是float元素所在的`流`中的一个点, 这个点本身并不浮动就表现而言更像一个没有margin border 和padding的空的内联元素
*. 浮动参考: 指的是浮动元素对齐参考的实体

浮动参考 指的是行框盒子 即 float元素在当前'行框盒子'内的定位

### 6.1.4 float与流体布局

![float-test](/assets/QQ20180825-164827.png)

体验DEMO<http://demo.404mzk.com/cssworld/6/float_test.html>

```css
.prev{ float: left; }
.next{ float: right; }
.title{ 
    margin: 0 70px;
    text-align: center;
}
```

```html
<div class="box">
    <a href="javascript:;" class="prev">prev</a>
    <a href="javascript:;" class="next">next</a>
    <h3 class="title">阿斯顿撒大所大所多撒多所多撒大所大所大所大所多撒大所多撒多撒</h3>
</div>
```

这里为什么h3要在next的后面?

## 6.2 float的天然克星clear

### 6.2.1 什么是clear属性

clear: none | left | right | both

clear: 元素盒子的边不能和前面的浮动元素相邻

none: 默认值

left: 左侧抗浮动

right: 右侧抗浮动

both: 两侧抗浮动

```css
li{
    width: 20px; height: 20px;
    margin: 5px;
    float: left;
}

li:ntf-of-type(3){
    clear: both;
}
```

假设有10个li

那么占据是2行还是3行?

结果是两行

因为clear属性是让自身不能和前面的浮动元素相邻

所有右边的li并不受clear影响

### 6.2.2 成事不足败事有余的 clear

clear属性只对块级元素有效

而::after等伪元素默认属性内联水平

所以一般设置是

```css
.clear:after{
    content: '';
    display: table;
    clear: both;
}
```

## 6.3 CSS世界的结界 BFC

### 6.3.1 BFC的定义

BFC: block formatting context: 块级格式化上下文

对应的还有IFC inline formatting content: 内联格式化上下文

如果一个元素具有BFC 内部子元素永远无法影响到外部的元素 同样外部元素也无法影响BFC元素

什么时候触发BFC

*. `<html>`根元素
*. float值不为none时
*. overflow的值为auto scroll hidden
*. display的值为table-cell table-caption和inline-block中的任何一个
*. position的值不为relative和static

只要元素符合上面任意调节 都无需clear:both清楚浮动

### 6.3.2 BFC与流体布局

## 6.4 最佳结界 overflow

### 6.4.1 overflow 裁剪界限border-box

```css

..boxbox  {{
    width: 200px; height: 80px;
    margin: auto;
    padding: 10px;
    border: 10px solid;
    overflow: hidden;
}
```

![overflow超出](/assets/QQ20180825-234654.png)

超出的部分是从padding内线开始切的

overflow有个不兼容的问题

chrome 如果容器可滚动(加上垂直滚动

padding-bottom也算在滚动尺寸以内

而ie firefox则不会

例如padding-bottom为10px

chrome则会在最下面留白10px

### 6.4.2 了解overflow-x和overflow-y

值:

visable: 默认值

hidden: 裁剪

scroll: 滚动

auto: 可以滚动时滚动 不可滚动时不滚

overflow-x和overflow-y 一个设置为visiable 另外一个设置了非visiable 则两者的表现都是auto

但是一个值是scroll auto hidden 另外一个是啥都不会有影响

### 6.4.3 overflow与滚动条

两个标签 标签overflow默认是auto

*. html
*. textarea

浏览器自定义滚动条

chrome为例

*. 整体部分: ::-webikit-scrollbar
*. 两端部分: ::-webokit-scrollbar-button;
*. 外层轨道: ::-webokit-scrollbar-track
*. 内层轨道: ::-webokit-scrollbar-track-piece
*. 滚动滑块: ::-webokit-scrollbar-thumb
*. 边角: ::-webokit-scrollbar-corner

```css
::-webikit-scrollbar{ /* 血槽宽度 */
    width: 8px; height: 8px;
}

::-webikit-scrollbar-thumb{ /* 拖动条 */
    background-color: rgba(0,0,0, .3);
    border-radius: 6px;
}

::-webikit-scrollbar-track{ /* 背景条*/
    background-color: #dd;
    border-radius: 6px;
}
```

### 6.4.4 依赖overflow的样式表现

文本省略

```css
.ell{
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
}
```

或者在高级浏览器试用...则无需overflow

```css
ell-rows-2{
    display: -webkit-box;
    -webkit-fox-orient: vertical;
    -webkit-line-clamp: 2;
}
```

### 6.4.5 overflow与锚点定位

> 锚点定位行为的触发条件

*. URL地址中的锚链与锚点定位行为的发生
*. 可focus的锚点元素处于focus状态

> 锚点行为的本质

锚点定位行为的发生 本质上是通过改变容器滚动高度或者宽度来实现的

而且定位行为的发生是由内而外的

即使元素设置了overflow:hidden的元素同样也是可滚动的

可以用这特性来实现切换轮播图

```css
.box {
    width: 20em;
    height: 10em;
    border: 1px solid #ddd;
    overflow: hidden;
}
.list {
    line-height: 10em;
    background: #ddd;
    text-align: center;
}
```

```html
<div class="box">
    <div class="list" id="one">1</div>
    <div class="list" id="two">2</div>
    <div class="list" id="three">3</div>
    <div class="list" id="four">4</div>
</div>
<div class="link">
    <a class="click" href="#one">1</a>
    <a class="click" href="#two">2</a>
    <a class="click" href="#three">3</a>
    <a class="click" href="#four">4</a>
</div>
```

体验地址: http://demo.cssworld.cn/6/4-2.php#four

原理就是通过锚点来实现的

但是因为锚点是由内而外的

即如果浏览器当前有滚动

切换选项的时候 最外边的页面也会跟着滚动

而改变这个可以用input的实现切换

```css
.box {
    width: 20em;
    height: 10em;
    border: 1px solid #ddd;
    overflow: hidden;
}
.list {
    height: 100%;
    background: #ddd;
    text-align: center;
    position: relative;
}
.list > input { 
  position: absolute; top:0; 
  height: 100%; width: 1px;
  border:0; padding: 0; margin: 0;
  clip: rect(0 0 0 0);
}
```

```html

<<divdiv  classclass="box">
    <div class="list"><input id="one">1</div>
    <div class="list"><input id="two">2</div>
    <div class="list"><input id="three">3</div>
    <div class="list"><input id="four">4</div>
</div>
<div class="link">
    <label class="click" for="one">1</label>
    <label class="click" for="two">2</label>
    <label class="click" for="three">3</label>
    <label class="click" for="four">4</label>
</div>
```

http://demo.cssworld.cn/6/4-3.php#four

## 6.5 float的兄弟position:absolute

两者都具有 块状化 包裹性 破坏性

### 6.5.1 absolute的包含块

包含块: 元素用来计算和定位的一个框

绝对定位元素的百分比宽度计算是相对于第一个position不为static的祖先元素计算的

1. 根元素: 为初始包含块 尺寸等于浏览器可视窗口大小
2. position是relative或static: 包含块则是最近块容器祖先盒的content box宽度计算的
3. position为fixed: 包含块是初始包含块
4. position为absolute: 则包含块是最近的position不为static的祖先元素建立

在第4点中

假如祖先元素是纯inline元素 规则就会比较复杂

假设给内联元素的前后各生产一个宽度为0的内联盒子, 则这两个内联盒子的padding box 外面的包围盒就是内联元素的包含块

如果该内联元素被跨行分隔了 那么包含块未定义 浏览器自行发挥

否则 包含块由祖先的padding box边界组成

#### 6.5.2 具有相对特性的无依赖absolute绝对定位

只设置postion: absolute 则不占据流空间

> 各类图标定位

```css
    position: absolute;
    margin: -6px 0 0 2px;
    width: 28px; height: 11px;
    background: url(hot.gif)
```

![无依赖absolute](/assets/QQ20180826-213226.png)

http://demo.cssworld.cn/6/5-5.php

其他应用地址: http://demo.cssworld.cn/6/5-6.php http://demo.cssworld.cn/6/5-7.php

### 6.5.3 absolute 与 text-align

```css
p{
    text-align: center;
}

img{
    position: absolute;
}
```

```html
<p>
    <img src="1.jpg"/>
</p>
```

照理来说 p设置的text-align 跟这个absolute的img没关系

但是因为幽灵节点的存在 图片会后移一半

http://demo.cssworld.cn/6/5-9.php

![absolute和text-align](/assets/QQ20180826-220305.png)

## 6.6 absolute与overflow

绝对定位元素不总被父级overflow属性剪裁

尤其当overflow在绝对定位元素及其包含块之间的时候

或者换句话说

```javascript
if ( overflow不是定位元素 && 绝对定位元素和overflow容器之间也没有定位元素){
    overflow无法对absolute元素进行裁剪
}
```

```html
<div style="position: relative;">
    <div style="overflow:hidden;">
        <img src="1.jpg" style="position: absolute;" /> <!--不裁剪-->
    </div>
</div>

<div style="position: relative; overflow:hidden;">
     <img src="1.jpg" style="position: absolute;" /> <!--裁剪-->
</div>

<div style="overflow:hidden;">
    <div style="position: relative;">
        <img src="1.jpg" style="position: absolute;" /> <!--裁剪-->
    </div>
</div>
```

## 6.7 absolute与clip

clip想起作用 元素必须是absolute或fixed

## 6.8 absolute的流体特性

### 6.8.3 absolute的margin:auto居中

推荐的绝对定位居中方法

```css
.element{
    wdith:300px; height:200px;
    position: absolute;
    left: 0; right: 0; top: 0; bottom: 0;
    margin: auto;
}
```

## 6.9 position: relative才是大哥

### 6.9.2 relative与定位

top/bottom 同时设置只有top有效

left/right 同时设置只有left有效

### 6.9.3 relative的最小化营销原则

*. 尽量不使用relative 如果想定位某些元素 尽量使用无依赖的绝对定位
*. 如果必须使用relative 该relative务必最小化

原来这样写的

```html
<div style="position: relative;">
    <img src="1.jpg" style="position: absolute; top:0;right:0" />
    <p></p>
    <p></p>
</div>
```


建议改为

```html
<div >
    <div style="position: relative;">
        <img src="1.jpg" style="position: absolute; top:0;right:0" />
    </div>
    <p></p>
    <p></p>
</div>
```

## 6.10 强悍的position: fixed固定定位

### 6.10.1 position: fixed 不一样的包含块

fixed的包含块是根元素

# 7 CSS世界的层叠规则

## 7.1 z-index 只是CSS层叠规则中的一叶小舟

z-index只和定位元素(position不为static的元素)才会起作用

## 7.4 务必记住的规则

*. 谁大谁上: z-index大的在上
*. 后来居上: 后面的DOM优先 

## 7.5 深入了解层叠上下文

### 7.5.2 层叠上下文的创建

```html
<div style="position:relative; z-index:auto;">
    <!-- 美女 -->
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:auto;">
    <!-- 美景 -->
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>

```

上面则是美女图在美景图前

因为父级的z-index为auto 所以无创建层级上下文

两个img直接作比较.所以美女z-index高在前

```html
<div style="position:relative; z-index:0;">
    <!-- 美女 -->
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:0;">
    <!-- 美景 -->
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>
```

两者父级z-index为0 但创建了层级上下文

两个div 层级相同 后来居上的美景div在上

两个img并不会进行层级比较

> css3与新时代的层叠上下文

*. 元素flex布局 同时z-index不为auto
*. 元素poacity值不是1
*. 元素mix-blend-mode值不是normal
*. 元素的filter值不是none
*. 元素isolation值不是isolate
*. will-change属性值为2~6的任意一个
*. -webkit-overflow-scrolling设为touch

### 7.5.3 层叠上下文与层叠顺序

*. background/border
*. 负z-index
*. block块状水平盒子
*. float浮动盒子
*. inline水平盒子
*. z-index: auto或者z-index: 0 不依赖z-index的层叠上下文
*. z-index: 正数

## 7.6 z-index负值深入理解

http://demo.cssworld.cn/7/6-1.php

```css
.box {
    background-color: blue;
}
.box > img { 
    position: relative; 
    z-index: -1; 
    right: -50px;
}

.context {
    transform: scale(1);
}
```

```html
<h4>.box非层叠上下文元素</h4>
<div class="box">
    <img src="1.jpg">
</div>

<h4>.box是层叠上下文元素</h4>
<div class="box context">
    <img src="1.jpg">
</div>
```

![z-index负值](/assets/QQ20180827-215901.png)

非层叠元素会盖住z-index负值

而当加了transform之后 .box变为了层叠元素

所以其background会被img盖住

## 7.7 z-index不犯二准则

*. 非浮层元素 避免设置z-index
*. z-index不超过2

# 8 强大的文本处理能力

## 8.1 line-hieght的另一个朋友font-size

### 8.1.1 font-size和vertical-align的隐藏故事

vertical-align百分比属性值 是相对于line-hieght计算的

line-height的部分属性值 是根据front-size计算的

所以vertical-align跟front-size是有关系的

例如实现一个图标垂居中于文本的案例

http://demo.cssworld.cn/8/1-1.php

主要CSS有

```css
p {
    font-size: 14px;
}

p > img {
width: 16px;
height: 16px;
vertical-align: 25%;
position: relative;
top: 8px;
}
```

vertical-align 具体的值如何计算?

vertical-align = 14px * 1(line-height) * 25% = 3.5px

而vertical-align = 3.5px的意思是 img基线超过父元素的基线3.5px

而文字的基线是 与父元素的基线对齐

所以此时需要图片的基线和文字的基线对齐

所以需要设置 vertical-align为 25%

---待补充 这里还没完全理解 ---

### 8.1.2 理解font-size与ex、em和rem的关系

ex: 1为 小写x的高度

em: 可以理解为 一个父元素中 font-size的倍数 不一定的话chrome默认为16px 其高度值可以理解为正方形的方格的边长

### 8.1.4 font-size: 0与文本的隐藏

Chrome上有个字号限制: font-size不能低于12px 否则当12px处理 除了0 0的话则隐藏

## 8.2 字体属性家族的大家长 font-family

font-family 支持的值有 字体名 和 字体族

字体名比较通俗易懂 例如`Arial, PingfangSC, "Microsoft YaHei"` 这些字体名

另外一类字体族

|key|value|
|---|---|
|serif|衬线字体|
|sans-serif|无衬线字体|
|monospace|等宽字体|
|cursive|手写字体|
|fantasy|奇幻字体|
|system-ui|系统UI字体|

### 8.2.1 了解衬线字体和无衬线字体

|名称|特点|常见字体|
|衬线字体| 笔画开始、结束的地方有额外的装饰而且笔画的粗细会有所不同的字体|宋体 ,Times New Roman, Georgia|
|无衬线字体| 没有额外装饰 粗细差不多 | 微软雅黑, Arial, Verdana, Tahoma, Helivetica, Calibri|
|等宽字体| 每个字符(te别是字母)宽度一致 | Consolas, Monaco, monospace|
### 8.2.2 等宽字体的实践价值

1. 代码展示 你去看下你的编辑器代码是否是等宽的?
2. 图案的展示 例如 `———————`, `-------`, `·······`
3. 结合ch 例如输入手机号 11位 设置输入框宽度为11ch 同时设置数字等宽, 就很直接明了的看到自己输入满了

### 8.2.3 中文字体和英文名称

一般在字体名时 不写中文 因为怕乱码 所以写英文会稳健点

window常见内置中文字体

|中文字体名| 英文字体名|
|---|---|
|宋体|Simsun|
|黑体|SimHei|
|微软雅黑| Microsoft Yahei|

macos常见中文字体

|中文字体名| 英文字体名|
|---|---|
|苹方|PingFang SC|
|华文黑体|STHeiti|

## 8.3 字体家族其他成员

### 8.3.1 貌似粗犷、实则精细务必的font-weight

`font-weight`: 表示文字的粗细

可选值

1. normal: 正常 相当于400
2. bold: 加粗 相当于700
3. lighter: 比从父元素继承的更细
4. bolder: 比从父元素继承过来更粗
5. 100~900: 只能取整百的 9个值

|继承值|bolder|lighter|
|---|---|---|
|100|400|100|
|200|400|100|
|300|400|100|
|400|700|100|
|500|700|100|
|600|700|100|
|700|900|400|
|800|900|700|
|900|900|700|

### 8.3.2 具有近似姐妹花属性值的font-style

|值|作用|
|---|---|
|normal| |
|italic| 寻找字体文件的斜体 没有就按oblique规则走|
|oblique| 让字体斜着|

## 8.4 font属性

### 8.4.1 作为缩写的font属性

font的完整写法是

[ [ font-style || font-variant || font-weight ]? font-size [ /line-height ]? font-family ]

font要没注明line-height则会被还原为 normal

需要注意的是 font-size 和 font-family是必须的 否则整个属性不起作用

## 8.5 真正了解@font face规则

### 8.5.1 @font face的本质是变量

```css
@font-face {
font-family: 'example';
src: url(example.ttf);
font-style: normal;
font-weight: normal;
unicode-range: U+0025-00FF;
font-variant: small-caps;
font-stretch: expanded;
font-feature-settings: 'ligal' on;
}
```

> font-family

这里的font-family可以看成一个字体的变量 可以随便取 例如 `$`;

注意不要和常用的字体重名即可

> src

src引入的资源可以是系统字体 也可以是外链字体

如果是系统安装字体 local() //IE9机器以上才支持

如果是外链 使用url()

常见设置字体 做法有

```css
@font-face{
font-family: ICON;
src: url('icon.eot') fotmat('eot'); # 兼容模式不认识下吗的?号 所以在这补充 在实际中午作用
src: url('icon.eot?#iefix') format('embedded-opentype'), #IE6~8仅支持这种字体
并解决IE9之前解析多个url 会把长长字符串当做一个地址导致404 这里主要用?把后面都当成参数
url('icon.woff2') format('woff2'), #web open font format 二期标准
url('icon.woff') format('woff'), #web open font format 一期标准
url('icon.ttf') format('typetrue'),#android兼容性好 但体积大
url('icon.svg#icon') format('svg'); #为了兼容IOS4.1及其以前版本
}
```
上面方案弊端很多 所以可以改成

```css
@font-face{
font-family: ICON;
src: url('icon.eot');
src: local('😁'),//用IE不认识的字符串解决 IE9以前会读取到这个属性 并且高级浏览器可以用woff2
url('icon.woff2') format('woff2'),
url('icon.woff') format('woff'),
url('icon.ttf')
}
```

> font-style

```css
@font-face {
font-family: 'I';
font-style: normal;
src: local('FZYaoti');
}

@font-face {
font-family: 'I';
font-style: 'italic';
src: local('FZShuTi');
}

.i{
font-family: I;
}
```

```html
<p><i class='i'>类名是i 标签是i</i></p>
<p><span class='i'>类名是i 标签是</span></p>
```
定义多种font-style 在使用字体时 根据font-style使用多套字体

> font-weight

定义多个 font-weight的@font-face 供使用者根据font-weight引用到多种情况

> 5 unicode-range

用于让特定的字符或特定范围的字符使用指定的字体

例如微软雅黑的双引号左右间隙不均 想用Simsun字体的


```css
@font-face {
font-family: quote;
src: local('Simsun');
unicode-range: U+201c, U+201d;
}

.font{
font-family: quote, 'Microsoft Yahei';
}
```

### 8.5.2 @font-face与字体图标技术

以后字体图标技术只会越来越边缘化 因为和SVG相比 唯一优势就是兼容老版本IE了

SVG同样是矢量的 同样颜色可控 但占用资源少 加载体验更好

## 8.6 文本的控制

### 8.6.1 text-indent 与内联元素缩进

设计的本意是 用来做 例如 段落首行空两个字空格的

但是用的人比较少....

都是用这个来做负值隐藏内容

text-index仅对第一行内联盒子内容有效

### 8.6.2 letter-spacing与字符间隔

字符间隔可负数 一般做些动画

### 8.6.3 word-spacing与单词间隔

主要作用增加空格的间隙宽度

### 8.6.4 了解word-break和word-wrap的区别

首先了解下

word-break:normal

word-break: break-all; #允许任意非CJK(chinese/japanese/korean)文本间的单词断行

word-break: keep-all; # 不允许CJK文本中的单词换行 只能在半角空格或连字符处换行 非CJK文本表现和normal一致

另外的属性

word-wrap: normal;

word-wrap: break-word; # 一行单词中实在没有其他靠谱的换行点的时候换行

http://demo.cssworld.cn/8/6-5.php

![了解word-break和word-wrap的区别](/assets/QQ20180731-111326.png)

### 8.6.5 white-space与换行和空格的控制

white-space声明的是如何处理元素内的空白字符

|属性|换行|空格和列表|文本环绕|
|---|---|---|---|
|normal|合并|合并|环绕|
|nowrap|合并|合并|不环绕|
|pre|保留|保留|不环绕|
|pre-wrap|保留|保留|环绕|
|pre-line|保留|合并|环绕|

http://demo.cssworld.cn/8/6-6.php

### 8.6.6 text-align与元素对齐

...作者太灵活了...

### 8.6.7 如何解决text-decoration下划线和文本重叠的问题

主要靠border

### 8.6.8 一本万利的text-transfrom字符大小写

text-transfrom: uppercase|lowercase

用于输入身份证和验证码

用户由于输入什么大小写的时候.


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

# 10. 元素的显示与隐藏

## 10.1 display与元素显隐

background-image在display: none时

firefox 中的 background-image不加载

chrome和safari 在当前元素的父元素是display:none 才不加载

ie则都加载

当元素 display: none的img标签 什么浏览器都会加载图片

## 10.2 visibility与元素的显隐

父元素设置visibility:hidden 子元素也会隐藏是因为其继承性

子元素若设置了visibility:visiable; 则子元素会显示出来

# 11. 用户界面样式


## 11.1 和border形似的outline属性

outline书本书介绍第一个真正意义上不占据任何空间的属性

可以用来做头像裁剪等功能

核心css就是

```css
.crop-area {
    width: 80px; height: 80px;
    outline: 256px solid rgba(0,0,0, .5);
    cursor: move;
}
```

http://demo.cssworld.cn/11/1-1.php

# 12. 流向的改变

## 12.1 改变水平流向的direction

### 12.1.1 direction简介

direction: ltr; //默认 left to right
         : rtl; //right to left

### 12.1.2 direction的黄金搭档 unicode-bidi

direction属性似乎只能改变图片或者按钮的显现顺序

但对于纯内容 就好像并没有什么效果 这个就要用unicode-bidi

unicode-bidi: normal;//默认
            : embed;//控制元素自身不被外元素控制unicode-bidi
            : bidi-override;

一个demo就能表现出unicode-bidi的属性

http://demo.cssworld.cn/12/1-4.php

```css
.rtl {
    direction: rtl;
    unicode-bidi: bidi-override;
}
.rtl > span {
    background-color: #ddd;
}
```

```html
<p class="rtl">开头是我，<span style="unicode-bidi:normal;">这是中间</span>，然后是结束</p>
<p class="rtl">开头是我，<span style="unicode-bidi:embed;">这是中间</span>，然后是结束</p>
```

结果:

![unicode-bidi](/assets/QQ20180828-225557.png)

## 12.2 改变CSS世界纵横规则的writing-mode

方便方向,但是笔者觉得适用场景有效..在这里不描述..


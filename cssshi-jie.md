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
    border-color: transparent #f30 transparent  ;
}

.sanjiaoxing4{
   border-color:  #f30 transparent  ;
}

.sanjiaoxing5{
    width: 10px;
    height: 10px;
   border-color:  #f30 #00f #396 #0f0  ;
}

.sanjiaoxing6{
    width: 10px;
    height: 10px;
   border-color:  #f30 transparent transparent ;
}

.sanjiaoxing7{
    border-width: 10px 20px;
    border-style: solid;
   border-color:  #f30 #f30 transparent transparent ;
}
```

###4.4.6 border等高布局技术

# 6 流的破坏与保护





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

ex:  1为 小写x的高度

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
3. 结合ch 例如输入手机号 11位 设置输入框宽度为11ch 同时设置数字等宽,  就很直接明了的看到自己输入满了

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



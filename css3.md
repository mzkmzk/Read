# CSS3

#0总结

1. 第一章详细讲了各种选择器.
2. 第二章讲述了字体和文本的相关属性.
3. 第三章降序了背景边框的属性.
4. 第四章简单讲述了大小、定位等属性.
5. 第五章盒模型,重要的模型讲述得还可以.
6. 第六章表格和列表还有几本的响应式属性.
7. 第七章为变形和动画,通俗易懂的列举了属性
8. 总体来说,这个CSS部分较为枯燥,很多属性需要知道和了解,特别是动画和变形,别太依赖JS.CSS部分具备了入门到初级前端应该知道的知识.

---

#1 级联样式与CSS选择器

#1.1 CSS基本使用

###1.1.1 引入CSS
	
	/* 不建议@import 性能问题 */
	<link href="CSS文件路径" type="text/css" rel="stylesheet">

##1.2 选择器

###1.2.1 元素选择器

	/* 仅对P标签有作用 */
	p{...}
	
###1.2.2 属性选择器
	
	/* 仅对具有attr属性的E元素起作用 */
	E[attr]{...}
	
	/* 仅对attr=value的E元素起作用 */
	E[attr=value]{...}
	
	/* attr的值以空格为分隔符分开,其中一个值等于value即可的E元素 */
	E[attr~=value]{...}
	
	/* attr的值以连字符'_'为分隔符分开,其中一个值等于value即可的E元素 */
	E[attr |=value]{...}
	
	/* attr属性包含value值的E元素 */
	E[attr*="value"]{...}
	
	/* attr属性以value为开头的E元素 */
	E[attr^="value"]{...}
	
	/* attr属性以value为结尾的E元素 */
	E[attr$="value"]{...}
	
###1.2.3 ID选择器

	/* id为xx的元素起作用 */
	#xx{...}
	
	/* id为xx的E元素起作用 */
	E#xx{...}
	
###1.2.4 class选择器
	
	/* E为可选是否填写指定元素,my_class为指定lcass */
	[E].my_class{...}		
	
	/* class同时包含class1.class2 */
	.class1.class2{...}
	
###1.2.5 包含选择器

	/* div内所有class为a的元素起作用,无论元素是否为子元素 */
	div .a{...}	
	
	/*注意 包含关系有空格,同时满足关系无空格 */
	.class1 .class2
	
###1.2.6 子选择器

	/* 仅对div的子元素且class为a的元素起作用 */
	div>.a{...}	

###1.2.7 兄弟选择器

	/* id为android的标签 之后出现的且class为long的同级元素 */
	#android ~ .long{...}	
	
###1.2.8 选择器组合

	/*逗号分隔或关系,仅符合一种选择器即起作用 */
	#a,.b,div>.a{...}	
	
	
###1.2.9 伪元素选择器

1. :first-letter
2. :first-line	
3. :before
4. :after

`:first-letter`和`:first-line`可实现首字大写与首行大写之类的问题

注意,这两个伪属性只对块元素`div,p,section`等元素起作用,对内嵌`span`等必须设定height与width、或postition:absolute、或display:block.

	span{
		disply:block;
	}
	
	//首字变蓝
	.span:first-letter{
		color:#00f;
	}
	
`:before`与`:after`用于在指定元素前/后插入内容,参考下面的内容相关属性.	
###1.2.10 内容相关属性

1. include-source: 属性值为url(url)
2. content: 作用向指定元素之前或元素之后插入指定内容,该属性的值可以是字符串、url(url)、attr(alt)、counter(name)、counter(name,list-style-type)、open-quote和close-quote等格式.
3. quotes: 该属性的值可以是两个以空格分隔的字符串,前面代表open-quotes,后面带便quote.
4. counter-increment:该属性用于定义一个计数器,值为该计数器的名称.
5. counter-rest: 重置计数器.

		
		eg:
		<!-- 子div前添加文字和设置样式 -->
		div>div:before{
			content: '文字';
			colol: blue;
		}
		
		<!-- 含有class为on的子div之后的添加图片 -->
		div>div.no:after{
			content:url("K.gif");
		}
		
		<!-- 配合quetos 对的...其实这属性貌似并没神马卵用.. -->
		<!-- 是的,你没猜错..其实close-quote写在before里也是可以的.. -->
		div>div{
			quotes: "<<" ">>";
		}
		
		div>div:before{
			content: open-quote;
		}
		
		div>divafter{
			content:close-quote;
		}
		
		<!-- counter-increment添加编号1. 2. 3. -->
		div>div{
			counter-increment: fucking_K;
		}
		
		div>div:before{
			content: counter{fucking_K} '.';
			font-size: 20pt;
		}
		
		<!-- 拓展样式 更多参数<https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-type> -->
		div>after{
			content: counter{fucking_K,lower-roman} '.';
			font-size: 20pt;
		}
		
		<!-- 多级编号 -->
		div>h2{
			counter-increment: fucking_K;
		}
		
		div>div{
			counter-increment: funcking_K2;
		}
		
		div>h2:before{
			counter-increment: funcking_K;
			counter-reset: fucking_K2;
		}
		
		div>div:before{
			content: counter: funcking_K2;
		}

###1.2.11 伪类选择器

Selecttor可省略.不作为匹配条件

1. 结构性伪类选择器

	1.1 Selector:root: HTML元素中,永远指向`<html.../>`元素.
	
	1.2 Selector:first-child: 匹配其父元素的第一个节点.
	
	1.3 Selector:last-child: 匹配选择器,而且是其父元素的最后一个节点.
	
	1.4 Selector:nth-child(n): 匹配选择器,而且是其父元素的第n个节点.
		
	1.5 Selector:nth-last-child(n): 匹配选择器,而且是其父元素倒数第n个节点.
	
	1.6 Selector:only-child: 匹配选择器,而且是其父元素唯一的节点. 
	
	1.7 Selector:first-of-type: 匹配选择器,而且是其同类型的兄弟元素中的第一个元素.
	
	1.8 Selector:last-of-type: 匹配选择器,而且是其同类型的兄弟元素中的最后一个元素.
	
	1.9 Selecttor:nth-of-type(n): 匹配选择器,而且是其同类型的兄弟元素中的第n个元素.
	
	1.10 Selector:nth-last-of-type(n): 匹配选择器,而且是其同类型的兄弟元素中的倒数第n个元素.
	
	1.11 Selector:only-of-type: 匹配选择器,而且是其同类型的兄弟元素的唯一一个元素. 
	
	1.12 Selector:only:empty: 匹配选择器,而且其内部没有任何子元素(包括文本)的元素. 

	拿first-child和first-of-type举例
	
		<ul>
		<img .../>
			<li>...</li>
		</ul>
	
		//匹配不到任何元素,li必须是父元素的第一个元素.
		li:first-child{...}
	
		//匹配到li,是在父元素中匹配到的类型中的第一个即可.
		li:first-of-type{...}	
	
	`-child匹配是会把其他不同类型的兄弟节点算进去,而-of-type只计算同类型的兄弟元素`	
	
	其中nth-child(n)、nth-last-child(n)、nth-of-type(n)、nth-last-of-type(n)支持参数

	1. odd:匹配奇数的元素
	2. even:匹配偶数的元素
	3. xn+y: 匹配第(x乘以n)加y的元素	

2. UI元素状态伪类选择器

	2.1 Selector:link: 匹配未访问的元素
	
	2.2 Selector:visited: 匹配访问过的元素
	
	2.3 Selector:active: 匹配处于用户被激活(鼠标点击与释放的过程中)的元素
	
	2.4 Selector:hover: 匹配鼠标悬停状态的元素
	
	2.5 Selector:focus: 匹配已得到焦点的元素
	
	2.6 Selector:enabled: 匹配当前处于可用状态的元素
	
	2.7 Selector:disabled: 匹配当前不可用状态的元素
	
	2.8 Selector:checked: 匹配当前选中状态的元素
	
	2.9 Selector:ready-only: 匹配当前只读状态的元素
	
	2.10 Selector:read-write: 匹配当前处于读写状态的元素
	
	2.11 Selector::selection: 匹配当前被选中的内容.
	
3. 特殊的伪类选择

	3.1 Selector1:not(Selector2): 匹配符合Selector1选择器,但不符合Selector2选择器的元素
	
		//尝试了Selector2 写了两个条件,无法筛选.然后这样就可以- -.
		:not(.container):not(.row):not(h3) {...}
	
	3.2 Selector:target: 匹配符合Selector选择器且必须是命名描点的且正在被访问的目标选择器.	
	
		:target{
			background-color: #fff;
		}
		
		<a href="#K" />
		
		//当正在浏览此div,:target生效
		<div id="K">
			...
		</div>
 		
###1.3 浏览器专属属性	

1. -ms- IE内核
2. -moz- Gecko内核(Firefox)
3. -o- Opera浏览器
4. -webkit- Webkit内核(Chrome、Safari)

###1.4 JS修改CSS

	//修改属性
	//注意属性名中采用驼峰命名,例如改变background-color,属性名要写backgroundColor
	document.getElementById("id").style.属性名=属性值;
	
	//修改class
	document.getElementById("id").className=class名称;

---
	
#2 字体与文本相关属性

##2.1 字体相关属性

1. font: 可添加font-style,font-variant,font-weight,font-size,line-height,font-family等属性.
2. color: 字体颜色,可颜色名,十六进制颜色值,RGB,HSL
3. font-family: 设置字体,可用`,`分割按顺序使用字体
4. font-size: 字体大小
5. font-size-adjust: 对字体进行微调.
6. font-streth: 改变字体横向拉伸,narrower既横向压缩,wider横向拉伸.
7. font-style: 文字风格,常用属性有italic斜体,oblique倾斜体.
8. font-weight: 是否加粗,属性可数值,也可lighter、normal、bold、bolder,越来越粗.
9. text-decoration: 文字修饰线,常用属性有:none、blink闪烁、underline、line-through中划线、overline上划线.
10. font-variant: 设置文字大写字母格式,属性有normal、small-caps(小型大写字母).
11. text-shadow: 文字阴影效果,后面会详细提.
12. text-transform: 字母大小写none,capitalize首字母大写、unpercase全部大写、lowercase全部小写;.
13. line-height: 行高,负值时候可用来实现阴影效果.
14. letter-spacing: 字符之后的间隔.最后一个字不受影响
15. word-soacing: 单词之间的间隔.

###2.1.1 字体添加阴影

text-shadow,多组阴影用,分割

1. color: 指定阴影颜色.
2. xoffset: 指定阴影在横向上的偏移量.(可负值)
3. yoffset: 指定阴影在纵向上的偏移量.(可负值)
4. redius: 指定阴影的模糊半径,半径越大越模糊	.
	
		<!-- 向右下角和左上角加阴影加阴影-->
		<span style="text-shadow:5px 5px 2px gray,-5px 5px 2px gray">帅哥麦</span>
	
###2.1.2 微调字体大小

font-size-adjust用调节同样字号不同字体的效果.	
	
font-size-adjust通过设定aspect值从而控制不同字体的大小.

aspect等于字体小写x的高度除以该字体的大小.

		#div {
			font-size: 16pt;
			font-family: "狂草";
			font-size-adjust:0.52;
		}	
		
		#div2 {
			font-size: 16pt;
			font-family: "楷书";
			font-size-adjust: 1.22;
		}
		
##2.2 CSS3支持的颜色表示方法

1. 颜色名:white,red,greenyellow
2. 十六进制
3. rgb(r,g,b)
4. rgba(r,g,b,a): a在0~1之间,0表示完全透明
5. hsl(hue,saturation,lightness):色调,饱和度,亮度控制.
6. hsla(h,s,l,a).

##2.3 文本相关属性

此类属性用于控制整段或整个div的

1. text-indent: 文本的缩进
2. text-overflow: 

	2.1 clip: 要指定overflow:hidden,隐藏溢出的文字
	
	2.2 ellipsi: 要指定了overflow:hidden,溢出时候用...标记
	
3. vertical-align:指定内容垂直方式

	3.1 auto: 自动对齐
	
	3.2 baseline: 默认值,将支持valign属性的元素的文本内容与基线对齐
	
	3.3 sub: 文本下标对齐
	
	3.4 super: 文本上标对齐
	
	3.5 top: 默认值,将支持valign属性的元素的文本内容与元素的顶端对齐
	
	3.6 middle: 默认值,将支持valign属性的元素的文本内容对齐到元素的中间.
	
	3.7 bottom: 默认值,将支持valign属性的元素的文本内容与元素的底端对齐.
	
4. text-align: left,right,center,justify(两端对齐)

5. direction: 用于设置文本流入的方式,ltr(左到右),rtl(右到左),不会影响数字和拉丁文字母(除拉丁文标点符号).

6. word-break: 用于设置目标组件中文本的换行方式

	6.1 normal: 按浏览器默认,西方文字:只会在半角空格、连字符地方换行.中文,任意文字后换行.
	
	6.2 keep-all: 只能在半角空格或连字符换行
	
	6.3 break-all: 允许在连续单词中间换行
	
7. white-space:处理目标组件中文本对空格的处理

	7.1 normal: 自动换行
	
	7.2 nowrap: 强制文本一行显示,除非遇到`<br />`
	
8. word-wrap: 用于设定目标组中文本内容的换行方式

	8.2 normal: 浏览器默认
	
	8.3 break-word: 允许单词中间换行.
	
		
word-break兼容性不好,单词中换行最好使用`word-wrap:breakword;`

word-break和word-wrap区别:

1. word-break: break-all:会让每一行文本最后一个单词换行.(不浪费一点空间)

2. word-wrap: break-word:会让太长单词之前自动换行.

3. 情况很多,不好理解.具体参考<http://www.cnblogs.com/2050/archive/2012/08/10/2632256.html>

##2.4 服务器字体

1. 下载.ttf/.otf字体
2. 定义服务器字体
		
		<!-- src ...;后可跟字体样式属性 -->
		@font-face {
			font-family: 404_K;
			src: url(url) format("字体格式");
			font-weight: blod;
		}

###2.4.1 优先使用客户端字体

		@font-face {
			font-family: 404_K;
			src: location("微软雅黑"),url(url) format("字体格式");
			font-weight: blod;
		}

---

#3 背景、边框和补丁相关属性

##3.1 背景相关属性
	
1. background: 可控制背景颜色、背景图片、背景重复方式.
2. background-attachment: 背景图片是否随对象内容滚动还是固定.

	2.1: scorll:背景随内容滚动
	
	2.2 fixed:背景图片固定.
	
3. background-color: 背景色
4. background-image: 背景图片,背景图片会覆盖背景色(假如同时设置了).
5. background-position: 可百分百可数值,只设横百分比,纵百分比为50%.
6. background-repeat:是否平铺

	6.1 repeat:平铺
	
	6.2 repeat-x:横向平铺
	
	6.3 repeat-y:纵向平铺
	
	6.4 no-repeat:不平铺

---
以下为CSS3新增

7. background-clip: 指定背景覆盖范围. 

	7.1 border-box: border-box(覆盖border,padding,content)
	
	7.2 padding-box: 覆盖padding,content(默认)
	
	7.2 content-box,只覆盖content.
	
8. background-origin: 设置背景覆盖的起点,有点和background-position类似.或指定从哪个区域的开始放置背景图片(content,padding,border).Chrome和Safari使用要开-webkit开头.
9. background-size: 设置图片大小.		

	9.1 数值: 12px,13px 占宽12px,高13px;
	
	9.2 百分比: eg 80% 10%,宽占百分之80,高占百分之10%
	
	9.3 auto:只有一个值能为auto,根据另外一个值自动缩放(这个对响应式非常有用,保证图片不走形 100% auto,完全显示图片).

CSS3提供了多背景控制,属性还是上面的,多个背景属性用,隔开.

##3.2 边框相关属性


1. border: 设置边框样式,粗细,线型,颜色.
2. border-color: 边框颜色..任何颜色值
3. border-style: 边框样式

	3.1 none:无边框
	
	3.2 hidden: 隐藏边框,于none类似,当在表中可解决边框冲突
	
	3.3 dotted: 电线边框
	
	3.4 dashed: 虚线边框
	
	3.5 solid: 实现边框
	
	3.6 double: 双线边框
	
	3.7 grove: 3D凹槽边框
	
	3.8 ridge: 3D凸槽边框
	
	3.9 inset: 3D凹入边框
	
	3.10 outset: 3D凸出边框(个人感觉3.7,3.8和3.9,3.10类似,不过立体感弱一点)

4. border-width: 边框线框.任何有效长度值.若只设width,是没法占位置的.

---

以下top bottom right left皆有.

1. border-top: 设置上线的复合属性,可设置边框样式,粗细,线型,颜色.
2. border-top-color: 上边框颜色.
3. border-top-style: 边框样式
4. border-top-width: 上边线框.
5. border-top-colors: 渐变属性,CSS3新增,假设border-top-width为Npx,则可以设置N种颜色,从外框往里渐变,若设置的颜色个数小于width,最后一个颜色会作为之后的颜色.

---
圆角边框,CSS3新增

6. border-radius: 圆角边框,数值代表圆角半径,半径越大,越圆,
7. 还有border-top-left-radius、border-top-right-radius、border-bottom-left-radius、border-bottom-right-radius属性.

---
图片边框

1. border-image: 语法:`<border-image-source>  <border-image-slice>[/boder-image-width]?  <border-image-repeat>`
2. border-image-source: none/url("图片路径")
3. border-image-slice: 可输1~4个百分比或数值用于切割图片,按上下左右切割,得出9张小图片,默认情况中间会丢弃.但例如 10 20&&fill中间就会保留放在内容中间.
4. border-image-width: 为上面切割的图片设置宽度.可设置1~4个长度,百分比,或auto,指定image-slice的宽度.
5. border-iamge-repeat: 指定图片覆盖方式.可设0~2个值,分别代表横竖.

	5.1 stretch: 拉伸覆盖
	
	5.2 repeat: 平铺覆盖
	
	5.3 round: 取整平铺,和repeat类似,
	
		当最后一张图片不能显示超过一半,则不显示最后一张图片,通过拉伸前面的图片实现填充.
	
		当最后一张图片可以显示超过一半,则显示最后一张图片,压缩前面的图片.
		
border存在则border-image相关属性不起作用.

#3 大小、定位、轮毂相关属性

##3.1 大小相关属性 

1. height: 设定高度
2. max-height: 设定最大高度.当此属性小于min-height,则自动转化为min-height,下面属性同理
3. min-height: 设定最小高度.
4. width: 设定宽度
5. max-width: 最大宽度
6. min-widht: 最小宽度

CSS3新增属性

1. box-sizing: 用于设定width和height控制content padding border等哪些区域.

	1.1 content-box: 只控制content
	
	1.2 padding-box: 控制content+padding
	
	1.3 border-box: 控制contetn+padding+border
	
2. resize: 设置用户能否拖动组件来改变组件的大小

	2.1 none: 不能改变大小
	
	2.2 both: 能改变大小,但只能按比例缩放
	
	2.3 horizontal: 不允许改变高度,能改变宽度
	
	2.4 vertical: 不允许改变宽度,能改变高度


##3.2 定位相关属性

定位作用为在于布局,漂浮~.除了position以外的其他属性不对static起作用.

1. position 定位方式

	1.1 absolute: 组件会漂浮在页面上,不考虑周围内容
	
	1.2 relative: 参考前一个组件的位置来定位
	
	1.3 static: 默认值,没有定位,元素出现在正常的页面流中.
	
	1.4 fixed: 绝对定位,以浏览器为定位参考
	
2. z-index: 数值觉得漂浮重叠优先级.数值越高优先级越高.
3. top: 设定最近一个具有定位设置的父元素向顶部偏移.
4. right: 设定最近一个具有定位设置的父元素向右侧偏移.
5. bottom: 设定最近一个具有定位设置的父元素向底部偏移.
6. left: 设定最近一个具有定位设置的父元素向顶部偏移.

漂浮居中定位设置很常用的一招.

	margin: auto;  
	position: absolute;  
	top: 0; left: 0; bottom: 0; right: 0; 
	
原理及其它居中方式<http://blog.csdn.net/freshlover/article/details/11579669>.

###3.3 轮廓相关属性

轮廓不占页面实际物理布局面积,可实现"光晕效果".

1. outline: 可设置轮廓颜色 线宽 线型.
2. outline-color: 轮廓颜色.
3. outline-style: 和之前的border-style一致.
4. outline-width: 轮廓宽度.
5. outline-offset: 设置轮廓和边框的距离

#4 盒模型与布局相关属性

##4.1 布局相关属性

1. float: 可left/right.当设置了该属性强制display为block,它会紧靠组件的左/右,直到遇到边框/padding/margin或另外一块组件(display为block)为止.)
2. clear: 设置该组件能否出现浮动组件.

	2.1 none: 默认,你随便飘~
	
	2.2 left: 不允许出现左飘~
	
	2.3 right: 不允许出现右飘~
	
	2.4 both: 你丫的别给我飘~.
	
3. clip: 对组件裁剪设置,(只有在position为absolute且overflow:hidden,效果才会较好.)
	
	3.1 rect(Num_A,Num_B,Num_C,Num_D),定义一个矩形,只有在矩形内才显示出来.这4个数有点奇葩,和别的不一样,定义的矩形为,横向显示(Num_D~Num_B)的内容,纵向显示(Num_A~Num_C)的内容.	当Num为auto,表示该边不做裁剪.
	
4. overflow: 设置等组件不能容纳内容显示时,采取的措施

	4.1 visble: 默认,不剪切也不加滚动条.
	
	4.2 auto: 添加滚动条显示全部内容.
	
	4.3 hidden: 裁剪不能显示的部分.
	
	4.4 scroll: 不够内容有没有超,总显示滚动条.

5. overflow-x: 仅对横向方向起作用,属性同overflow.
6. overflow-y: 仅对纵向起作用.属性同overflow.
7. visibility: visibl/hidden.隐藏时仍占据网页空间
8. display: 设置盒模型.

	8.1 block类型: 默认占据一行,可CSS设置宽高度,默认此属性的元素有`<div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>`等.

	8.2 inline类型: 默认允许一行放多个组件,CSS设置宽高度无作用.默认此属性的元素有`<a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>`等.当两个block中间有inline他们是不可以在同一行的,
	
	8.3 none: 隐藏组件,不占网页空间.
	
	8.4 inline-block盒模型: 不占据一行,但可以设置宽高度.这个也可以实现多栏布局.默认情况下,多个inline-block盒模型会对齐底部.改变对齐顶部使用`vertical-align:top`,默认此属性的元素有`<img>、<input>`等.当两个block中间有inline-block他们是可以在同一行的,
	
	8.5 inline-table盒模型: 与inline-block类似,分别在表格设置inline-table或inline-block,但是inline-table设置width会拉伸单元格,inline-block会把内容多余的空间在右边留白,而不是给单元格.
	
		
		//关于表格的盒模型,其实作用不大,作用大概就是可以把div当做表格来显示.
		1. table: 将组件变为表格.
		2. table-caption: 将组件变为表格标题.
		3. table-cell: 将组件变为单元格.
		4. table-column: 将组件变为表格列.
		5. table-column-group: 将组件变为表格列组.
		6. table-row: 将组件变为表格行.
		7. table-row-group: 将组件变为表格列组.
		8. table-header-group: 将组件变为表格头部分.
		9. table-footer-group: 将组件变为表格尾部分.

	8.6 list-item盒模型: 可以将组件转为ul等列表元素.
	
	8.7 run-in盒模型: 和inline类似,但假如后面紧跟block,那么run-in会包含在后面的block中.
	
	8.8 box模型: CSS3新增.当display为box时,组件可以使用以下的属性控制box
	
		1. box-orient:horizontal/vertical. 
		
			1.1 当属性为horizontal时候,没有为子元素定义高度,则高度等于父元素的高度.
			
			1.2 当属性为vertical时候,没有为子元素定义宽度,则高度等于父元素的宽度.
			
		2. box-oridinal-group: 设置box盒模型中子元素的显示顺序.
		
		3. box-flex: 设置模型自动适应宽度比例,
		
			3.1 例如box盒模型多余150px,第一个元素的box-flue为1,第二个元素的box-flue为2,则把多余的宽度分50px给第一个元素,分100px给第二个元素.	
	
##4.2 给盒子加阴影

CSS3新增的,主要使用box-shadow属性.是一个复合属性,包括以下5个属性.

1. hOffset: 控制阴影在水平方向的偏移.
2. vOffset: 控制阴影在纵向的偏移.
3. blurLength: 控制阴影的模糊程度.
4. scaleLength: 控制阴影的缩放程度.
5. color: 控制阴影颜色


eg:
	
	<!-- 右下阴影,模糊程度10px,缩小阴影区域-10px -->
	<div style="box-shadow: 10px 8px 10px -10px red">..</div>

##4.3 column-count分栏

column-count分栏为CSS3新增,属性主要有:

1. columns: 复合属性,可指定栏目宽度,栏目数.但内容大于容器时,Firefox和Chrome会增加每栏的宽度,Opera会保持栏目宽度,但增加栏目数.
2. column-width: 指定每个栏目的宽度.
3. column-count: 指定栏目个数.
4. column-rule: 符合属性,指定栏目之间分隔条的宽度 样式 颜色. 
5. column-rule-width: 指定分隔条的宽度.
6. column-rule-style: 指定分隔条的样式,属性和border-style的样式一样.
7. column-rule-color: 指定分隔条的颜色.
8. column-gap: 设定栏目的间距.
9. column-fill: 设定栏目的高度

	9.1 auto: 根据内容多少变化高度.
	
	9.2 balance: 高度统一为最长的那栏的高度.

#5 表格、列表及media query

##5.1 表格相关属性

1. border-collapse: 控制两个单元格的边框分开还是合并.

	1.1 seperate: 边框分开.(双线)
	
	1.2 collapse: 边框合并.(单线)
	
2. border-spacing: 前提为border-collapse为seperate时,设置分割间距.
3. caption-side: 设置表格标题在表格的哪边,必须与`<caption.../>`一块使用,属性有top bottom/left/right.
4. empty-cells: 前提为border-collapse为seperate,设置单元格为空时,是否显示单元格边框,属性有show/hide.	
5. table-layout: 设置表格宽度布局方法.

	5.1 auto: 默认值.
	
	5.2 fixed 固定布局.
	
		//fixed表格宽度计算方式.
		1. 如果设置了<col../>或<colgroup../>每列的宽度,则表格宽度等于所有列宽的总和.
		2. 如果表格内第一个单元格设置了宽度,则表格宽度所有所以列宽的总和.
		3. 直接平均分配每列的宽度,忽略单元内容的实际宽度.
		
##5.2 列表相关属性

1. list-style: 复合属性,可以指定list-style-image list-style-position list-style-type.		
2. list-style-image: 指定列表标记的图片.
3. list-style-position: 设定列表标记出现的位置

	3.1 outside: 列表项标记防盗列表元素之外.
	
	3.2 inside: 列表想标记放在列表元素之内.

4. list-style-type : 设置项标记符号,list-style-image会覆盖list-style-type属性,全部属性[参考](http://www.w3school.com.cn/cssref/pr_list-style-type.asp).常用的有

	4.1 decimal: 默认,阿拉伯数字. 
	
	4.2 disc: 实心圆
	
	4.3 upper-roman: 大写罗马数字.

	4.4 none: 不使用符号.
	
	4.5 lower-alpha: 小写英文字母
	
	4.6 upper-alpha: 大写英文字母
	
##5.3 控制光标的属性

cursor属性控制光标属性

1. auto: 默认
2. default: 默认光标(一般是箭头).
2. all-scroll: 代表十字箭头光标.
3. col-resize: 代表水平拖动线光标.
4. crosshair: 代表十字线光标.
5. more: 移动十字箭头光标.
6. help: 带问号光标.
7. no-drop: 禁止光标.
8. not-allowed: 和no-drop一样.
9. pointer: 代表手型光标.
10. progress: 代表漏沙光标.
11. wait: 和progress一样.
11. row-resize: 代表垂直拖动光标.
12. text: 文本编辑光标.
13. vertical-text: 代表垂直文本编辑光标.
14. *-resize: 该属性\*可为n(上) s(下) e(右) w(左) 方向的光标.\*可为1位或2位,例如nw-resize代表指向上右的光标.

##5.4 media query功能

语法

	@media [not|only] 设备类型 [and 设备特性]*
	
1. all: 所有设备.
2. aural: 适用于语音和音频合成器.
3. braille: 适用于触觉反馈设备.
4. embossed: 使用凸点字符(盲文)印刷设备.	
5. handheld: 适用于小型或手提设备.
6. print: 适用于打印机.
7. projection: 适用于投影图像,如幻灯片.
8. screen: 适用于计算机显示器.
9. tty: 适用于固定间距字符格的设备,如电传打字机和终端.
10. tv: 适用于电视类设备.

设备特性中的值[参考](http://blog.csdn.net/lee_magnum/article/details/12144187)

最常用的设备特新为 min-width和max-width.

一般使用的代码

	@media screen and (min-width: ***px){...}

#6 变形与动画相关属性.

##6.1 CSS3提供的变形支持

1. transform: 设置1个或多个变形函数,设置变形函数顺序和重要,按顺序变形,变形函数有

	1.1 translate[tx[,ty]]: 横向移动tx,纵向移动ty.,ty不写则不移动.
	
	1.2 translateX(tx): 横向移动tx.
	
	1.3 translateY(ty): 纵向移动ty.
	
	1.4 scanle(sx[,sy]): 横向缩放比为sx,纵向缩放比为sy,但sy不填,则缩放比为sx. 
	
	1.5 scanleX(sx): scanle(sx,1).
	
	1.6 scanleY(sy): scanle(1,sy).
	
	1.7 rotate(angle): 顺时针转angle角度.
	
	1.8 skew(sx [,sy]): 沿X轴倾斜sx角度,沿Y轴倾斜sy角度.省略sy,sy为0.
	
	1.9 skewX(sx): skew(sx);
	
	1.10 skewY(sy): skew(0,sy);
	
	1.11 matrix(m11,m12,m21,m22,dx,dy): 基于矩阵变换,前4个参数组成变形矩形,dx、表示对坐标系统进行平移.
	
		//按照矩形变换公司最后变形坐标计算公式,其实变形函数都是通过这个函数实现的.
		(x*m11+y*m21+dx,x*m12+y*m22+dy);
	
2. transform-origin: xCenter yCenter ,设置变形中心点,可选值有左上角 左下角 右下角 右上角

	2.1 xCenter可为left/right
	
	2.2 yCenter可为top/bottom	

##6.2 CSS3提供的Transition

transition属性指定下面4部分,可设置多组动画,用`,`分割.

1. transition-property:指定组件的哪个CSS属性进行平滑渐变,可指定background-color,width,height等标准CSS属性.
2. transition-duration: 渐变时间.
3. transition-timing-function: 渐变速度

	3.1 ease: 先慢后快再慢.
	
	3.2 linear: 速度不变.
	
	3.3 ease-in: 先慢后快
	
	3.4 ease-out: 先快后慢.
	
	3.5 ease-in-out: 和ease类似.
	
	3.6 cubic-bezier(x1,y1,x2,y2): 通过贝济埃曲线控制动画.
	
4. transition-delay: 指定延迟时间.延迟多久后开始渐变

eg:

	div{
		浏览器前缀-transition: background-color 4s linear 4s.
	}	



##6.3 CSS 3提供的Animation动画

Animation动画提供一下几个属性.

1. animation 复合属性,可同时按顺序设置下面的属性.
2. animation-name: 动画名称,指向一个已有的关键帧.
3. animation-duration: 制动动画的持续时间.
4. animation-timing-function: 指定动画的变化速度.
5. animation-delay: 延迟多久开始执行动画.
6. animation-iteration-count: 指定动画循环次数.数值/infinite为无数次,

关键帧定义格式:

	keyframes 关键帧名称 {
		form | to | 百分比 {
			css属性1 : 属性值1;
			...
		}
		...
	}

eg 实现鼠标hover时放大div:

	@-webkit-keyframes '404_K' {
		0% {
		    -webkit-transform: scale(1);
		}
		
		50% {
			-webkit-transform: scale(2);
		}
		
		100% {
		    -webkit-transform: scale(1);
		}
	}
	...
	div>a:hover {
		-webkit-animation-name: '404_K' ;
		-webkit-animation-duration: 3s;
		-webkit-animation-iteration-count: infinite;
	} 


###参考资源

1. 特殊字符编码表<http://www.jb51.net/onlineread/htmlchar.htm>
2. CSS学习资料网站<http://zh.learnlayout.com/inline-block.html>
# HTML5

#0总结

#1. HTML5简介

HTML:Hyper Text Markup Language.(How To Make Love - -.)

标记H5.

	<!DOCTYPE html>
	
作者的愤怒.

	不要在<html>和<head>、</head>和<body>还有</body>和</html>插任何内容.		
验证HTML是否符合规范<http://validator.w3.org/>.

#2. HTML5常用标签和属性.
	
##2.1 HTML常用属性

###2.1.1文本格式化元素
	
1. `<i>:` 斜体.
2. `<em>:` 强调文本.
3. `<strong>|<b>:` 粗体.
4. `<small>:` 小号字体.
5. `<sup>:` 上标
6. `<sub>:` 下标
7. `<bdo>:` 文本方向 设定属性dir为ltr/rtl(反着来)

###2.1.2 语义相关元素

1. `<abbr>:` 表示缩写,可定义title,表示全写,鼠标移上去会显示一次title.
2. `<address>:` 表示地址,会斜体显示.
3. `<blockquote>:` 表示定义引用很长的文本,会缩进显示,属性有cite指定该文应用的url.
4. `<q>:` 定义短的引用文本.
5. `<cite>:` 表示作品(书,电影,歌曲).
6. `<code>:` 表示代码.
7. `<dfn>:` 定义专业术语,一般会粗体/斜体显示.
8. `<del>`: 表示删除的文本,一般会中画线显示. 
9. `<ins>:` 定义文档插入的文本,一般会下画线显示. 

	del和ins结合使用有文档修订的效果,这两个属性都有cite定义一个url解释删除/插入的原因,datetime表示删除/插入的时间.
	
10. `<pre>:` 表示元素内已经预格式化了,该标签内的空格 回车 tab和其他格式的字符都会比保留下来.
11. `<samp>:` 用于定义示范文本内容.
12. `<var>:` 定义变量,一般斜体显示.

###2.1.3 超链接和描点

a标签属性

1. href: 关联资源.

	语法规则`scheme://host.domain:port/path/filename`
	
	scheme(因特网服务类型): 常见有https http file ftp.还有
	
		1. news: 访问新闻组上的文件.
		
		2. telnet: 访问Telnet连接.
		
		3. gopher: 访问远程Gopher服务器上的文件.
		
		4. mailto: 发送邮件
	
	host: 指定此域中的主机,HTTP默认主机为www.
	
2. target: 如何显示关联资源

	2.1 _self: 自身.
	
	2.2 _blank: 新窗口.
	
	2.3 _top: 顶层框架 一般用于含有iframe等标签的时候使用.
	
	2.4 _parent: 父层显示.
	
3. media: 指定媒体类型(HTML5新增).
4. name: 命名描点.`<a name="K">`.

###2.1.4 列表相关元素

1. `<ul>:` 无序列表.
2. `<ol>:` 有序列表.

	2.1 start: 起始数字 1/A等.
	
	2.2 reversed: 反着排序.
	
3. `<li>:` 列表项.
4. `<dl>:` 定义列表.
5. `<dt>:` 标题列表项.
6. `<dd>:` 定义普通列表项.	

###2.1.5 图像相关属性

img新增usemap属性.

1. `<map>:` 定义图片映射,包含多个`<area>`.
2. `<area>:` 定义图片映射区域.特殊属性有

	2.1 shape: 默认为rect 还有circle和ploy属性.
	
	2.2 coords: 多个坐标,确定区域位置.
	
	2.3 href: 链接资源.
	
	2.4 alt: 提供图片信息.
	
	2.5 target.
	
	2.6 media.
	
例子:

	<img usemap="#test" .../>
	
	<map name="test" id="test">
		<area shape="cicle" coords="57,55,25" href...>
		<area shape="poloy" coords="188,28,185,50,200,74,244,72,246,51 ...">
	</map>	
	
###2.1.6 表格相关属性

1. `<caption>:` 定义表格标题,智能包含文本 图片 超链接 文本格式元素 和表单控件.
2. `<colgroup>:` 此标签智能包含在`<table>/<colgroup>`中,用来包含多个`<col>`
3. `<col>`: 用于指定一列或多列的样式.span属性控制控制几列.
4. `<thead>:` 定义表格头.
5. `<tbody>:` 定义表格体智能直接包含`<tr>`.
6. `<tfoot>:` 定义表格腿.
7. `<tr>`: 一行
8. `<th>:` 定义页眉单元格.
9. `<td>:`单元格,属性有:

	9.1 colspan: 单元格夸几列
	
	9.2 rowspan: 单元格夸几行 	
	
例子

	<!-- 书名列宽为160px 作者和价格列宽度为100px -->
	<table>
		<caption>标题</caption>
		<colgroup>
			<col style="width :160px">
			<col span="2" style="width:100px">
		</colgroup>	
		<thead>
			<tr>
				<th>书名</th>
				<th>作者</th>
				<th>价格</th>
			</tr>
		</thead>
		<tr>
			<td>程序员把妹<td/>
			<td>K<td/>
			<td>1024<td/>
		</tr>
		...
		<tfoot>
			<tr>
				<td>现总计: 2本图书</td>
			</tr>
		</tfoot>
		
###2.1.7 HTML5新增通用属性

1. contentEditable属性: 此值为true,组件可随意编辑.可继承.
2. isContetnEditable属性: 若处于可编辑状态,返回true.
3. designMode: 默认为off 设为on时,真个页面都可编辑(和在body设置contentEditable为true类似).
4. spellcheck: 当true时,用户输入的内容会进行单词拼接检查.
5. hidden: true/false,其实不建议用,样式还是给CSS控制吧.

##2.2 HTML5 新增常用元素 

###2.2.1 文档结构元素

此类元素其实只负责分块,其实无实际作用.只是友好提示程序员这是一块内容.

1. `<article>:` 用于展示一个整体内容,一篇文章 一条回复等.
2. `<nav>:` 导航栏(边导航,页面导,底部导)标签.
3. `<header>:` 页面顶部.
4. `<hgroup>:` 定义多个标题时	
5. `<section>:` 内容分块.可设置cite属性.
6. `<aside>:` 专门用于定义当前页面的附属信息,一般用作侧边栏.一般放在`<footer>`之前.
7. `<figure>:` 用于一块图片区域,可包含一个`<figcaption>`
8. `<figcaption>:` 作为图片区域的标题. 
9. `<footer>`: 底部.

###2.2.2 语义相关元素

1. `<mark>:` 重点关注内容
2. `<time>:` 指定时间,里面应该包含一个`datetime`属性指定符合`yyyy-MM-ddTHH:mm`格式的时间
3. `<details>:`	 用于展示某个主题的时间,可包含`<summary>`使用.
4. `<summary>:` 作为details的摘要元素.

###2.2.3 特殊功能的元素

1. `<meter>:`用于表示一个已知最大值和最小值的计数仪表,属性皆可浮点

	1.1 value: 当前值.
	
	1.2 min: 最小值.
	
	1.3 max: 最大值.
	
	1.4 low: 指定范围的最小值.
	
	1.5 high: 指定范围的最小值.
	
	1.6 optimum: 指定有效范围的最佳值,若大于high,表示当前值越大越好,若小于low,则表示当前值越少越好.
	
2. `<progress>:` 用于表示一个进度条.

	2.1 max: 进度条完成时的值.
	
	2.2 value: 当前值.
	
##2.3 HTML5 头部和元数据

1. `<base>:` 相对地址取的是此base的href,而非url.绝好放在head的头元素.

	1.1 href: 指定相对地址
	
	1.2 target: 默认打开链接方式
	
2. `<meta>:` 指定页面元信息.

	2.1 name: 可为:
	
		2.1.1 author: 作者		
		2.1.2 description: 简述 
		2.1.3 keywords: 关键词 
		2.1.4 generator: 说明生成工具,如`Microsoft FrontPage 4.0`等
			<meta name=”generator” Content=”PCDATA|FrontPage|”>
			
		2.1.5 revised: 定义网页版本
			<meta name=”revised” content=”David, 2008/8/8/” />
			
		2.1.6 robots: 给机器人(搜索引擎)做登陆参考,可同时设多个属性逗号分隔
			
			2.1.6.1 all：默认,文件将被检索，且页面上的链接可以被查询(和 "index,follow"作用一样) 
			2.1.6.2 none：文件将不被检索，且页面上的链接不可以被查询(和 “noindex, no follow” 起相同作用)
			2.1.6.3 index：文件将被检索；（让robot/spider登录）
			2.1.6.4 follow：页面上的链接可以被查询；
			2.1.6.5 noindex：文件将不被检索
			2.1.6.6 nofollow：页面上的链接不可以被查询
									
		2.1.7 others: - -....	
		2.1.8 viewport: 优化移动浏览器显示,。如果不是响应式网站，不要使用initial-scale或者禁用缩放。大部分4.7-5寸设备的viewport宽设为360px；5.5寸设备设为400px；iphone6设为375px；ipone6 plus设为414px。
		
			<meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1.0, user-scalable=no"/>
			<!-- `width=device-width` 会导致 iPhone 5 添加到主屏后以 WebApp 全屏模式打开页面时出现黑边  -->
			
			width：宽度（数值 / device-width）（范围从200 到10,000，默认为980 像素）
			height：高度（数值 / device-height）（范围从223 到10,000）
			initial-scale：初始的缩放比例 （范围从>0 到10）
			minimum-scale：允许用户缩放到的最小比例
			maximum-scale：允许用户缩放到的最大比例
			user-scalable：用户是否可以手动缩 (no,yes)
			minimal-ui：可以在页面加载时最小化上下状态栏。（已弃用）
		
	2.2 http-equiv 属性主要有	
	
		2.2.1 expries: 指定网页过期时间,过期必须冲浏览器重新下载
		2.2.2 pragma: 值可为no-cache,不适用缓存
		2.2.3 refresh: 指定多长时间自动刷新页面
		2.2.3 set-cookie: 设定Cookie
		2.2.4 content-type: 指定网页内容类型和字符集.
		
			<meta http-equiv="Content-Type" content="text/html charset=utf-8">
			
	2.3 scheme 指定用来翻译属性值的方案			
		
`<meta>`参考链接<http://segmentfault.com/a/1190000002407912>非常详细!、<http://www.hujuntao.com/web/html/html-meta-tag.html>	

##2.4 HTML5 新增拖放API

###2.4.1 拖放事件

不知道你有没有发现...网页的img和a(设置了href)的标签都是可以拖动的.对于其他标签,只要设定`draggable="true"`即可拖放.标签中含有相关的触发事件

	1. ondragstart: 开始拖动触发
	2. ondrag: 拖动过程不断触发
	3. ondrageend: 拖动结束触发
	4. ondragenter: 当拖动的元素进入本元素后触发的时间
	5. ondragover: 当拖动的元素在本元素范围内将不断触发
	6. ondragleave: 拖动的元素离开本元素时触发
	7. ondrop: 其他元素放到本元素时触发该时间
	
	//实现拖动一个div随意放
	
	//取消默认事件 每个浏览器不一样.
	document.ondragover =function(evt){
		//取消默认事件
		evt.preventDefault();
	}
	
	document.ondrop = function(evt){
		//取消默认事件
		evt.preventDefault();
		source.style.position = "absolute";
		source.style.left = evt.pageX+"px";
		source.style.top = evt.pageY +"px";
		
		
	}
	
###2.4.2 DataTransfer对象

拖放事件中的参数中有一个dataTransfer属性.该属性的方法有

1. dropEffect: 设置或返回拖放目标上允许的拖放行为,属性值有`none(拖动元素不能放到本元素中)`,`copy`,`link`,`move`之一.且行为需要是effectAllowed中允许的行为
2. effectAllowed: 设置或返回被拖动元素允许发生的拖动行为,属性有`none(不允许拖动本元素)`,`copy`,`link`,`move`,`copyLink`,还有`copyMove`,`linkMove`,`all`和`uninitalized`
3. items: 返回DataTransferItems对象,该对象代表了拖放数据.
4. setDragImage(element,x,y): element代表指定的元素,x表示图标与鼠标的水平距离,y代表纵向距离.

		//eg
		var K_Icon =document.createElement("img");
		K_Icon.src ="K.gif";
		
		被拖动元素.ondragestart =function(evt) {
			evt.dataTransfer.setDragImage(K_Icon,0,0);
		}
		
5. addElement(element): 添加自定义图标.
6. types: 该属性返回一个DOMStringList对象,包含了dataTransfet中所有数据类型
7. getData(format): 获取DataTransfer对象中的format格式的数据.
8. setData(format,data): 设置DataTransfer对象中格式为format的data数据.
9. clearData([format]): 清楚指定format格式数据,省略则全部删除.		
一般监听ondragstart来把数据放入DataTransfer中,监听ondrop取数据.

下面代码代表页面元素拖入收藏夹的部分功能实现.

	<div draggable="true" ondragstart="K(event)";>...
	<div id="Mark_Div" draggable="true" ondrop="mark(evt)" ;>...
	
	var K = function(evt) {
		evt.dataTransfer.setData("text/plain","<item>"+evt.target.innerHTML);
	}
	
	var mark = function(evt){
		var text =evt.dataTransfer.getData("text/plain");
		//如果以<item>开头
		if (text.indexOf("<item>") == 0 ){
			var New_Div = document.createElement("div");
			New_Div.id =new Data().getUTCMilliseconds();
			New_Div.innerHTML = text.substring(6);
			document.getElementById("Mark_Div").appendChild(New_Div);
		}
	}
	
#3 HTML5表单

这里列举部分form表单属性

1. enctype: 指定表单内容进行编码时候的字符集.

	1.1 application/x-www-form-urlencoded: 默认
	
	1.2 multipart/form-data: 二进制流 一般上传文件时使用
	
	1.3 text/plain 当表单的action为`mailto:URL` 发送邮件时,采用这种方式可能比较方便
	 
2. target: 表单也可以设置target.

表单控件设置了disabled时,不提交此参数.

表单控件有tabIndex属性,值为1,2,3...,按Tab按此顺序跳动.
	
##3.1 form表单的一些标签

1. `<label...>`: 和控件关联.

	1.1 隐式调用: 使用for属性关联控件的ID
	
	1.2 显式调用: 用`<label>框住表单控件</label>`	
	
2. `<select>`: 下拉列表/列表框

	2.1 multiple: 指定可多选.
	
	2.2 size: 列表框可以显示几条数据.
	
	2.3	`<optgroup>:` 指定选项组, 必须定义lable属性.只能包含`<option>`
	

##3.2 HTML5新增的表单控件玩法

1. form: 该属性指定为form表单的id,及时该控件不在form表单内,也会随form提交.
2. formaction: 一个表单若要两个提交按钮要提交到不同的地方,在提交控件中设置不同的formaction即可.
3. formxxxx属性: 和formaction类似,为提交按钮设置不同的`formenctype`,`formmethod`,`formtarget`等.
4. autofocus: 当打开一个页面时,这个控件自动获取焦点.
5. placeholder: 提示功能,在文本框内显示提示信息,开始输入时消失.
6. list: list指定`<datalist>`标签中的ID,`<datalist>`的使用方法和select完全一样,当文本控件获取焦点时,这个`<datalist>`定义的下拉列表显示在文本框下.
7. autocomplete: 与list结合使用,当此属性为on,list属性起作用,为off,list属性失效.然而这并没有什么卵用.	

###3.2.1 input标签的type的新选项值

1. color: 生成一个颜色选择器.
2. date: 生成日期选择器.
3. time: 生成一个时间选择器.
4. datetime: 让生成一个UTC日期、时间选择器.
5. datetime-local: 生成一个当地的日期、时间选择器.
6. week: 生成一个第几周选择器.
7. month: 生成一个月份选择器.
8. email: 生成一个校验邮件的控件,当type="email"时,可指定multiple属性,表示允许输入多个email.
9. tel: 输入电话的文本框,并不怎么校验.
10. url: 校验url的文本框.
11. number: 只让输入数字的文本框.
12. range: 进度条,当type=range时候,可值得属性有`min`,`max`,`step(指定拖动条步长)`.
13. search: 输入搜索关键字,其实也并没什么卵用.

###3.2.2 HTML5新增表单控件

1. `<output>`: 输出变量,但不会随表单提交.


###3.2.3 HTML5增强文件上传

当`type="file"`时,可指定一下属性

1. accept: 指定一个/多个允许的文件上传MIME类型,多个类型用`,`分割.例如`accept="image/*"`指定智能上传图片.[更多格式参考](http://www.w3school.com.cn/media/media_mimeref.asp)
2. multiple: 允许用户提交多个文件.

JS也可以访问上传文件夹的具体属性.

1. name: 返回File对象的文件名.
2. type: 文本的类型.
3. size: 文件的大小.

####3.2.3.1 新增的FileReader对象读取文件内容

FileReader对象提供以下方法.

1. readAsText(file,encoding): 以文本格式读取文件.根据ID选择到file,encoding字符编码默认utf-8.
2. readAsBinaryString(file): 二进制读取文件,通过这种方式可以用Ajax把数据上传到服务器.
3. readAsDataUrl(file): 以DataURL方式读取文件,这种方式也可以读取二进制,但是这种方式会采用base64方式把文件内容编码成DataURL格式字符串.
4. abort(): 停止读取.
5. loaded属性: 当前FileReader已读取文件的大小.
5. result属性: 当FileReader读取完文件后,result包含读取文件的内容.

但read*的所有方法都是异步的,进行时会不断触发函数.

1. onloadstart: FileReader开始读取数据触发.
2. onprogress: FileReader读取数据时不断触发.
3. onload: FileReader成功读取后触发.
4. onloadend: FileReader读取完成触发,无论成功还是失败都会触发.
5. onerror: FileReader读取失败触发.

例子:浏览器读取文件进度条,注意该方法只不过是浏览器读取文件的进度,而不是上传到服务器的进度.


	<input id="file_1" type="file" />
	<progress id="pro" value="0"></progress>
	...
	var Read_Binary =function(){
		var reader = new FileReader();
		var pro =document.getLementById("pro");
		pro.max = document.getElementById("file_1").files[0].size;
		reader.onprogress =function (evt){
			pro.value =evt.loaded;
		}
	}

###3.2.4 HTML5 新增客户端校验.

表单控件可增加以下属性完成校验

1. required: 指定控件必须填写.
2. pattern: 指定符合正则表达式.
3. min、max、step: 只对数值类型,日期类型或者`<input ../>`起作用.,指定数值必须在min~max,并符合step步长.

在JS中form对象多了checkValidity()方法,当form表单所有HTML5客户端验证通过返回true.

除此之外,所有表单和表单控件都有validity(ValidityState对象)属性,用于判断表单内所以控件/单个表单验证的情况.返回true/false.

所有表单/表单控件都有setCustomValidity("错误提示")方法,当校验没通过时,自定义的错误提示文字.

关闭表单校验.

1. 当form表单属性包含novalidate属性,不进行校验.
2. 当提交按钮设置了 formnovalidate属性,不进行校验.

#4 HTML5 绘图支持

`<canvas>`提供了一个画布.JS进行绘图,绘图步骤

1. 获取`<canvas>`DOM对象.
2. 调用Canvas对象的getContext()方法,返回一个CanvasRenderingContext2D对象.

		var ctx = Canvas对象.getContext('2d');
		
3. 调用CanvasRenderingContext2D对象的方法进行绘图.

这部分API非常多,用法这里不主要阐述,后面有时间会单独总结.总的来说,当你的网页功能有绘图的话,这会非常重要.

这里只抛砖引玉一下..会在网页内出现颜色为#f00的长方形.

	<canvas id="K" style="width:300px ; height:180px">
	
	...
	
	var K_Canvas = document.getElementById("K");
	var K_CTX = K_Canvas.getContext("2d");
	//设置填充颜色
	K_CTX.fillStyle= "#f00";
	//绘制图形
	K_CTX.fillRect(30,40,80,100);

#5 HTML5 多媒体支持

简单使用

	<!-- 音频 -->
	<audio src="demo.ogg" controls>
		你的浏览器不支持audio
	</audio>
	
	<!--视频-->
	<video src="movie.webm" controls>
		你的浏览器不支持video元素
	</video>

audio推荐使用OGG Vovis格式

video推荐使用VP8格式.

##5.1 audio和video支持的属性

1. src: 地址.
2. autoplay: 指定了属性装载完成后是否自动播放
3. controls: 指定了属性就有控制条.
4. loop: 指定了该属性循环播放.
5. preload 是否预加载,指定了autoplay,此属性无作用.

	5.1 auto: 预加载音频 视频
	
	5.2 metadata: 只预加载音频 视频元数据.既媒体字节数,第一帧,播放列表,持续时间.
	
	5.3 none 不预加载. 
	
6. poster: 只对video有效,指定一个图片url,开始播放前,显示这张图片.

##5.2 audio和video可包含标签

1. `<source ../>` 浏览器会根据自己情况选择格式.

	1.1 src: 地址
	
	1.2 type: 播放格式.


##5.3 JS控制媒体播放

DOM获得对象分别是`HTMLAudioElement`或`HTMLVideoElement`,支持的方法有.

1. play(): 播放.
2. pause(): 暂停
3. load(): 重新装载.
4. canPlayType(type): 判断能否播放type格式的资源,type可指定MIME字符串/codecs属性,多个格式用`,`分割,返回值有

	4.1 probably: 该浏览器支持type格式.
	
	4.2 maybe: 可能支持- -..
	
	4.3 空字符串: 不支持.
	
在这里写一个播放多个音乐的JS函数

	var musics =[
		"K.ogg",
		"404_K.ogg",
	];
	var index = 0;
	window.onload =function(){
		var player = Audio的DOM对象.
		palyer.src=musics[index];
		//onended在资源播放完后后触发.
		player.onended = function(){
			player.src =musics[++indx % musics.length];
			player.paly();
		}
		
	}		

`HTMLAudioElement`或`HTMLVideoElement`包含大量属性和事件监听.这里就不一一列举了.

# 疯狂JavaScript

#0 总结

本书的JS

1. 第一章有讲语法有挺多常见的坑点和原理解释很不错
2. 第二章DOM编程讲述了挺多API
3. 第三章事件处理机制其实对事件中的this关键字和事件传播顺序讲解还不错
4. 第四章WebStorage本地存储例子鲜明
5. 第五章Worker应付复杂的js操作
6. 第六章客户端通信WebSocket挺有用,可以实现用户与用户在浏览器中互动

#1. JavaScript语法

##1.1 执行js代码

1. javascript:alert('执行js');//一般放在超链接中,用户点击即执行,
2. `<script>alert("执行js")</script>`

##1.2 变量赋值

	var a = 1;//显式
	a =1; //隐式
	
##1.3 全局变量与局部变量

	...
	var scope = "全局变量";
	function test(){
		alert(scope); // undefiend
		var scope = "局部变量";
		alert(scope); // 局部变量
	}

因为全局变量被局部变量覆盖了.虽然局部变量的scope还没赋值,但是已经在方法里"占"上位置了.

但如果把局部变量的var删了,就会先输出全局变量后输出局部变量,因为没有var在方法里给局部变量"占"位置;

##1.4 浮点数

	var a =.333
	var b = a * 5;
	alert(b);
	
得出的结果是 1.66499999999999999

所以在js中判断浮点数是否相等 建议判断两者的差值是否小于一个足够的数(例如0.0000000000001)

##1.5 字符串

js中没有字符类型变量 ""与''一致

	var s ="abcdefg"
	
	//b = "def"
	b = s.slice(3, -1);
	
##1.6 字符串的正则表达式方法中

1. match()返回匹配的字符串(数组或null),可加/g进行全局匹配多个
2. search()返回匹配的索引值 单个

##1.7 undefined和null

	null == undefined //true
	
	null === undefined //false
	
undefined 是没设值

null则是设定了为null值 

##1.8 运算符
	
	//逗号运算符 取最右返回值
	a = (b =5 , c = 7 , d =56) //a =56
	
	a = void(b =5 , c = 7 , d =56) //a = undefined
	
##1.9 typeof和instanceof

typeof 用来获得 实例类型 :

	typeof("123"); //string

instanceof 判断变量是否为某类的实例

	var a = [4,5];
	alert(a instanceof Array); //true
	
##1.10 语句

抛出异常

	throw new Error("用户自定义异常"); //一般用于终止程序和返回错误提示是个不错的选择;
	
	try{
	
	}catch(e){
		alert(e.message); // "用户自定义异常"
	}

for in 
	
	//这回输出浏览器的所有属性,做浏览器兼容之前可以考虑看看.
	for( prop_name in navigator){
		document.wrti(prop_name + " : " + navigator[propname]);
	}	
	
跳出命名for

	outer: 
	for(...){
		for(...){
			...
			continue outer;
		}
	}
	
##1.11 函数

js 允许先调用函数 再定义函数

###1.11.1 定义匿名函数

	var show_name = function(name){
		alert(name);
	}
	
	show_name("K"); //K
	
这样的好处是什么,如果直接定义function 它实际上也是创建了一个对象

###1.11.2 函数既对象

	var hello = function(){...};
	
	hello instanceof Function //true;
	
	hello instanceof Object //true;
	
	alert(heelo) //输出函数源代码
	
###1.11.3 调用函数方式的不同

1. 直接调用函数 返回return的值或void
2. new 函数 得到的都是对象 - -.......

###1.11.4 this关键字.

在函数中使用this.变量 该变量就是函数的实例变量,而非局部变量,无论它在哪里.

函数可依附在类中.如没指定 则依附在winodw对象中

	var hello =function(){...}
	
	window.hello();
	
	var p = {
		wark: function(){...}
	}
	p.wark();
	
###1.11.5 函数中的变量有三种

	function Person(){
		//局部变量 只能在函数里访问
		var id ;
		
		//实例属性 通过对象.访问
		this.age ;
		
		//类属性 通过Patient.name访问 与static类似
		Person.name ;
	}

###1.11.6 js是一种动态语言,能随时给对象增加属性和方法	

	function Student(){ };
	
	var student =new Student();
	//动态增加name属性
	student.name = 'K';
	alert(sutdent.name) //K
	
	Student.age =22 ;
	alert(Student.age); //22 类属性也是可以动态添加的

###1.11.7 调用函数的三种方式

1. 直接调用

		windows.alert();
		//or
		alert();
		
2. call()调用

	作用:动态地传入一个函数引用
	
		var each = function(array,fn){
			for(var index in arrary){
				//null表示以window为调用者fn函数
				fn.call(null,index,arrary[index]);
			}
		}
		
		each([4,20,3] , function(index ,ele){
			alert("第 " + index "个元素是 : " + ele);
		});
		
	call()调用函数语法为:`函数引用.call(调用者,参数1,参数2...)` 
	
	直接调用函数语法为:`调用者.函数(参数1,参数2 ...)` = `函数.call(调用者,参数1,参数2 ...)`
	
3. apply()调用

	apply和()call()基本相似,区别如下:
	
	1. 通过call()调用函数时,括号必须详细列出每个参数
	2. 通过apply()动态地调用函数时,可以在括号中以`arguments`来代表所有参数
	
			var myfun = function (a , b){
				alert(a + "  " +b);
			}
			
			myfun.call(window ,12 ,23); //12 23;
			
			myfun.apply(window ,[20 , 39]); //20 39
			
			var example = function (num1 ,num2){
				//直接调用arguments代表调用者(example,this代表example)时的传入的所有参数
				myfun.apply(this,arguments); 
			}
			example(20,40) //20 40 
			
###1.11.8 函数的独立性

在函数A中可以定义函数B,但是函数B还是独立于函数A

	function Person(name){
		this.name = name;
		
		this.info = function(){
			alert(this.name);
		}
	}	
	
	var person =new Person('K');
	person.info(); //K
	var name = 'K_window';
	
	//由于window为调用者 ,this.name访问的是window.name
	p.info.call(window); //K_window
	
来爽一发猫学狗叫?.

	function Dog(name,bark){
		this.name = name;
		this.bark = bark;
		this.info =function(){
			alert(this.name + "  " + this.bark);
		}
	}
	
	function Cat(name){
		this.name =name;
	}	
	
	var dog = new Dog("K","汪汪!");
	var cat = new Cat("K_2");
	dog.info.call(cat); //K_2 undefined
	
###1.11.9 参数传递方式

和JAVA一样 都是值传递拉~.	

基本类型

	function change(arg){
		arg =10 ;
		alert(arg);
	}
	var x = 5;
	alert(x); //5
	change(x); //10
	alert(x); //5
	
复合类型

	function change(person){
		person.age = 10;
		alert(person.age);
		person = null;
	}
	
	var person = {age : 5};
	alert(person.age); //5
	change(person); //10
	alert(person.age); // 10
	alert(person); // []object Object]
	
复合类型的传递为值传递,原person和参数person指向同一javascript对象,所以当改变age的时候,是在改变javascript对象的age,但是参数person赋值为null,原person并无改变.

###1.11.10 空参数

	function text(person){
		alert( typeof parson);
	}
	
	text(); //undefined
	
所以对于弱类型,方法重载是无作用的,因为js会把空参数当做undefined传递进去;

同名函数,后面出现的会覆盖前面的,无论参数个数是多少.

###1.11.11 对象和关联数组

javascript和Map有点类似,当key为对象,value为函数,该该函数就是对象的方法,当key为对象,value为基本类型,则该基本类型为对象的属性.(以上为便于理解,切勿细琢)所以访问属性时,可以obj.propName也可以obj[propName].

但有时候我们只能用obj[propName],因为.propName不能把propName当做变量处理,而是把他当成'propName'字符串

	function Person(name){
		this.name =name;
		this.info = function(){
			alert(K);
		}
	}
	
	var person = new Person("K");
	//遍历person属性
	for (propName in person){
		alert(p[propName]);//alet K info源代码 假如此处用p.propName则undefined,因为Person无'propName'变量.
	}

###1.11.12 继承和prototype

在一个类(函数)中定义一个函数会导致

1. 性能低下:每次new一个类 都会生成一个函数
2. 函数中若引用类的局部变量会产生闭包 导致局部变量一直存在

		function Person(){
			var local = "局部变量"
			this.info = function(){
				//产生闭包
				alert(local);
			}
		}
		
		var person = new Person();
		person.ifno(); // 局部变量	
		
解决方案:prototype

增加了prototype属性的类可视为继承了原先的类(伪继承)

	function Person(){...}
	
	var person = new Person();
	
	//person.wark(); 程序报错wark不存在
	Person.prototype.wark = function(){...}
	
	person.wark(); //ok
在prototype之前实例化的类会具有wark方法吗? 有的,因为prototype这样并不会产生一个新的类,而是直接动态的往Person里加函数.

判断某方法是否继承了该对象

	该对象.prototype['某方法'] != undefined;
	
	var test = new 该对象();
	test.某方法 !=undefined ;
	

##1.12 创建对象三种方式

	//(单身汪别说我不教你)
	
1. new关键字调用构造器创建对象
	
		function Person(name){...}
		var person_1 = new Person();
		var person_2 = new Person('K'); //js不存在方法重载,空参数undefined顶替
		
2. 使用Object直接创建对象

		var my_obj = new Object();
		my_ojb.name = 'K';
		my_obj.handsome = function(){...}
		
		function text(){...}
		my_obj.text = text;//不要添加(),不然会认为是调用方法

3. JSON创建对象

		ver person = {
			name : 'K',
			school : ['ChangAn','TianJin'],
			girl_friends :[
				{
					name : 'Haski',
					age : 11
				},
				{
					name : 'Samoyed',
					age : '8'
				}
			]
		}
		
		alert(person.girl_friends[0].name); //Haski

#2 DOM编程

DOM操作其实JQuery已经做得很好了,这里简单补充一下原生JS的知识

HTML文档中只有一个根节点

##2.1 访问HTML元素

1. id getElementById('id'); or getElementsByName('name');
2. 根据节点关系

		Node parentNode: 返回父节点
		Node previousSibling: 返回前一个兄弟节点
		Node nextSibling: 饭后后一个兄弟节点
		Node[] childNodes 返回当前节点的所有节点
		Node[] getElementsByTagName('标签名称'): 返回当前节点具有制定标签的子节点
		//注意ol标签的子标签IE8和其他浏览器不一样(其他浏览器会把ol下的li和其后面的空白分别当成2个节点,IE8则不会)
		
##2.2 增加HTML函数

1. document.createElement("标签名");
2. 复制节点 var node = ul.firstChild.nextSibling.cloneNode(boolean),boolean为true时,复制所有所有后代节点.false则仅复制当前节点.clone了节点以后还要找一个节点添加进去.

##2.3 添加节点

1. appendChild(Node);添加为当前节点的最后一个子节点
2. inserBefore(newNode,refNode);在refNode前添加newNode
3. replaceChild(newChild,oldChild);替换节点
4. 增加select选项 new Option(text,value,defaultSelected,selected);

##2.4 删除节点

1. removeChild(oldNode);

##2.5 window对象

1. 返回上一个页面: back()
2. window.href: 当前url
3. window.width: 屏幕横向分辨率
4. window.height: 屏幕纵向分辨率
5. 遍历window.screen,包含所以屏幕属性

		for(var propName in window.screen){
			alert(propName+":" +screent[propname]);
		}

6. cofrim('标题'); 能弹出是否确认的提示框
7. prompt('标题'); 能弹出一个文本输入框输入.
8. 定时器:setInterVal,clearInterval()
		
		var timer;
		var cur = new Date().getTime();
		var setTime = function(){
			document.getElementById("tm").innerHTML = new Date().toLocationString();
			if(new Date().getTime- cur > 60 *1000){
				clearInterval(timer);
			}
		}
		//每1S执行一次,执行了60次就暂停
		timer = window.setInterval("setTime()",1000);
		
##2.6 navigator和地理位置

navigator汉堡浏览器所有信息,遍历循环获取信息

	for(var propName in window.navigator){
		alert(propName + ":" + window.navigator[propName]);
	}	
	
HTML5新增geolocation属性

Geolocation提供的方法

1. getCurrentPosition(onSuccess,onError,options)
2. int watchCurrentPostion(OnSuccess,onError,options),周期性调用getCurrentPosition,返回的int代表这个"监听器"的ID,用来clearWatch(watchID)取消监听
3. clearWatch(watchID),用于取消watchCurrentPosition

上面的前两个方法的options参数是一个对象,可包含3个变量

1. enabelHighAccuracy(是否制定高精度地理位置)
2. tiemout 设置超时时长
3. maximumAge,设置缓存时间

例子:

	var geoHandler = function(position){
		var geoMsg = "用户地址位置是 : <br/>"
		geoMsg += "timestamp属性为 :" + position.timestamp + "<br/>"//获取位置的时间
		var cords =position.coords;
		for(var prop in coords ){
			geoMsg += prop + ": " + coords[prop] +"<br/>"//经纬度,移动速度等
		}
		document.writeln(geoMsg);
	}
	
	var errorHandler = function(error){
		var errMsg = {
			1: '用户拒绝了位置服务'
			2: '无法获取地址位置信息'
			3: '获取地理位置信息超时'
		};
		alert(error[error.code]);
	}
	
	navigator.geolocation.getCurrentPosition(geoHandler
	, errorHandler
	, {
		enableHighAccuracy:true,
		maximuAge:1000
	});
	
##2.7 HTML5新增浏览器分析

实现该功能主要通过performance对象

其中的(PerformanceTiming)timing属性包含加载时间相关的属性

另外(PerformanceNavigation)navigation,主要属性有

type :

	TYPE_NAVIGATE(数值为0): 超链接/输入url
	TYPE_RELOAD(1): 重新加载方式,diaoyonglocation.reload()等
	TYPE_BACK_FORWARD(2): 通过浏览器的前进方式
	TYPE_RESERVED(255): 未知方式
	
redirectCount: 重定向次数

#3 事件处理机制

##3.1 常见事件

1. onabort: 图片加载终端
2. onblur: 失去焦点
3. onchange: 表单域更改
4. onclick: 点击
5. onerror: 图片加载出错
6. onfocus: 获得焦点
7. onkeydown: 按下鼠标
8. onkeypress: 当焦点在当前元素上,单击键盘某个键触发
9. onkeyup: 当焦点在当前元素上,松开某个键触发
10. onload: 某个对象加载完毕,适用于`img`,`oframe`,`body`
11. onunload: 当某个对象从窗口下卸载触发,适用于`img`,`oframe`,`body`
12. onmousedown: 焦点停留在当前元素上,按下鼠标触发
13. onmousemore: 当焦点在当前元素上,鼠标移动到该元素
14. onmouseout: 鼠标移出当前元素触发
15. onmouseover: 鼠标移动到该元素触发
16. onmouseup: 焦点在当前元素,松开鼠标时触发
17. onreset: 重置表单时触发
18. onsubmit: 表单提交触发

##3.2 事件处理和this

	p.info = function(){
		alert(this.name);
	}
	document.getElementById("bt").onclick = p.info//this指向'bt'控件
	document.getElementById("bt").onclick = new function (){ p.info();} //this总是指向p
	
注意表单设置id为x和name为y时候,相当于表单创建了x属性和y属性,所以id和name不能是关键字submit等

##3.3 DOM

创建监听事件

objectTarget.addEventListener("eventType",handler,capture),第一个参数表示绑定的事件,如click、keypress之类的,第二个制定绑定的函数,第3个位boolean,true表示监听捕获阶段,false表示监听冒泡阶段

objectTarget.removeEventListener("eventType",handler,captureFlag): 删除绑定事件

捕获状态阶段的绑定事件先执行,事件冒泡状态阶段的绑定事件后执行.
捕获状态阶段从外往内触发,事件冒泡状态阶段从内往外触发.

绑定例子

	var got_click = function (event){
		for ( event_one in event){
			alert(event_one + "  : " + event[event_one]);
		}
	}	
	
	document.getElementByID("test").addEventListener("cilck",got_click,true);
	
阻止事件传播
	
	event.stopPropagation();
	
取消事件的默认行为,如跳转页面等,但不会阻止事件传播.

	event.preventDefault();
	
###3.3.1转发事件

DOM提供了dispathEvent方法用于事件转发,该方法属于Node

target.dispathEvent(Event event),将event转发到target上

DOM的dispath()必须转发人工合成的Event事件

document.createEvent(String type),tpy参数用于指定事件类型,eg:普通事件Events,UI事件UIEvents,鼠标事件:MouseEvents

初始化事件

	initEvent(具体参数...)
	
	initUIEvent(具体参数...)
	
	intMouseEvent(具体参数...)
	
	//例子
	
	<input id="bt1">
	<input id="bt2">
	...
	
	var rd =function(evt){
		alert("事件冒泡阶段: " + evt.currentTarget.value +"被点击了");
		var e =document.createEvent("Evnets");
		e.initEvent("click",true,false);//true表示是否支持冒泡,false表示是否有默认行为
		document.getElementById("bn2").dispathEvent(e);
	}
	var go_click = function (evt){
		alert("事件冒泡阶段: " + evt.currentTarget.value);
	}
	   
	document.getElementById("bn1").addEventListener("click",rd,false);
	
	document.getElementById("bn2").addEventListener("click",go_click,false);;
	
	//点解按钮一结果
	alert(事件冒泡阶段: 按钮一被点击了);
	alert(事件冒泡阶段:按钮2);
	
点击按钮1,按钮执行了前面按钮一被点击了提示语句后,将点击事件转给了按钮2,按钮2执行自身的点击事件.

#4 本地存储与离线应用

##4.1 Web Storage

使用理由之一Cookie的局限性:

1. Cookie大小被限制为4KB
2. Cookie会包含在每次HTTP请求中
3. Cookie网络传输未加密(除非整个应用都使用SSL)

Web Storage分两种

Session Storage: 生命周期与用户Session一致(用户Session是指:用户从访问网址到离开网址/关闭浏览器)

Local Storage: 保存在用户的磁盘中,失效的方式为用户/程序显示删除.

Web Storage的方法有

1. length: 返回key-value对数
2. key(index): 返回第index个key
3. getItem(key): 获取key对应的value
4. set(key,value): 设置key-value
5. removeItem(key): 删除key-value
6. clear(): 清除所有key-value

Web Storage包含在window对象中

当value为对象时,建议用JSON存储 

##4.2 构建离线应用

1. 在html标签中修改
		
		//表明该页使用index.manifest文件
		<html manifest="index.manifest">
		
2. index.mainfest文件

		CACHE MANIFEST
		//第一行必须为上述字符
		//指定版本号
		#version 1
		//本地缓存资源
		CACHE
		inedx.html
		logo.jpg
		//不缓存的资源
		NETWORK
		*
		//前者表示在线状态使用的资源,后者代表离线状态使用的资源
		FALLBACK
		test.js offline.js
		
		
3. Tomcat为例,天津映射文件

		<!--conf的web.xml根元素中增加MIME映射-->
		<mine-mapping>
			<extension>manifest</extension>
			<mine-type>text/cache-mainfest</mime-type>
		</mime-mapping>
		
启动应用后,页面可刷新(即使离线状态),并使用离线时候的资源

###4.2.1 判断在线状态

navigator.onLine属性: true表示在线

online/offline事件: 当在线/离线状态切换时,body上的online/offine事件会被触发,沿着document.body、document和window冒泡

	window.addEventListener("offline",function(){
		alert("离线状态")
	},true);
	if(navigator.onLine){
		alert("在线");
	}
	
###4.2.2 applicationCache对象

js可通过applicationCache控制离线缓存.

status属性: 

1. UNCACHE: 主机没开启离线功能.
2. IDLE: 空闲状态.
3. CHECKING: 正在检查本地manifest和服务器中manifest的差异
4. DOWNLOADING: 正在下载需要的缓存数据 
5. OBSOLETE: 缓存已经过期

常用方法

1. void update(): 强制检查服务器的mainfest文件是否有更新
2. void swapCache(): 更新缓存,只能在applicationCache的updateReady事件被触发时调用.

		setInterval(function(){
			applicationCache.update()
		},2000);
		
		applicationCache.onupdateready = function(){
			if(confirm("已从远程服务器下载了需要的缓存,是否更新?")){
				applicationCache.swapCache();
				location.reload();
			}
		}
###4.2.3 离线应用的事件与监听

访问html页面过程

1. 浏览器请求index.html
2. 服务器返回index.html
3. 浏览器页面是否制定manifest属性,若制定,触发checking事件,检查服务器中的manifest文件是否存在,不存在则触发error事件,不会制定第六部及其后续步骤
4. 浏览器解析index.html,请求该页其他资源.
5. 服务器返回所以请求
6. 浏览器处理mainfest文件,重新请求manifest文件中的所以页面,包括index.html页面,前面下载过的资源,扔会再下一遍.
7. 服务器返回所以要求被要求缓存的资源
8. 浏览器开始下载需要在本地缓存的资源,开始下载时触发ondownloading事件,在下载过程中不断触发onprogress事件.以便开发人员了解下载进度.
9. 下载完成后触发oncache事件.缓存完成

当用户再访问index.html时,前面1~5完全相同,接下来检测mainfest文件是否有改变.

1. 没有改变触发onnoupdate事件,结束.
2. mainfest改变,执行第7,8部,当所以文件本地缓存下载完毕后,浏览器触发onupdateready事件,而不会触发oncached事件.

#5 使用worker创建多线程

worker中无法使用DOM、alert等与界面有关的操作.

使用理由:防止js阻塞主线程的js运行

WorkerAPI

1. onmessage: 获取前台js提交过来的数据
2. postMessage(data): 前台js通过postMessage触发Worker对象的onmessage事件.
3. importScripts(urls),导入多个js,importScripts("a.js","b.js");
4. sessionStorge/localStorage: 使用Worker操作Storage本地存储
5. Worker: 创建新的Worker对象启动嵌套线程
6. XMLHttpRequest: Worker使用XMLHttpRequest发送异步请求.
7. navigator: 与window的location属性类似
8. location: 与window的location属性相似
9. self: WorkerGlobalScope对象,代表当前Worker线程自身作用域.调用self的close()结束线程
10. setTimeout()/seInterval()/eval()/inNaN()/parseInt,等与界面无关的js核心函数,包括Array/Data/Math/Number/Object/String等.

写一段找出输入start和end之间素数的线程.

worker.js代码

	onmessage =function(event){
		var data =JSON.parse(event.data);
		var start =data.start;
		var end =data.end;
		var result ="";
		search:
		for (var n =start; n <= end :n++){
			if(n%i==0){
				continue search;
			}
			result +=(n+",");
		}
	}
	postMessage(result);

网页代码

	<input name="start" ...>
	<input name="end" ...>
	<input type=button inclick="cal();" ...>
	...
	var car =function(){
		var start = parseInt(document.getElementById("start").value);
		var end = parseInt(document.getElementById("end").value);
		//创建线程
		var cal = new Worker("worker.js");
		var data ={
			"start" : srart,
			"end" : end
		};
		//发送数据
		cal.postMessage(JSON.stringify(data));
		cal.onmessage = function (evnet){
			alert(event);
		}
	}

并行的两条Worker不能互相通信,但Wroker可嵌套.

#6 客户端通信

WebSocket: 服务器主动推送信息/客户端实时推送数据到服务器

##6.1 跨文档通信

window对象新增方法

1. targetWindow.postMessage(message,targetOrigin): 该方法用户向targetWindow中状态的HTML发送信息,targetOrigin表示接收html的域名.
2. onmessage: 调用方法:windows.onmessage =function(event){...}

	event中的属性:
	
	1. data: 数据
	2. orgin: 发送消息window的源域名
	3. lastEventID: 返回发送消失时间的ID
	4. source: 返回发送消息的窗口
	
html想发送要做

1. 获取接收消息的window对象
2. 调用接收消息的window对象的postMessage(any message)方法

html想接收要做

1. 本html绑定事件window.message = function(event){...};

跨文档消息传递

	//source.html
	var targetWin = window.open("接收方url",'_blank','windth=400,height=300');
	
	targetWin.onload =function(){
		targetWin.postMessage("传输消息","接收方域名");
	}
	
	window.onmessage =function(event){
		//忽略其他域名发送的消息
		if(event.orgin !="指定域名"){
			return ;
		}
		alert(event.data);
	}
	
	//接收页面.html
	window.onmessage = function(event){
		//忽略其他域名发送的消息
		if(event.orgin !="指定域名"){
			return ;
		}
		alert("接收到消息拉!"+event.data);
		event.source.postMessage("回传消息",event.origin);
	}
	
结果:

alert(接收到消息拉!传输消息);

alert(回传消息);

注意!一定要判断发送方的域名!!!!!一定要判断发送方的域名!!!!!一定要判断发送方的域名!!!!!

###6.2 WebSocket与服务器通信

以前方案:

1. 周期发送请求
2. 页面使用隐藏窗口与服务器长连接

WebSocket方法

1. send("数据");向服务器发送数据.
2. close();关闭该WebSocket.

WebSocket监听事件

1. onopen: 当WebSocket建立网络连接触发该.
2. onerror: 网络连接错误
3. onclose: WebScokt被关闭触发
4. onmessage: WebSocket接收到服务器数据时

WebSocket属性

1. readyState

	1.1 CONNECTING(0): WebSocket正在尝试连接
	
	1.2 OPEN(1): 已经连接
	
	1.3 CLOSING(2): 正在关闭连接
	
	1.4 CLOSED(3): 已经关闭连接
	
WebSocket与服务器通信步骤

1. WebSocket.Constructor(url,[DOMString protocols]);创建WebSocket对象
2. 发送信息: WebSocket对象的send()
3. 接收信息: WebSocket对象的onmessage属性绑定函数;	

实现客户端多人聊天,JAVA为例

客户端代码:

	var web_socket =new WebSocket("ws://域名:端口");
	
	web_socket.onopen =function(){
		web_socket.onmessage =function(event){
			document.getElementById('show').innerHTML += event.data +"</br>"
		}
	};
	
	var sendMsg =function(val){
		var inputElement = document.getElementByID('msg');
		webSocket.send(inputElement.value);
		inputElement.value="";
	}
	
	...
	
服务端代码

	import java.io.*;
	import java.net.*;
	import java.nio.charset.Charset;
	import java.security.MessageDigest;
	import java.util.regex.*;
	import java.util.*;
	import sun.misc.BASE64Encoder;

	public class ChatServer
	{
		// 记录所有的客户端Soccket
		public static List<Socket> clientSockets
			= new ArrayList<Socket>();
	public ChatServer()throws IOException
	{
		// 创建ServerSocket，准备接受客户端连接
		ServerSocket ss = new ServerSocket(30000);
		while(true)
		{
			// 接收到客户端连接
			Socket socket = ss.accept();
			// 将客户端Socket添加到clientSockets集合中
			clientSockets.add(socket);
			// 启动线程
			new ServerThread(socket).start();
		}
	}
	public static void main(String[] args)
		throws Exception{
		new ChatServer();
	}
	}
	class ServerThread extends Thread
	{
		private Socket socket;
		public ServerThread(Socket socket)
		{
			this.socket = socket;
		}
		public void run()
		{
		try
		{
			// 得到Socket对应的输入流
			InputStream in = socket.getInputStream();
			// 得到Socket对应的输出流
			OutputStream out = socket.getOutputStream();
			byte[] buff = new byte[1024];
			String req = "";
			// 读取数据，此时建立与WebSocket的"握手"。
			int count = in.read(buff);
			// 如果读取的数据长度大于0
			if(count > 0)
			{
				// 将读取的数据转化为字符串
				req = new String(buff , 0 , count);
				System.out.println("握手请求：" + req);
				// 获取WebSocket的key
				String secKey = getSecWebSocketKey(req);
				System.out.println("secKey = " + secKey);
				String response = "HTTP/1.1 101 Switching Protocols\r\nUpgrade: "
					+ "websocket\r\nConnection: Upgrade\r\nSec-WebSocket-Accept: "
						+ getSecWebSocketAccept(secKey) + "\r\n\r\n";
				System.out.println("secAccept = " + getSecWebSocketAccept(secKey));
				out.write(response.getBytes());
			}
			int hasRead = 0;
			// 不断读取WebSocket发送过来的数据
			while((hasRead = in.read(buff)) > 0){
				System.out.println("接收的字节数：" + hasRead);
				/*
					因为WebSocket发送过来的数据遵循了一定的协议格式，
					其中第3个〜第6个字节是数据掩码。
					从第7个字节开始才是真正的有效数据。
					因此程序使用第3个〜第6个字节对后面的数据进行了处理
				*/
				for (int i = 0 ; i < hasRead - 6 ; i++ ){
					buff[i + 6] = (byte) (buff[i % 4 + 2] ^ buff[i + 6]);
				}
				// 获得从浏览器发送过来的数据
				String pushMsg = new String(buff
					, 6 , hasRead - 6 , "UTF-8");
				// 遍历Socket集合，依次向每个Socket发送数据
				for (Iterator<Socket> it = ChatServer.clientSockets.iterator()
					; it.hasNext() ;)
				{
					try
					{
						Socket s = it.next();
						// 发送数据时，第一个字节必须与读到的第一个字节相同
						byte[] pushHead = new byte[2];
						pushHead[0] = buff[0];
						// 发送数据时，第二个字节记录发送数据的长度
						pushHead[1] = (byte) pushMsg.getBytes("UTF-8").length;
						// 发送前两个字节
						s.getOutputStream().write(pushHead);
						// 发送有效数据
						s.getOutputStream().write(pushMsg.getBytes("UTF-8"));
					}
					catch (SocketException ex)
					{
						// 如果捕捉到异常，表明该Socket已经关闭
						// 将该Socket从Socket集合中删除
						it.remove();
					}
				}
			}
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
		finally
		{
			try
			{
				// 关闭Socket
				socket.close();
			}
			catch (IOException ex)
			{
				ex.printStackTrace();
			}
		}
	}
	// 获取WebSocket请求的SecKey
	private String getSecWebSocketKey(String req)
	{
		//构建正则表达式，获取Sec-WebSocket-Key: 后面的内容
		Pattern p = Pattern.compile("^(Sec-WebSocket-Key:).+",
				Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
		Matcher m = p.matcher(req);
		if (m.find())
		{
			// 提取Sec-WebSocket-Key
			String foundstring = m.group();
			return foundstring.split(":")[1].trim();
		}
		else
		{
			return null;
		}
	}
	// 根据WebSocket请求的SecKey计算SecAccept
	private String getSecWebSocketAccept(String key)
		throws Exception
	{
		String guid = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11";
		key += guid;
		MessageDigest md = MessageDigest.getInstance("SHA-1");
		md.update(key.getBytes("ISO-8859-1") , 0 , key.length());
		byte[] sha1Hash = md.digest();
		BASE64Encoder encoder = new BASE64Encoder();
		return encoder.encode(sha1Hash);
	}
	}
	

# JavaScript高级程序设计

Javascript组成

1. ECMAScript
2. DOM(文档对象模型)
3. BOM(浏览器对象模型)


#1. 基础

##1.1 `<script>`元素

1. async: 下载脚本时,异步执行,不阻塞其他页面渲染.
2. defer: 是否延迟到文档完全解析和显示完再执行(但下载还是会顺序下载).

##1.2 typeof可能返回的值

1. undefined
2. boolean
3. string
4. number
5. object
6. function

`typeof null` 大部分浏览器会返回`object`,而部分返回`function`

##1.3 undefined

未声明和未定义

```javascript
var message;
alert(message);//undefined
alert(age); //报错

alert(typeof message);//undefined
alert(typeof age);//undefined;
```
##1.4 Object

1. hasOwnProperty(propertyName),判断属性是否在当前实例当中,而非原型链.
2. isPrototypeOf(obbject),检测参数对象是否为本对象的原型.

##1.5 逻辑或

1. 如果第一个操作数是对象,返回第一个操作数
2. 如果第一个操作数值为false,返回第二个操作数
3. 如果两个操作数都是对象,返回第一个参数.

##1.6 关系操作符

1. 当操作数是对象时,先执行valueOf(),如果没有此方法执行toString().

##1.7 没有块状作用域

体现在if for当中

```javascript
if(true){
    var name = 'K';
}
console.log(name); //K ,name作为了全局变量 for 也一样.
```

##1.7 数组

1. join('分隔符') toString()
    
    toString方法默认输出,分割的数组字符串,join就可以控制分隔符
2. push和pop方法实现栈,后进先去
3. push和shift(取出最前端的任意元素.)实现队列,先进先出.而pop和unshift(在最前端加任意元素..)能实现相反方向的队列.
4. 排序,当一个数组都存着人名和age的对象,要根据age升序排序

    ```javascript
    var items = [
        {name : 'K' , age :12},
        {name : '404' , age =21}
    ];
    items.sort(function(a,b)){
        return a.age-b.age;
    }
    ```
    
    哎呀我操,那我是不是比较每一个属性都写一个这样的匿名函数啊...我不...
    
    设置一个函数,每次只要传递给他要比较的属性即可
    
    ```javascript
    function createComparisonFunction(propertyName){
        return function(object1,object2){
            var value1 = object1[propertyName];
            var value2 = object2[propertyName];
            return value1 - value2;
        }
    }
    
    items.sort(createComparisonFunction('age'));
    ```
5. concat方法,参数为空,复制副本,若有参数,参数放在数组后面并返回,不会影响原来的数组.
6. slice(开始位置,结束位置)截取数组,不影响原来的数组
7. splice(起始位置,删除的项数,要插入的任意多项),原数组会改变,调用方法会返回删除的项.
8. every,filter,forEach,map,some方法参数组项的值,该项在数组的位置,数组本身
9. reduce,reduceRight方法,第一个参数为前一项,第二个参数为当前项,第三个参数为当前项索引,第四个为数组本身
    
##1.8正则

Regexp的实例属性

1. lastIndex,表示开始下一次搜索匹配字符的起始位置
2. source,返回字面量形式的正则表达式字符

Regexp方法

1. exec方法,返回一个数组,数组的元素包含2个额外属性,index:匹配字符串的位置,input: 应用正则表达式的字符串.
2. test:匹配返回true/false

##1.9 函数表达式和函数声明的区别

调用函数表达式必须在函数之后,而调用函数声明的函数则与位置无关.

##1.10 字符串的模式匹配方法

重要的是replace方法

```javascript
var text = 'cat,bat';
var result = text.replace('/at/g','K'); //cK,bK
```

第二个参数还可以是特殊的字符序列

| 字符序列 | 替换文本                     |
|----------|------------------------------|
| $$       | $                            |
| $&       | 匹配整个模式的子字符串       |
| $'       | 匹配的子字符串之前的子字符串 |
| $`       | 匹配的子字符串之后的子字符串 |
| $n       | 匹配的第n个捕获组子字符串    |
| $nn      | 匹配的第nn个捕获组的子字符串 |

```javascript
var text = 'cat,bat';
var result = text.replace('/at/g','K $1'); //K cat,K bat
```

下面演示过滤html字符过滤

```javascript
function htmlEscape(text){
    returntext.replace([/[<>"&]]/g,function(match,pos,originalTex){
        swith(match){
            case "<":
                return "&lt";
            case ">"
                return "&gt;";
            case "&":
                return "&amp;";
            case "\""
                return "&quot;";
        }
    });
}
```

##1.11 Math 获取随机数
```javascript
function selectForm(lowerValue,upperValue){
    var choices = upperValue - lowelValue +1 ;
    return Math.floor(Math.random() * choices +lowerValue);
}
var num = selectFrom(2,10);//获取包括2和10的一个数值.
```

##1.12 对象属性


1. 原型继承:如何取决在prototype还是在构造方法中定义哪些属性,构造函数定义实例间不同的属性,而prototype定义实例间共享的属性和方法

#2. 继承

##2.1 组合继承

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ['red','blue','green'];
}

SuperType.propertype.sayName = function (){
    alert(this.name);
};

function SubType(name,age){
    //继承属性
    SuperType.call(this,name);
    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age)
}

var instance1 = new SubType("K",21);
instnace1.colors.push("black");
console.log(instance1.colors); //red,blue,green,black
instance1.sayName();//K
instance1.sayAge(); //21

var instance2 = new SubType("404",22);
console.log(instance2.colors);//red,blude,green
instance2.sayName();/404
instance2.sayAge(); //22
```

##2.2 寄生组合继承

```javascript
function inheritPrototype(subType,superType){
    var prototype = Object(superType.prototype);
    prototype.constructor = subType;
    subType.prototype = prototype;
}

function SuperType(name){
    this.name =name;
    this.colors = ['red','blue','green'];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name,age){
    SuperType.call(this,name);
    this.age =age;
}

inheritPrototype(SubType,SuperType);

SubType.prototype.sayAge = function(){
    alert(this.age);
};
```

#3. 函数表达式

递归保险的写法

```javascript
var factorial = (function f(num){
    if(num<=1){
        return 1;
    }else {
        return num * f(num-1);
    }
});
```

模仿块级作用域

```javascript
(function(){
    块级作用域
})();
```
类似的写法很多,实际上立即运行里面的匿名函数,这样做事为了防止函数里的代码变量污染到全局变量.

第一个大的()代表包含的函数表达式,后面的()代表调用这个函数.和普通函数一样,可以在里面放参数,里面的函数即可访问到这些参数

这样做也可以防止闭包占用内存问题.因为函数一旦完成,里面的作用域链就会销毁.

# 4. 客户端检测

# 5. DOM

# 13. 事件

## 13.1 事件流

### 13.1.1 事件冒泡

事件冒泡: 即事件开始时由具体的元素(文档中嵌套层次最深的那个节点)接受,然后逐级向上传播到较为不具体的节点(文档)

IE9 FF Chrome Safari 都是将事件一直冒泡到window对象

### 13.1.2 事件捕获

捕获是从window开始捕获事件,从window一直捕获到具体的节点,但是IE8及其更早的版本不兼容

规范要求是从ducmnent开始捕获,但是浏览器一般从window就开始获取了

### 13.1.3 DOM事件流

事件捕获阶段->处于目标节点->事件冒泡阶段

DOM2级事件规范要求捕获阶段不要涉及事件目标,但IE9和FF和Chrome和Safari和Opera9.5及更高版本都会在捕获阶段触发事件对象上的事件.结果就有两个机会再目标对象上面操作事件

## 13.2 事件处理程序

### 13.2.1 HTML事件处理程序

html里写JS事件弊端主要有,当html刚显示出来,但当时事件处理程序可能还不具备执行的条件,就容易报错

### 13.2.2 DOM0级事件处理程序

这个是直接指定属性,优势有两: 1.简单,2: 浏览器兼容较好

但是这种绑定事件不能在chrome的`EventListeners`面板中看到

但是这个好像跟加载的时机有关,第一次打开页面的话,加载事件没问题,但是刷新后再加就加不上了

```javascript
//绑定事件
btn.onclick = function(){};
//取消绑定事件 除了可以取消DOM0事件,还可以取消HTML事件
btn.onclick = null;
```

### 13.2.3 DOM2级事件处理程序

DOM2的话主要有addEventListener()和removeEventListener

第一个参数为要绑定的事件,第二个是处理函数,第三个为`true(捕获阶段处理函数)|false(冒泡阶段执行函数)`

通过`addEventListener`添加的事件只能通过和removeEventListener移除

这意味着,addEventListener中的第二个参数如果是匿名函数,则无法移除

绑定多个事件,会按绑定顺序来触发

IE9及FF Chrome Opera都支持DOM2

### 13.2.4 IE事件处理程序

attachEvent和detachEvent方法

注意点

1. 这两个方法的this是window,而不是元素本身
2. 绑定多个事件,是按倒序来触发的

支持性 IE和Opera

## 13.3 事件对象

| 属性/方法                | 类型         | 读/写 | 说明                                                                                   |
|--------------------------|--------------|-------|----------------------------------------------------------------------------------------|
| bubbles                  | Boolean      | 只读  | 是否为冒泡                                                                             |
| cancelable               | Boolean      | 只读  | 表明是否可以取消事件的默认行为                                                         |
| currentTarget            | Element      | 只读  | 其事件处理程序当前正在处理事件的那个元素                                               |
| defaultPrevented         | Boolean      | 只读  | 为true表明已经调用了preventDefault(),DOM3级事件中新增                                  |
| detail                   | Integer      | 只读  | 与事件相关的细节信息                                                                   |
| eventPhase               | Integer      | 只读  | 调用事件处理程序的阶段: 1表示捕获阶段, 2 表示"处理目标", 3 表示冒泡阶段                |
| preventDefault           | Function     | 只读  | 取消事件的默认行为,如果cancelable是true,则可以使用这个方法                             |
| stopImmediatePropagation | Function     | 只读  | 取消事件进一步的捕获或冒泡,同时阻止任何事件处理程序被调用(DOM3级事件中新增)            |
| stopPropagation          | Function     | 只读  | 取消事件进一步的捕获或冒泡,如果bubbles为true,可以使用这个方法                          |
| target                   | Element      | 只读  | 事件的目标                                                                             |
| trusted                  | Boolean      | 只读  | 为true表示事件是浏览器生成的,为false表示事件时有开发者通过javascript创建的(DOM3级新增) |
| type                     | String       | 只读  | 被触发事件的类型                                                                       |
| view                     | AbstractView | 只读  | 与事件关联的抽象视图,等同于发生事件的window对象                                        |

this,currentTarget和target的关系

1. this和currentTarget永远相等
2. target指的是具体点击的最底层的标签.

### 13.3.2 IE中的事件对象


| 属性/方法    | 类型    | 读/写 | 说明                                                                              |
|--------------|---------|-------|-----------------------------------------------------------------------------------|
| cancelBubble | Boolean | 读/写 | 默认为false,但将其设置为true,就可以取消事件冒泡,和stopPropagation方法作用相同     |
| returnValue  | Boolean | 读/写 | 默认值为true,但将其设置为false就可以取消事件的默认行为,和preventDefault()作用相同 |
| srcElement   | Element | 只读  | 事件的目标,和target属性相同                                                       |
| type         | String  | 只读  | 被触发的事件类型  

## 13.4 事件类型

1. UI: 用户和元素进行交互触发
2. 焦点事件
3. 鼠标事件
4. 滚轮事件
5. 文本事件: 挡在文档中输入文本时触发
6. 键盘事件
7. 合成事件: 当为输入法编辑器输入字符时触发
8. 变动事件: 当底层DOM结构发生变化时触发

### 13.4.1 UI事件

1. load: window在加载完所有外部支援时触发,img元素、link元素和object元素也支持onload方法,IE9和其他高级浏览器支持script元素
2. unload: 一般绑定在window对象上,离开页面时起作用(但是笔者亲测无效)
3. resize: 当窗口发生变化时,触发,包括最小化和最大化(mac下最小最大化无触发)
4. scroll: window被滚动时触发

### 13.4.2 焦点事件

1. blur: 元素失去焦点时触发,但不冒泡
2. foucs: 获得焦点时触发,但不冒泡
3. focusin: 获得焦点时触发,冒泡
4. focusout: 失去焦点时触发

### 13.4.3 鼠标和滚轮事件

1. click: 当用户点击,或者点击后的按entor默认都为点击上次点击的地方
2. dbclick: 双击,DOM3标准
3. mousedown: 用户按下鼠标按钮时触发,不能通过键盘触发
4. mouseenter: 鼠标首次进入元素时触发,不冒泡
5. mouseleave: 鼠标移除到元素之外时触发,不冒泡
6. mousemove: 当鼠标指针在元素内部移动时重复触发,键盘不能触发
7. mouseout: 当鼠标原本在一个元素上,然后用户移动到另一个元素时触发,这另一个函数可能是外部的元素也可能是子元素,键盘不能触发
8. mouseover: 当指针位于一个元素外部,首次移入另一个元素之内时触发,键盘不能触发
9. mouseup: 鼠标松开时触发,键盘不能触发

注意只有 mouseenter和mouseleave不会冒泡

点击顺序,当其中一个被取消就执行不下去

1. mousedown
2. mouseup
3. click
4. mousedown
5. mouseup
6. click
7. dbclcik

客户端坐标位置(event.clientX,event,ClientY),不包括滚动的位置
页面坐标位置(event.pageX,evnet.pageY)
屏幕坐标位置(event.screenX,event.screenY)

IE8不知道页面坐标位置,可以通过scrollTop计算

> 4.修改键

当鼠标点下的时候,是否有按其他键
```
btn.addEventListener('click',function(event){
  if(event.shiftKey) {}
  if(event.ctrlKey) {}
  if(event.altKey) {}
  if(event.metaKey) {}//window下是wind Mac下是cmd
});
```

IE8不支持metaKey

> 5.相关元素

对于mouseover,主目标是获得光标的元素,相关目标是刚离开的元素

对于mouseout,住目标是被移走光标的元素,相关目标是刚进入的元素

event.relatedTarget|toElement|fromElement

代表的是一个元属性,只不过是兼容不同的浏览器,

只有mouseover、mouseout和变动事件含有这个属性

> 8.滚轮事件

其他浏览器的滚轮事件名称都是mousewheel

event.whellDelta 向上滚是正120的倍数,而Opera9.5是相反的

而FF的叫DOMMouseScroll

滚轮信息在event.detail 而向上方向都是-3的倍数

> 9.触屏设备

Android和iOS的特殊

1. 不支持dbclick
2. 如果mousemove事件导致内容变化,将不会有其他事发生,如果没改变,则依次发生mousedown,mouseup,click
3. mousemove事件也会触发mouseover和mouseout
4. 两个手指放在屏幕上且页面随手指一动而滚动时触发mousewheel和scroll事件

### 13.4.4 键盘与文本事件

1. keydown: 当用户按下任意键时触发,若不放开,重复执行
2. keypress: 用户按下字母或ESC时触发,若不放开,重复执行
3. keyup: 用户释放键盘时触发

键盘事件也有shiftKey ctrlKey altKey metaKey

当发生keydown和keyup时,event对象会包含一个keyCode属性,其值为ASCII

但是FF和Opera中分号键的keyCode为59,也就是ASCII码,但是IE和Safari返回186,即按键的键码

keypress事件被触发时,会包含一个charCode属性,

取得值后,通过String.fromCharCode()转换为实际字符

DOM3的变化

DOM3不再支持charCode属性

而是新增两个属性 key char

IE9支持了key属性

Chrome和Safari5增加了keyIdentifier属性

> 4.textInput事件

textInput事件智能放在可编辑区域,event中新增了data为用户输入的字符,还有inputMethod属性,其值为

1. 0: 浏览器不知道他怎么输入的
2. 1: 键盘输入
3. 2: 粘贴
4. 3: 拖放
5. 4: IME输入的
6. 5: 表单中选择一项输入的
7. 6: 手写
8. 7: 语音
9. 8: 几种方法组合
10. 9: 通过脚本输入的

支持textInput有IE9+ Safari Chrome

只有IE支持inputMethod

> 5.设备中的键盘事件

### 13.4.6 变动事件

> 1.删除事件

当removeChild replaceChild删除节点时,会触发DOMNodeRemoved事件,会冒泡

其中event.target代表被删除节点 event.relatedNode为其父节点的引用

> 2.插入节点

当appendChild replaceChild insertBefore 插入节点节点时 会冒泡

会触发DOMNodeInserted

其中event.target代表被新增节点 event.relatedNode为其父节点的引用

### 13.4.7 HTML5事件

> 1.contextmenu事件

用于window右键和Mac ctrl+单击可以触发上下文菜单

contextmenu是冒泡的

兼容性良好

> 2.beforeunload

只可以设置一个文案,在用户退出前弹出一次

设置方法 event.returnValue设置,同时函数返回相同的提示语

> 3.DOMContentLoaded

刚形成DOM树就会触发

不理会图形 CSS 和js

> 4.readystatechange

感觉没啥用

> 5.pageshow和pagehide

当浏览器例如FF和Opera有`往返缓存`时.不会执行onload,而会监听window上的pageshow

pageshow具有persitsted属性,表名页面是否保存在bfcache里了

pagehide则相反,也有persiststed属性

使用onunload处理意味着页面不会被bfcache.

IE10 和其他浏览器支持

> 6.hashchange

hashchange要绑定在window上,表名#后字符串有变化

event.oldURL和newURL

分别保存变化前后的完整url

IE8+和其他都支持hashchange,但是oldURL和newURL只有FF和Chrome和Opera支持,所以最好使用locaition保存

### 13.4.8 设备事件

> 1.orientationchange

移动的Safari有orientationchange事件表明iPhone的查看模式

要绑定在window上

window.orientation的值

1. 0: 正常竖屏
2. 90: 左90
3. -90: 右90
4. 180: 头朝下

> 2.MozOrientation

当FF检测到设备方向变化时,触发此事件,但是和orientationchange不同的是,这只提供一个平面的方向变化

要绑定在window上

event有x,y,z 值为-1到1

> 3.deviceorientation

要识别设备空间中朝向哪里

deviceorientation也要绑定在window上

event包含以下属性

1. alpha: 在围绕z轴转动时(左右转动),y轴的度数差,是一个0~360之间的浮点数
2. beta: 在围绕x轴转动时(前后转动),z轴的度数差,是一个-180~180之间的浮点数
3. absolute: 在围绕7轴转动时(旋转设备时),z轴的度数差,是一个-90到90的浮点数 

支持性 Safari CHrome 和Android的webkit

> 4.devicemotion

告诉开发者设备什么时候移动,可以检测手机是否在往下掉

需要绑定在window

event事件有以下属性

1. acceleration: 保护x,y,z三个属性,在不考虑重力的情况下,告诉你每个方向的加速度
2. accelerationIncludingGravity: 一个包含x,y,z属性的对象,考虑z上的加速度,告诉你每个方向的加速度
3. interval: 以毫秒表示时间值,必须在另一个devicemotion事件触发前传入,这个值在每个事件中应该是一个常量

### 13.4.9 触摸与手势事件

1. touchstart: 当手指触摸到屏幕时触发,即使已经有一个手指放在屏幕上了
2. touchmove: 当手指在屏幕上滑动时连续地触发,可以通过preventDefault阻止滚动
3. touchend: 当手指从屏幕上移开时触发
4. toucancel: 当系统停止跟踪触摸时触发,

以上事件都会冒泡,也都可以取消

1. touches: 表示当前跟踪的触摸操作的Touch对象的数组
2. targetTouchs: 特定于事件目标的Touch对象的数组
3. changeTouches: 表示自上次触摸以来发生了什么改变的Touch对象的数组.

每个Touch对象包含下列属性

1. clientX: 触摸目标在视口中的x坐标
2. clientY: 触摸目标在视口中的y坐标
3. identifier: 标示触摸的唯一ID
4. pageX: 触摸目标在页面中的x坐标
5. pageY: 触摸目标在页面中的y坐标
6. screenX: 触摸目标在屏幕中的x坐标
7. screenY: 触摸目标在屏幕中的y坐标
8. target: 触摸的DOM节点目标

事件顺序

1. touchstart
2. mouseover
3. mousemove(一次)
4. mousedown
5. mouseup
6. click
7. touchend

支持触摸的有Safari 和Android的Webkit

> 2.手势事件

1. getsturestart: 当一个手指已经在屏幕上而另一个手指触摸屏幕时触发
2. gesturechange: 当触摸屏幕的任何一个手指的位置发生变化时触发
3. gestureend: 当任何一根手指从屏幕上面移开时触发

事件除了有普通的触屏属性wait,还有

1. rotation: 变化引起的旋转角度,负表示逆时针旋转
2. scale: 两个手指的距离变化,从1开始,拉大则变大,缩小则变小

## 13.5 内存和性能

### 13.5.2 移除事件委托

小心空事件处理程序

1. 当为一个节点绑定了事件,而被父元素innertHTML改掉了,这个事件是无法移除的

## 13.6 模拟事件

支持的浏览器IE9 和其他浏览器

### 13.6.1 DOM中的事件模拟

可以在document通过createEvent方法创建event对象

DOM2中createEvent的参数为事件类型字符串的英文复数

DOM3又变回了单数

1. UIEventys
2. MouseEvents
3. MutationEvents
4. HTMLEvents

DOM2没有键盘事件,IE9只唯一支持DOM3键盘事件的浏览器

触发事件通过dispatchEvent

> 1.模拟鼠标事件

```javascript
var mouseEvent = createEvent('MouseEvents');
mouseEvent.initMouseEvent(
  type,
  bubles
  cancelable
  view(基本都是document.defaultView)
  detail(一般为0)
  screeX(整数)
  screeY(整数)
  clientX(整数)
  clientY(整数)
  ctrlKey(默认false)
  altKey(默认false)
  shiftKey(默认false)
  metaKey(默认false)
  button(整数,默认0,按下哪个鼠标键)
  relatedTarget(对象只有mouseovew和mouseout可用)
);
```

> 2.模拟键盘事件和模拟其他事件

支持下比较弱,先不记载

>4.自定义DOM

```javascript
var customEvent = document.createEvent('CustomEvent');
customEvent.initCustomEvent(自定义type,bubbles,cancelable,detail)

```

支持自定义事件有FF6+和IE9+


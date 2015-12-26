# DOM编程

浏览器独自实现JS和DOM

IE中.分别存在`jscript.dll` & `mshtml.dll`

为什么DOM会慢

因为分开实现,之间通过接口访问.之间需要`过桥费`

##1. 优化建议

1. 谨慎使用循环操作DOM

    因为这个过桥费,所以谨慎使用循环.

    若DOM操作有顺序,先放在一个字符串里,汇总完后再一次修改DOM.

2. `innerHTML` & `createElement`

    在旧浏览器中`innerHTML`比`标准DOM操作(document.createElement())`快

    现代浏览器相差无几,甚至有点反超.
    
3. 节点`clone`
    
    先`createElement`好所需元素.
    
    在循环中`clone`
4. HTML集合优化

    1. document.getElementByName();
    2. document.getElementsByClassName();
    3. document.getElementsByTagName();
    4. document.images;
    5. document.links;
    6. document.forms;
    7. document.forms[0].elements
    
    以上方法军返回HTML集合对象,非数组,因为没有`push()`和`slice()`等方法,可以根据索引获取对象和length获取长度
    
    建议如果频繁操作HTML集合,可转换为数组.
    
    避免在for循环中使用HTML集合.length因为每次都会重新计算.
    
    尽量采用局部变量,但要用到对象成员时.
    ```javascript
    function K(){
    var coll document.getElementsByTagName('div'),
        len = coll.length,
        name ='',
        el =null;
        for(var count =0;count<len;count++){
            el =coll[count];
            name = el.nodeName;
            type = el.nodeType;
            name = el.tagName;
        }
    
    }
    ```
    
5. 遍历DOM

    遍历查询某节点的所有子节点时,可以
    
    1. 使用`nextSibling`递归的查找所有子节点
    2. 使用`childNodes`查找所有子节点的集合
    
    因为方法2中用到HTML集合,所有在IE6/7上前者效率高很多,但是现代浏览器就相差无几了
    
    但是`nextSlibing`和`childNodes`都会有一个问题,会把HTML的注释/空格都算作一个元素节点.所以有了以下API的代替
    
    | 属性名 | 被替代的属性|
    | -- | -- |
    | children | childNodes |
    | childElmentCount | childNodes.length |
    | firstElementChild | firstChild |
    | lastElementChild | lastChild |
    | nextElementSlibling | nextSlibling |
    | previousElementSlibing | previousSlibing |
    
以上属性IE6/7/8只支持children属性,使用新的API会比旧的要快很多,因为少了空白等无用的节点个数.
    
6.选择器API
    
    ```javascript
    var elements = document.querySelectorAll('#menu a');
    var elements = document.getElementById('menu').getElementsByTagName('a');
    ```
    前者会返回一个`NodeList`--匹配节点的类数组对象.因为是数组比后者返回的HTML集合要快多了.
    
    前者从IE8开始支持.
        
##2. 重绘和重排

浏览器下载完所以组件--`HTML标记` `javascript` `CSS` `图片`后会生成两个内部数据结构.

1. `DOM`树 : 表示页面结构
2. 渲染树 : 表示DOM节点如何显示.

每一个`DOM树`需要显示的节点都至少在渲染树中存在一个对应的节点(隐藏DOM除外),渲染树的节点称为`帧` / `盒`,主要包含`padding` `border` `margin` `position`等元素,DOM树和渲染树构造完成,浏览器就开始绘制页面.

何为`重排` & `重绘`

1. 重排 : 当DOM影响了元素的宽 / 高时发生
    
    受影响部分在渲染树中失效,重新构造渲染树.

2. 重绘 : 完成了重排后.浏览器重新绘制手影响部分到网页中,这个过程叫重绘.

重排何时发生 :

1. 添加/删除可见DOM元素
2. 元素位置改变
3. 元素尺寸改变
4. 内容改变
5. 页面渲染器初始化
6. 浏览器窗口尺寸改变

当浏览器的滚动条出现时,整个页面重排.

###2.1 渲染树变化的队列和刷新

使用`offsetTop/Left/Width/Height`

or `scolllTop/...`

or `clientTop/...`

or `getComputedStyle()` //currentStyle in IE

当执行以上方法时,浏览器刷新队列,并立即执行上述代码.

在修改样式的过程中,尽量少用.

即使你要用.多次访问这些变量时,请赋值到一个局部变量里,不要多次访问以上属性.

###2.2 最少化重绘和重排

改变样式时

1. 多次改变样式

方法 1:
```javascript
element.style.borderLeft = ...;
element.style.borderRight = ...;
```

方法 2 :
```javascript
element.style.cssText += '; borderLeft :1px ...';
```
后者会只触发一次重排.

###2.3 批量修改DOM

当需要一些列DOM操作时,可以通过下面三个步骤优化

1. 使元素脱离文档流
2. 对其应用多重改变
3. 把元素带回文档

令DOM脱离文档流主要有三种方法

1. `display : none`(两次重排)
    
    这里就是批处理前先`none`掉,执行完了,再`display`回来.

2. 在文档外创建并更新一个文档片段.(一次重排 推荐.)
    
    ```javascript
    val ul = document.createDcoumentFragment();
    DOM操作.
    document.getElementById('指定部分').appendChild(fragment);
    ```
3. 把要改变的部分`clone`,再替换(两次重排)

    ```javascript
    var old = document.getElementById('修改部分');
    var clone =old.cloneNode(true);
    DOM操作`clone`
    old.parentNode.replaceChild(clone,old);
    ```
    
###2.4 让元素脱离动画流

假如你要在页顶添加一个`div`,导致整个页面向下移,会导致整个页面重排

避免页面中的大部分重排.

1. 使用绝对位置定位页面上的动画元素,让其脱离文档流.
2. 让元素动起来,当它扩大时,临时覆盖部分页面.尽量少的影响其他元素.
3. 当动画结束时恢复定位,从而智慧下移一次文档的其他元素.

###2.5 IE和:hover

例如表格中使用hover让所在行高亮,当表格很大时,应该避免这种效果.

###2.6 事件委托

不建议绑定事件过多

建议让时间冒泡,让统一的高层捕抓.

eg:

在导航栏中,`ul->li->a`,要把所有的a链接改为ajax更新页面.

```javascript
document.getElementByID('ul_ID').onclick =function (e){
    //IE6/7/8中会采取后面的方式获得全局事件对象..
    e = e || window.event
    
    var pageid,hrefparts;
    //获取触发事件的元素 后者还是为了兼容IE.
    var target = e.target || e.srcElement;
    
    if(target.nodeName !=='A'){
        return ;
    }
    
    //作者应该默认网页为../id.html
    从链接找到页面ID
    hrefparts =target.href=splict('/');
    pageid =hrefparts[hreparts.length-1];
    //把.html去掉..
    pageid = pageid.replace('.html','');
    
    ajaxRequets('xhr.php?page='+id,updatePageContent);
    
    
    if(typeof e.preventDefault == 'function'){
        //阻止浏览器默认行为 跳转页面
        e.preventDefault();
        //阻止事件冒泡&捕获
        e.stopPropagation();
    }else {
        e.returnValue =false;
        e.cancelBubble =true;
    }
}
```

##总结

1. 尽量减少DOM访问次数,尽可能在JS解决,然后采取操作DOM
2. 使用HTML最好把它放到数组里.
3. 动画中使用绝对定位.
4. 使用事件委托,减少绑定数量.




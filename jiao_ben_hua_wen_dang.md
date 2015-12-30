# 脚本化文档

`Document Object Model`

![文档节点的部分层次结构](QQ20151230-0.png)


每个Window对象都有一个document属性指向`Document`

Document对象表示窗口的内容.

##1. 获取文档元素
```javascript
//ID
//返回`element`
document.getElementById('ID_K');

//Name
//返回NodeList
document.getElementsByName("name_K");

//根据标签 
//返回HTMLCollection 在<<JavaScript权威指南>>第六版第367页中写的是返回NodeList,但是查了官网API是返回HTMLCollection的.
//因为HTML不区分大小写,所以这个也不区分大小写.
var first_p = document.getElementTagName("p")[0];
first_p_span = first_p.getElementsTagName("span");

//通过Class
document.getElementsByClassName("K container");

//通过CSS选择器 返回NodeList
document.querySelectorAll("#K div");
document.querySelector("#K div"); //返回第一个匹配的.

```

`HtmlDocument`还定义了一些快捷的访问方式.

只读的`img`,`form`,`a(包含href属性的)`等元素的集合.(返回HTMLCollection)

因为在`Document`对象中为`form` `img` `iframe` `applet` `embed` `ojbect`元素设置name属性,既在Document对象中创建以此name属性值为名字的属性.

不太建议这样的使用方法,以下都返回的是`HTMLColection`
```javascript
document.images;

//假如women定义了一个ID/name属性为K_404的form 
document.forms.K_404;
//or
document.K_404;

document.links
```

##2. Node属性

1. parentNode : 该节点的父节点,类似Document对象此属性为null.
2. childNode : 只读的类数组对象(NodeList对象),实时表示
3. firstChild lastChild
4. nextSibling previoursSibling
5. nodeType : 节点类型, 9表示Document节点,1表示Element节点,3表示Text节点,8表示Comment节点,11表示DcoumentFragment节点



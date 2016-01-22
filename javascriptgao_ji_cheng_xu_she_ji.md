# JavaScript高级程序设计

Javascript组成

1. ECMAScript
2. DOM(文档对象模型)
3. BOM(浏览器对象模型)


##1. 基础

#1.1 `<script>`元素

1. async: 下载脚本时,异步执行,不阻塞其他页面渲染.
2. defer: 是否延迟到文档完全解析和显示完再执行(但下载还是会顺序下载).

##1.2 typeof可能返回的值

1. undefined
2. boolean
3. string
4. number
5. object
6. function


##1.3 undefined

未声明和未定义

```javascript
var message;
alert(message);//undefined
alert(age); //报错

alert(typeof message);//undefined
alert(typeof age);//undefined;
```





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
    
    尽量采用局部变量,但要用到
5. 

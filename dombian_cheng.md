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
    
4. 

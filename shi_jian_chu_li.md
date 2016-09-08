# 事件处理

##1. `addEventListener` 和 `attachEvent`

前者支持IE9及其他主流浏览器,或者为支持IE8及其之前的IE.

区别.

1. `attacheEvent`不支持事件捕获
2. `attacheEvent`要添加`on`前缀
3. `attacheEvent`允许同一函数注册多次,而`addEventListener`同一函数只能注册一次
4. `attachEvent`注册的处理程序可能按任何顺序调用(~_~!!!!),`addEventListener`按照注册顺序调用.
5. `addEventListener`第三个参数为true,则注册为捕获事件处理,作为事件传播第一个阶段调用.




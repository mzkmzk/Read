# 事件处理

##1. `addEventListener` 和 `attachEvent`

前者支持IE9及其他主流浏览器,或者为支持IE8及其之前的IE.

区别.

1. `attacheEvent`不支持事件捕获
2. `attacheEvent`要添加`on`前缀
3. `attacheEvent`允许同一函数注册多次,而`addEventListener`同一函数只能注册一次


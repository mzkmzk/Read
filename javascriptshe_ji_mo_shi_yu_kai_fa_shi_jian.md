# JavaScript设计模式与开发实践

多态思想为了解决`做什么`和`谁去做`分离开来.

JavaScript不存在类型耦合,像鸡和鸭要用同一个方法发出声音.

Java的做法:

1. 继承相同父类
2. 各子类实现方法
3. 在测试代码调用同一方法

JavaScript

1. 各模型实现方法
2. 在测试代码中调用同一方法

因为JavaScript不存在类型判断,只取决于你有/没有这个方法即可.

一般在调用方法时候,判断有无这个方法即可

```javascript
类.方法 instanceof Function

or

typeof 类.方法 === 'function'
```

如果`true`那就放心的调用吧.
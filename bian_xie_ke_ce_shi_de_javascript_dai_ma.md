# 编写可测试的javas

# 1. 可测试的JavaScript

.....

# 2. 复杂度

复杂度代表糟糕,简单代表美好

衡量复杂度: JSLint,圈复杂度,代码行数,扇入和扇出

我们会花费50%的时间在调试和测试我们的代码

认识问题是解决问题的第一步,了解项目的复杂度问题所在,然后一个个解决

代码尽量减少

函数最好保持读写分离

圈复杂度是表示代码中独立现行路径的数量

```javascript
function sum(a, b) {
  if (typeof(a) !== typeof(b)) {
    throw new Error('Cannot sum different types!');
  } else {
    return a + b ;
  }
}
```
这里的话圈复杂度为2

有人说: 任何方法的圈复杂度都不应该大于10





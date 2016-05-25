# HTML5 Canvas

## 1.HTML5 Canvas简介

这章就是扯了下蛋,讲了几个demo.

比平常用定时器,设定动画效果

更优的做法为

```javascript
window.requestAnimFrame = (
  function(){
    return window.requestAnimationFrame ||
            window.webkitRequestAnimationFrame ||
            window.oRequestAnimationFrame ||
            window.msRequestAnimationFrame ||
            function (callback) {
              window.setTimeout(callback,1000/60);
            };
  }
)();
//应用时 感觉这样会爆栈?
(
  function animploop(){
    requestAnimFrame(animploop);
    render();
  }
)();
```

## 2. 在Canvas上绘图

###2.4 使用路径创建线段

### 2.4.1 设置路径的开始和结束

1. beginPath()
2. closePath()

当前变换矩形将影响路径中绘制的任何内容

### 2.4.2 动态绘图

1. lineCap: 定义端点
   1. butt: 默认值,端点垂直于线段边缘的平直边缘
   2. round: 以线宽为直径的半圆
   3. square: 以线宽为长、以一般线宽为宽的矩形

2. lineJoin: 定义两条线相交产生的拐角
   1. miter: 默认值,在连接处边缘延长相接
   2. bevel: 连接处是一个对角线斜角
   3. round: 连接处是一个圆
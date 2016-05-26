# HTML5 Canvas

# 1.HTML5 Canvas简介

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

# 2. 在Canvas上绘图

##2.4 使用路径创建线段

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

## 2.7简单画布变换

1. context.setTransform(a,b,c,d,e,f),重置并创建新的变换矩阵,再次绘制矩形.
    
    该方法和transform的区别主要在于,setTransform不会相对于其他变换来发送行为.即执行多次相同参数,效果一直,而transform会一直效果累加
    1. a: 水平旋转绘图
    2. b: 水平倾斜绘图
    3. c: 垂直倾斜绘图
    4. d: 垂直缩放绘图
    5. e: 水平移动绘图
    6. f: 垂直移动绘图
2. translate: 重新映射画布在(0,0)的位置
3. rotate: 旋转当前绘图
4. scale: 缩放
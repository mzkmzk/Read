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


# JavaScript高效图形编程

# 1. 代码的重用和优化

一些比较基本的css,dom优化

# 2. DHTML基础

距离了一个控制CSS的案例

# 3. 滚动

操作DOM使动画...

感觉这样的应该要淘汰了吧

# 4. 高级UI

旋转木马......

# 5. JavaScript游戏介绍

### 5.2.5 碰撞检测

对页面计切成成方格

只对周围方格的对象进行检测

# 6. HTML5画布

### 6.7.4 绘制直线好曲线

1. beginPath(): 开始新路径
2. moveTo(x, y): 设置路径的起始位置
3. LineTo(x, y): 定义从当前位置开始的直线
4. arc(x, y, 圆半径, 起始角度, 结束角度, 绘制弧线的方向): 定义弧
5. arcrTo(x1, y1, x2, y2, 弧度): 定义从当前位置开始的弧,方便制作直线之间的圆角
6. quadraticCureTo: 定义从当前位置开始的二次曲线
7. bezierCurveTo: 定义从当前位置开始的贝塞尔曲线
8. closePath: 结束路径
9. stroke: 勾画路径
10. fill: 填充颜色,会自动执行closePath命令

`to`是命令(LineTo, bezierCurveTo) ,to命令的结束位置也定义了下一个to的开始位置,可以把to想象成在纸上连续地画线

而moveTo就是领你将笔离开纸面,从其他地方重新开始画


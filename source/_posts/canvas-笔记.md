---
title: canvas 笔记
date: 2017-10-23 11:49:26
tags:
- canvas
- 前端
categories:
- 笔记
- 前端
- canvas
---

- 资料 [https://segmentfault.com/a/1190000008071757](https://segmentfault.com/a/1190000008071757)
- 手册 [http://canvas.migong.org/html5-canvas](http://canvas.migong.org/html5-canvas)

- 初始化

<!--more-->

```js
var canvas = document.getElementById('test-canvas');
/* 检查canvas是否可用 */
if (canvas.getContext) {
    alert('你的浏览器支持Canvas!');
} else {
    alert('你的浏览器不支持Canvas!');
}
var ctx = canvas.getContext('2d');
```
- 三次贝塞尔曲线

```js
ctx.beginPath();
// 开始点
ctx.moveTo(0, 40);
// 第一个峰的控制点 第二个峰的控制点 结束点
ctx.bezierCurveTo(40, 30, 70, 80, 100, 60);
ctx.strokeStyle = '#06e9a3';
ctx.stroke();
```

- 绘制图形

```js
ctx.clearRect(0, 0, 200, 200); // 擦除(0,0)位置大小为200x200的矩形，擦除的意思是把该区域变为透明
ctx.fillStyle = '#dddddd'; // 设置颜色
ctx.fillRect(10, 10, 130, 130); // 把(10,10)位置大小为130x130的矩形涂色
// 利用Path绘制复杂路径:
var path=new Path2D();
path.arc(75, 75, 50, 0, Math.PI*2, true);
path.moveTo(110,75);
path.arc(75, 75, 35, 0, Math.PI, false);
path.moveTo(65, 65);
path.arc(60, 65, 5, 0, Math.PI*2, true);
path.moveTo(95, 65);
path.arc(90, 65, 5, 0, Math.PI*2, true);
ctx.strokeStyle = '#0000ff';
ctx.stroke(path);
```

- 绘制文本

```js
ctx.clearRect(0, 0, canvas.width, canvas.height);
ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
ctx.shadowBlur = 2;
ctx.shadowColor = '#666666';
ctx.font = '24px Arial';
ctx.fillStyle = '#333333';
ctx.fillText('带阴影的文字', 20, 40);
```

- 文本居中

```js
ctx.textAlign = 'center';
ctx.textBaseline = 'middle';
ctx.strokeText(n.toFixed(0) + '%', centerX, centerY);
```


- 复杂的操作的优化方案：
    - 通过创建一个不可见的Canvas来绘图，然后将最终绘制结果复制到页面的可见Canvas中；
    - 尽量使用整数坐标而不是浮点数；
    - 可以创建多个重叠的Canvas绘制不同的层，而不是在一个Canvas中绘制非常复杂的图；
    - 背景图片如果不变可以直接用<img>标签并放到最底层。

- 绘制渐变

```js
var gradient = ctx.createLinearGradient(0, 0, width, height); // x0,y0,x1,y1
gradient.addColorStop(0, "magenta");
gradient.addColorStop(0.5, "blue");
gradient.addColorStop(1, "red");
ctx.strokeStyle = gradient;
```

- 绘制阴影

```js
ctx.shadowOffsetX = 10; // 设置水平位移
ctx.shadowOffsetY = 10; // 设置垂直位移
ctx.shadowBlur = 5; // 设置模糊度
ctx.shadowColor = "rgba(0,0,0,0.5)"; // 设置阴影颜色
```
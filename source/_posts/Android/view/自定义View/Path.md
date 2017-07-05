---
title: Path 
date: 2016-04-03 13:18:12
tags: [path, 自定义View]
category: android

---

# Path

<!--more-->

## moveTo、lineTo、quadTo、cubicTo、arcTo
- moveTo:不会进行绘制.只用于移动画笔
- lineTo:画直线
		`mPath.lineTo(300, 300);`
- quadTo:用于绘制圆滑曲线,即贝塞尔曲线

		mPath.quadTo(x1, y1, x2, y2) (x1,y1) 为控制点，(x2,y2)为结束点。

- cubicTo:同样是用来实现贝塞尔曲线的。
		mPath.cubicTo(x1, y1, x2, y2, x3, y3) (x1,y1) 为控制点，(x2,y2)为控制点，(x3,y3) 为结束点。

- arcTo:用于绘制弧线（实际是截取圆或椭圆的一部分）。
		mPath.arcTo(ovalRectF, startAngle, sweepAngle) , ovalRectF为椭圆的矩形，startAngle 为开始角度，sweepAngle 为结束角度。




---
title: 对于Rotate的图形解释
date: 2019-03-12 10:16:05
categories: [CSS]
tags: [css, rotate]
---

### 前言

开始本文之前，需要先对CSS的3d坐标轴有一定的概念，如下图所示：

![3D坐标轴](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1552369974256&di=1735bcc1a7fc3a053fe1e3cc8e6facda&imgtype=0&src=http%3A%2F%2Fwww.787866.com%2Fupload%2F2012-08-31%2F2012-08-31-426a7984c0-f7de-4725-84bb-bf5d96477aa7.jpg)

### 分析

#### RotateZ

![rotateZ](/images/rotate.png)

从上图可知，rotateZ旋转就是普通的rotate 2d 旋转，我们可以把一个图形压缩为一条线(图中蓝色虚线)，然后按给定角度就行旋转，红色虚线和蓝色虚线的夹角就是旋转的角度

> 注意：rotate 采取就近转到目标的原则
> - 小于180°，正常顺时针，但大于180°逆时针旋转
> - 设180°会逆时针转，-180°才顺时针
> - 设为360°的倍数值不会转

#### RotateX

![rotateX](/images/rotate1.png)

从上图可知，当绕X轴旋转时，图形的X边长不会变化，只有Y值发生改变，公式为： `cos deg * Y = Y'`，意思就是，当前选择角度的cos值乘以原先Y边的值等于旋转后新边的值

#### RotateY

![rotateY](/images/rotate3.png)

从上图可知，当绕Y轴旋转时，图形的Y边长不会变化，只有X值发生改变，公式为： `cos deg * X = X'`，意思就是，当前选择角度的cos值乘以原先X边的值等于旋转后新边的值

> Tips:
> 我们可以通过与 `perspective` 透视属性 和 `transform-style:preserve-3d` 属性配合，观察图形的3D变化


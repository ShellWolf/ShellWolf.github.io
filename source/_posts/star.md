---
title: 如何用CSS手绘一个5角星？
date: 2019-03-12 16:53:00
categories: [CSS]
tags: [css, border, 三角形, 星星]
---

### 前言

前2天和同事聊天的时候，被问到如何利用CSS手绘一个 5角星 图形，简单思考了下，想出了个比较简易的办法，详细分析见下文

### 分析

我们先来观察下5角星，发现它有以下几个特征：

1. 有5个角，对应5个三角形
2. 每个三角形都是等边三角形
3. 每个三角形之间间隔同样的角度，并按圆形排列

结合上述特征，就可以把问题进行切割，分解成下面几步：

1. 创建5个一样的等边三角形
2. 把每个三角形按（360°/5 = 72°）进行间隔排列
3. 给最后的中心空白区域位置填色

### 实现

#### 创建三角形

我们利用 border 法画三角形，进行三角形绘制之前，得先搞懂一个图，如下所示：

[border](/images/border.png)

分析上图，蓝色三角形（bottom）其实从它的顶点垂直下来一条线为准，将蓝色三角形分为左右两个小三角形，左边小三角形底边受left值影响，右边小三角形底边受right值影响，其它三角形也一样。

根据上述规则， 我们可以很方便得到一个角度为60°的等边三角形：

1. 指定bottom border的宽度，举例我们设为100px
2. 由于要创建一个 角度为指定角度（60°）的等边三角形，我们把三角形对分，成为2个直角三角形，这样，我们就有了一个指定为 30°的角和一个给定的bottom 值，通过勾股定理公式: `tan30°= X/(bottom值)`，很容易得到X的值，这样我们就确定了 left 和 right 的值为 √3/3*100px ≈ 58px

具体的代码如下所示：

```css
div{
  position: absolute;
  left: calc(50% - 58px);
  top: calc(50% - 58px);
  border-style: solid;
  border-width: 0 58px 100px 58px;
  border-color: red transparent currentcolor transparent;
  width: 0px;
  height: 0px;
}

```

#### 按圆形排列三角形

上面我们得到了我们想要的三角形，接下来我们就把他们按72°间隔进行排列，具体代码如下所示

```css
#div1 {
  transform: rotate(72deg)  translateY(-120%)
}

#div2 {
  transform: rotate(144deg)  translateY(-120%) 
}

#div3 {
  transform:  rotate(216deg) translateY(-120%)
}

#div4 {
  transform: rotate(288deg) translateY(-120%) 
}

#div5 {
  transform: rotate(360deg) translateY(-120%) 
}
```

#### 填充图形中间位置颜色

多个三角形围城一圈会组成，一个空白的区域，我们需要把它填充为与边框同样的颜色

```css
#fill {
  width: 166px;
  height: 158px;
  border: none;
  left: calc(50% - 83px);
  top: calc(50% - 79px);
  background: currentcolor;
  border-radius: 10px;
}
```

> 注意： border 和 填充体的颜色都取 currentcolor

### DEMO

DEMO链接地址（可在线测试）: https://codepen.io/shellWolf/pen/YgxOVm

### 结语

增强自己的分解意识，还有更好的方法等待去探索。

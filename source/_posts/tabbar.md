---
title: Tabbar 标签栏组件
date: 2019-03-14 11:17:46
categories: [Vue, 组件分析, Tabbar]
tags: [Vue, 组件]
---

### 前言

标签栏组件是什么样子的呢？ 看下图：

![tabbar](/images/tabbar.png)

下面我将分析如何实现效果

> 本文将不会给出具体的代码实现，但是会绘画出设计流程

### 实现

#### 布局

1. 首先我们来说说布局，父组件其实只是一个容器组件，我们对它进行flex布局，并用 flex = 1 根据子元素个数等分其宽度
2. 子组件内部有2个元素，一个图标，对应图标下方的文字（附加功能右上角徽章，这个不做分析），同样也采用flex布局，然后做居中处理，不过元素排列方向通过 `flex-flow: column` 改变为竖排，这样大致布局也就基本完成了

#### 逻辑

逻辑代码分析见下图：

![tabbar-logic](/images/tabbar-logic.png)

### DEMO

点击下面👇链接展示：

http://wechat.hand-china.com/hippius-ui/#/zh-CN/tab-bar



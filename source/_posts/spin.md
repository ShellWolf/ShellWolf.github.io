---
title: Spin 旋转提示组件
date: 2019-03-07 00:01:25
categories: [Vue, 组件开发, Spin]
tags: [Vue, 组件, CSS3]
---

### 前言

要实现一个spin效果，这里有2种实现实现方式：

1. 往 `img` 或 `embed` 标签中，传入svg文件（loading效果的svg）地址到 `src` 特性中，就可以了

 如下所示旋转效果（更多资源，查看附录）：

<p style="display: inline-flex">&nbsp; &nbsp; &nbsp; &nbsp;<img src="https://loading.io/spinners/message/index.messenger-typing-preloader.svg" alt="" width="80" height="80">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://loading.io/spinners/stripe/index.svg" alt="" width="80" height="80">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://loading.io/spinners/camera/index.svg" alt="" width="80" height="80">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://loading.io/spinners/microsoft/index.svg" alt="" width="80" height="80">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://loading.io/spinners/square/index.svg" alt="" width="77" height="77">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;</p>

2. 利用纯CSS3来绘制旋转效果 （下面将重点分析其绘制过程）， 如下图所示：

![spin](/images/loading.png)

> ***DEMO效果展示，请点击 [这里](http://wechat.hand-china.com/hippius-ui/#/zh-CN/spin) 查看***

### 实现

代码如下所示：

#### 模板代码

```html
<template>
  <img v-if="!!svgSrc" :src="svgSrc" :style="svgStyle">
  <div v-else :class="b()" :style="sizeStyle">
    <i v-for="i in 12" :class="b('item')" :style="colorStyle" :key="i"/>
  </div>
</template>
```

从上可知，模板代码非常简单，就是循环出了12个元素在一个容器中，构成 `Spin` 组件的主体，重要的是样式的编写，我们继续往下走👇

#### CSS代码 (采用stylus语法)

```styl
@keyframes spin-fade {
  0% {
    opacity: .85
  }
  50% {
    opacity: .25
  }
  100% {
    opacity: .25
  }
}

.spin {
  position: relative
  display: inline-block
  width: 40px
  height: 40px
  &__item {
    position: absolute
    left: calc(50% - 1px)
    top: 37.5%
    width: 2px
    height: 25%
    border-radius: 50% / 20%
    opacity: .25
    background-color: currentColor
    animation: spin-fade 1s linear infinite

    for num in (1..12) {
      &:nth-child({num}) {
        animation-delay: ((num - 1) / 12) s
        transform: rotate(30deg * (num - 6)) translateY(-150%)
      }
    }
  }
}
```

针对上述样式的解释：

1. 创建一个行内块的父容器盒子，设置为相对定位
2. 创建12个绝对定位的子元素，并设置其透明度为 .25 为后面动画打铺垫
3. 给每个子元素的四个角设置水平半径为50%，垂直半径为20%的圆角
4. 给每个子元素设置固定宽度2px，高度相对父容器自适应（占父容器的25%）
5. 通过 `top: 37.5%; left: calc(50% - 1px) ` 设置初始子元素为垂直居中的
6. 通过 `transform: rotate(30deg * (num - 6)) translateY(-150%)` 让第一个子元素旋转-150deg 改变正反向，然后向正方向移动（即向下（因为改变了正方向））1.5（37.5 / 25 = 1.5）个子元素高度的距离，确定第一个元素的初始位置，其它11个子元素按同样的规则进行排布，最后形成一个圆形排布
7. 最后给子元素设置动画，控制子元素的透明度，以 1/12 s 为间隔控制透明度的变化，形成视觉上的转动（其实只是子元素透明度的变化）

下面几张图分解关键步骤解释：

![spin](/images/spin.png)
![spin1](/images/spin1.png)
![spin2](/images/spin2.png)

***附录 ✨***

- [b函数](/2019/03/01/bem/)分析
- [loading svg 资源地址](https://loading.io/)
- [对border-radius的解释](/2019/03/08/border-radius/)

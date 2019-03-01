---
title: Grid 栅格组件
date: 2018-11-13 12:05:21
categories: [Vue, 组件开发, Grid]
tags: [Vue, 组件]
---

{% blockquote 戴圣, <<礼记·学记>> %}
学然后知不足
{% endblockquote %}

### 声明

---

{% note info %} **本篇文章是基于网上开源的UI库 vant、antd、element 等做的关于一个栅格组件的分析**
 {% endnote %}


### 组件分析

---

1. 首先，需要创建的是一个布局组件，基本的结构如下图所示：

![basic-layout](/images/layout.png)

从上图可知，一个布局组件最少需要2个容器，一个父容器组件 `<row>`，多个子容器组件 `<col>`，当然，这个简单结构是不足以支持复杂场景的，我们继续往下走

2. 现代浏览器常用的布局方式为 `flex布局`，但是为了考虑到兼容性问题，得有个兼容性版的基础布局

### 组件设计

---

为了考虑到实际情况，所以需要考虑，子容器 `列元素` 的占位 `span`、间距 `gutter`、 偏移 `offset` 问题， 且为了能更加细分和适应更多的场景，这里把 父容器 `行元素` 分为24栅栏，具体思路如下所示：

1. 考虑到组件的灵活性，把父子容器组件都设计为动态 `<component>` 组件，通过 `is` 属性指定具体标签

2. 在父容器 `<row>` 上，设计 `type`，通过类型传递来选择开不开起 `flex` 布局

3. 在父容器 `<row>` 上，传递 `gutter`，并在子容器 `<col>` 上，通过 `this.$parent` 对象，取到父容器上传递的 `gutter` 来设置列元素的间距，因为需要考虑到边界条件，容器2端的列元素应该没有padding，并且考虑到列元素应该被均匀拉伸，所以此时在父容器上通过 `gutter` 来计算并设置父容器的负边距，以此来处理这种情况，这也是为什么 `gutter` 传递到父容器而不是子容器的一个原因

4. 在子容器 `<col>` 上，传递 `offset`，来设置列元素的偏移值，这里通过设置左外边距 `margin-left` 来实现，计算规则 (与计算 `span` 一样) 如下所示，这里采用的是stylus语法：

```stylus
{ margin-left: "calc(%s * 100% / 24)" % $i }
```

容器具体的属性设计总结如下所示：

#### Row

| 参数 | 说明 | 默认值 |
|-----------|-----------|-------------|
| type | 布局方式，可选值为flex | - |
| gutter | 列元素之间的间距（单位为px）| - |
| tag | 自定义元素标签 | `div` |
| justify | Flex 主轴对齐方式 | `start` |
| align | Flex 交叉轴对齐方式 | `top` |

#### Col

| 参数 | 说明 | 默认值 |
|-----------|-----------|-------------|
| span | 列元素宽度 | - |
| offset | 列元素偏移距离 | - |
| tag | 自定义元素标签 | `div` |

### 布局样式分析

---

完整代码，请<a href='#appendGrid' style="color: blue">→戳此处←</a>

1. 为了方便使用循环，变量这些语法，采用stylus或其他预编译语言编写样式，`下面所有的css代码片段都采用stylus书写`
2. 为了考虑组件之后使用场景，需要良好的覆盖性和命名的独立性，这里采用[BEM命名规范](https://en.bem.info/methodology/quick-start/)
3. 为了创建兼容性版本的布局，这里子容器采用 `float` 布局，并设置盒模型为 `border-box` (内边距和边框在以设定的宽高内进行绘制)，更多[盒模型](https://www.cnblogs.com/svenjia/p/7701168.html)知识自行搜索，如下所示：

  ***{prefix}是stylus的插值语法，代表类名的前缀，表示设置了一个 prefix 变量，如：prefix = hips***

  ```stylus
  .{prefix}-col {
    float: left
    box-sizing: border-box
  }
  ```

  同时为了处理高度塌陷和margin无效等问题，需要我们在父容器上通过伪元素上的设置来清除浮动，如下所示：

  ```stylus
  .{prefix}-row {
    &::after {
      content: ""
      display: table
      clear: both
    }
  }
  ```

  在将 `type` 设为 `flex` 的时候，***设为Flex布局以后，子元素的float、clear和vertical-align属性将失效***，但还是需要将父容器上的伪元素清除如下所示：

  ```stylus
  .{prefix}-row {
    &--flex {
      display: flex
      &::after {
        display: none
      }
    }
  ```

至于上面为什么要那么设计，这里有一篇分析[Bootstrap和flex](https://www.jianshu.com/p/1978c7a3292d)的文章，也是栅格布局概念精要，推荐阅读!!

这里是一个在线测试demo，做了[float布局-处理高度塌陷和margin无效](https://codepen.io/shellWolf/pen/oVNaxq?editors=1100)等问题的分析，有兴趣可以自己实际演练下。

***<a name='appendGrid'>附录：</a>***

- 一个新属性介绍[display:flow-root](https://div.io/topic/1973)，本质上还是创建一个[BFC](https://www.cnblogs.com/libin-1/p/7098468.html)
- Row [源码](https://github.com/youzan/vant/blob/dev/packages/row)
- Col [源码](https://github.com/youzan/vant/blob/dev/packages/col)

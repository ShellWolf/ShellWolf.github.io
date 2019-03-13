---
title: Switch 开关组件
date: 2019-03-13 11:32:37
categories: [Vue, 组件开发, Switch]
tags: [Vue, 组件]
---

### 前言

`switch` 组件是一个很常用的基础组件，如下图所示：

![switch](/images/switch.png)

下面我们来一起开发一个自定义的 `switch` 组件吧

### 分析

因为 `switch` 组件是一个开关组件，它的可操作状态就和 checkbox 一样，所以，我们可以基于 `<input type="checkbox">` 标签为基础，对它进行改造封装

### 实现

#### 组件逻辑代码

我们可以在 `switch` 组件上创建双向数据绑定， 当用户触发 `input` 元素的输入事件时，自动更新到 `switch` 的数据

> 注意：内部的 `input` 元素的数据也是双向绑定

大致操作流程如下所示：

1. 在 computed 的 get 中 给 `currentValue` 赋初值
2. 当用户触发 input 的 `chang` 事件时，input 元素自动更新数据，也就是下文的 `currentValue` 变量， 同时发送一个自定义的 `change` 事件，停供给外界使用
3. 当 `currentValue` 发生变化时，会触发它 `setter` 方法，我们在 computed 的 set 中 发射默认的 `input` 事件
4. 在父组件中 用 v-model 自动接收上一步 发射事件的值，更新 `switch` 组件选择状态

具体代码如下所示：

```js
<template>
  <div
    :class="b()"
  >
    <input
      :class="b('input', { disabled })"
      :disabled="disabled"
      v-model="currentValue"
      type="checkbox"
      @change="onChange"
    >
  </div>
</template>

<script>

export default {
  name: 'switch',

  props: {
    value: Boolean,
    disabled: Boolean,
  },

  computed: {
    currentValue: {
      get () {
        return this.value
      },

      set (val) {
        this.$emit('input', val)
      },
    },
  },

  methods: {
    onChange () {
      this.$nextTick(() => {
        this.$emit('change', this.currentValue)
      })
    },
  },
})
</script>

```

#### 组件样式代码

```styl
.switch
  position: relative
  display: inline-flex
  align-items: center
  font-size: 14px

  &__input
    display: inline-block
    box-sizing: border-box
    appearance: none
    -webkit-appearance: none
    outline: none
    width: 52px
    height: 32px
    border: 1px solid #eee
    border-radius: 32px
    transition: background-color 0.1s, border 0.1s
    &--disabled
      opacity: 0.4
      pointer-events: none

    &::before,
    &::after
      content: ""
      position: absolute
      border-radius: 16px
      top: 0px
      left: 0px
      bottom: 0px
      right: 0px
      transition: transform .4s cubic-bezier(0.4, 0.4, 0.25, 1.35)
      background: #eee

    &::after
      width: 30px
      height: 30px
      border-radius: 15px
      background: #fff
      box-shadow: 0 1px 3px rgba(0, 0, 0, .3)
      margin-top: 1px

    &:checked
      background: #1f8ceb
      &::before
        transform: scale(0)
      &::after
        transform: translateX(100%  - 30px)
```

上述代码有以下几点需要注意的地方：

1: 设置 `-webkit-appearance: none` 去除 input 的原始外观
2: 通过 before 和 after 伪元素 构造 switch的动画元素
3: 通过 `input:checked` 选择器控制当选中时， before 和 after 的变化，配合 `transition`形成点击的动画效果

#### DEMO展示
点击下方链接查看 👇：
http://wechat.hand-china.com/hippius-ui/#/zh-CN/switch

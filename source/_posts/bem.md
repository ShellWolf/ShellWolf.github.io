---
title: BEM
date: 2019-03-01 17:08:19
categories: [Vue, BEM]
tags: [Bem, CSS, Mixin]
---

### 声明

{% note info %}原始代码出自vant组件库。 最新代码，[点击这里](https://github.com/youzan/vant/blob/dev/packages/utils/use/bem.ts){% endnote %}

### 前言

在实际项目中，当我们需要覆盖某些开源组件库样式的时候，我们会发现会有一些奇怪的CSS命名方式，如下所示：

```css
// 以下是CSS类名
.xx__xx
.xx__xx--xx
.xx--xx
```

为了搞清楚这种规则，通过查阅了解到这是一种叫做 `BEM`的命名方法。下面我就来了解下这个高大上的东西：

***BEM是一种方法，可帮助我们在前端开发中创建可重用的组件和代码共享***

更多中文解释见 https://www.cnblogs.com/dujishi/p/5862911.html
更全面详细的解释下 http://getbem.com

通过阅读上述文章，可知BEM命名是很重要的一种规范，但是纯手写起来，会有一些繁琐，为了将这些规则更好的和vue组件的HTML Class绑定相互结合，做如下封装：

```js
/**
 * bem helper
 * b函数内部，默认取当前组件名称做默认类名，如下，以'button'为例
 * b() // 'button'
 * b('text') // 'button__text'
 * b({ disabled }) // 'button button--disabled'
 * b('text', { disabled }) // 'button__text button__text--disabled'
 * b(['disabled', 'primary']) // 'button button--disabled button--primary'
 */

const ELEMENT = '__'
const MODS = '--'

const join = (name, el, symbol) => el ? name + symbol + el : name

const prefix = (name, mods) => {
  if (typeof mods === 'string') {
    return join(name, mods, MODS)
  }

  // 数组语法，传递一个数组
  if (Array.isArray(mods)) {
    return mods.map(item => prefix(name, item))
  }

  // 对象语法，传递对象
  const ret = {}
  Object.keys(mods).forEach(key => {
    ret[name + MODS + key] = mods[key]
  })
  return ret
}

export default {
  methods: {
    b (el, mods) {
      // 获取组件名称
      const { name } = this.$options

      if (el && typeof el !== 'string') {
        mods = el
        el = ''
      }
      el = join(name, el, ELEMENT)

      return mods ? [el, prefix(el, mods)] : el
    },
  },
}

```

将 `b` (bem的简写) 方法混入到组件中后，我们就可以直接在HTML模板中使用它，如下所示：

```html
<template>
  <div :class="b([type])">
    <div :class="[b('wrapper', { scrollable }), {'thinline--bottom': type=== 'line'}]">
      ...
    </div>
  </div>
</template>

<script>
expot default {
  name: 'tabs',
  data() {
    return {
      type: 'line',
      scrollable: true,
    }
  }
}
</script>
```

如上所示代码，将渲染出如下类名：
```css
.tabs--line
.tabs__wrapper
.tabs__wrapper--scrollable
.thinline--bottom
```

> 上述代码可以直接拷贝使用，可以在Vue项目中实际用起来看下效果啦。

---
title: 通用方法收集
date: 2019-03-01 09:25:20
categories: [Util, 函数]
tags: Util
---

### 前言

{% note info %}以下源代码大部分收集于各个开源库和公开代码{% endnote %}

---

#### <a name='auxiliary' href="#auxiliary" style="border: none"><span style="color: #1ca5f2">#</span> 常用辅助类函数</a>

```js
function isDef (value) {
  return value !== undefined && value !== null
}

function isObj (x) {
  const type = typeof x
  return x !== null && (type === 'object' || type === 'function')
}

function isArray (val) {
  return Object.prototype.toString.call(val) === '[object Array]'
}

// 将keba-case(短横线)的变量名转换为camelCase(驼峰式)
function camelize (str) {
  return str.replace(/-(\w)/g, (_, c) => c.toUpperCase())
}

// 范围限制 ,确保当前值在最大值和最小值之间
function range (num, min, max) {
  return Math.min(Math.max(num, min), max)
}

/* eslint-disable */
function isEmail(value: string): boolean {
  const reg = /^((([a-z]|\d|[!#\$%&'\*\+\-\/=\?\^_`{\|}~]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])+(\.([a-z]|\d|[!#\$%&'\*\+\-\/=\?\^_`{\|}~]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])+)*)|((\x22)((((\x20|\x09)*(\x0d\x0a))?(\x20|\x09)+)?(([\x01-\x08\x0b\x0c\x0e-\x1f\x7f]|\x21|[\x23-\x5b]|[\x5d-\x7e]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(\\([\x01-\x09\x0b\x0c\x0d-\x7f]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF]))))*(((\x20|\x09)*(\x0d\x0a))?(\x20|\x09)+)?(\x22)))@((([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.)+(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))$/i;
  return reg.test(value);
}

function isMobile(value: string): boolean {
  value = value.replace(/[^-|\d]/g, '');
  return /^((\+86)|(86))?(1)\d{10}$/.test(value) || /^0[0-9-]{10,13}$/.test(value);
}

function isNumber(value: string): boolean {
  return /^\d+$/.test(value);
}

// Is image source
export function isSrc(url: string): boolean {
  return /^(https?:)?\/\/|data:image/.test(url);
}
```

#### <a name='deepAssign' href="#deepAssign" style="border: none"><span style="color: #1ca5f2">#</span> deepAssign</a>

将所有可枚举属性的值从源对象复制到目标对象，返回目标对象

```js
const { hasOwnProperty } = Object.prototype

function assignKey (to, from, key) {
  const val = from[key]

  // 1.如果源对象的某个key对应的value是未定义的，直接返回
  // 2.如果源对象的某个key对应的value不是未定义的，但是目标对象对应的key的value未定义 直接返回
  if (!isDef(val) || (hasOwnProperty.call(to, key) && !isDef(to[key]))) {
    return
  }

  if (!hasOwnProperty.call(to, key) || !isObj(val)) {
    to[key] = val
  } else {
    to[key] = assign(Object(to[key]), from[key])
  }
}

export default function assign (to, from) {
  for (const key in from) {
    if (hasOwnProperty.call(from, key)) {
      assignKey(to, from, key)
    }
  }
  return to
}
```

> 注意1✨: 该方法会跳过那些值为 null 或 undefined 的源对象和目标对象；该方法对于目标对象中没有的key会直接复制源对象对应的key和value，不会发生深拷贝

> 注意2✨: 当Object以非构造函数形式被调用时，Object() 等同于 new Object()，主要用于处理给定值是 null 和 undefined·时, 创建并返回一个空对象，否则，将返回一个与给定值对应类型的对象

***[点击查看最新源码](https://github.com/youzan/vant/blob/dev/packages/utils/deep-assign.ts)***

#### <a name='raf' href="#raf" style="border: none"><span style="color: #1ca5f2">#</span> raf</a>

[requestAnimationFrame官方介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)
下面是 requestAnimationFrame的 polyfill

```js
let prev = Date.now()

/* istanbul ignore next */
function fallback (fn) {
  const curr = Date.now()
  const ms = Math.max(0, 16 - (curr - prev))
  const id = setTimeout(fn, ms)
  prev = curr + ms
  return id
}

/* istanbul ignore next */
const root = Vue.prototype.$isServer ? global : window

/* istanbul ignore next */
const iRaf = root.requestAnimationFrame || root.webkitRequestAnimationFrame || fallback

/* istanbul ignore next */
const iCancel = root.cancelAnimationFrame || root.webkitCancelAnimationFrame || root.clearTimeout

export function raf (fn) {
  return iRaf.call(root, fn)
}

export function cancel (id) {
  iCancel.call(root, id)
}

```

***[点击查看最新源码](https://github.com/youzan/vant/blob/dev/packages/utils/raf.ts)***

#### <a name='scroll' href="#scroll" style="border: none"><span style="color: #1ca5f2">#</span> scroll</a>

获取html元素对应的一些位置信息的封装

```js
export default {
  // get nearest scroll element
  getScrollEventTarget (element, rootParent = window) {
    let currentNode = element
    // bugfix, see http://w3help.org/zh-cn/causes/SD9013 and http://stackoverflow.com/questions/17016740/onscroll-function-is-not-working-for-chrome
    while (currentNode && currentNode.tagName !== 'HTML' && currentNode.tagName !== 'BODY' && currentNode.nodeType === 1 && currentNode !== rootParent) {
      const overflowY = this.getComputedStyle(currentNode).overflowY
      if (overflowY === 'scroll' || overflowY === 'auto') {
        return currentNode
      }
      currentNode = currentNode.parentNode
    }
    return rootParent
  },

  getScrollTop (element) {
    return 'scrollTop' in element ? element.scrollTop : element.pageYOffset
  },

  setScrollTop (element, value) {
    'scrollTop' in element ? element.scrollTop = value : element.scrollTo(element.scrollX, value)
  },

  // get distance from element top to page top
  getElementTop (element) {
    return (element === window ? 0 : element.getBoundingClientRect().top) + this.getScrollTop(window)
  },

  getVisibleHeight (element) {
    return element === window ? element.innerHeight : element.getBoundingClientRect().height
  },

  getComputedStyle: !Vue.prototype.$isServer && document.defaultView.getComputedStyle.bind(document.defaultView),
}
```

***[点击查看最新源码](https://github.com/youzan/vant/blob/dev/packages/utils/scroll.ts)***


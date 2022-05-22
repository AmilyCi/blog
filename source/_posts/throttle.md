---
title: 节流与防抖
date: 2020-08-17 20:20:30
tags: JS
categories:
  - 性能优化
---

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;节流和防抖是我们平常开发经常会用到的性能优化之一，它们都是用来限制函数的执行频率，用来优化函数触发频率过高导致的响应速度跟不上，延迟假死或卡顿的现象，下面就让我们来详细的分析一下怎样来做节流与防抖。

### 防抖

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;函数的防抖就是在一定时间内如果某个事件被高频的触发,那么我们就延时让他执行，比如，我们可以设置一个定时器，时间为 3s，当我们第一次触发行为时，计时器开始计时，如果在倒计时时间内没有再次触发行为，那么计时结束后会执行函数，但是在计时时间内如果又触发了该行为，则计时器开始重新计时，以此类推，其实防抖就是在触发高频率的行为时只执行一次函数，这样通过控制次数来提高性能。

> 简述：事件触发 n 秒后被执行回调，如果 n 秒内再次触发，则重新计时

假如我们有一个滚动事件，我们想监听滚动行为来执行函数，用防抖可以这样来做

- 简易版

```js
function debounce(fn, wait) {
  let timeout
  return function () {
    let context = this
    let arg = arguments
    if (timeout) clearTimeout(timeout)
    timeout = setTimeout(() => {
      fn.apply(context, arg)
    }, wait)
  }
}
function print() {
  console.log('我是防抖')
}
window.addEventListener('scroll', debounce(print, 1000))
```

- 立即执行版(升级)

如果希望触发之后立即执行函数，然后等待 n 秒后才可以重新触发执行，并且在 n 秒内再次触发会重新计时 n 秒

```js
function debounce(fn, wait, immediate) {
  let timer
  return function () {
    const context = this
    const arg = arguments
    if (timer) clearTimeout(timer)
    if (immediate) {
      const callNow = !timer
      timer = setTimeout(function () {
        timer = null
      }, wait)
      if (callNow) fn.apply(context, arg)
    } else {
      timer = setTimeout(function () {
        fn.apply(context, arg)
      }, wait)
    }
  }
}
```

- 简述过程(基础版本)

  - 接收两个参数，fn 是要调用的函数，delay 是防抖时间
  - 设置一个 timer 定时器为 null
  - 返回一个闭包函数，保存 timer 确保其不会被销毁
  - 保存一下事件参数，防止 fn 函数需要事件参数里的数据
  - 之后先调用一下清除定时器
  - 再把定时器赋值给 timer
  - 定时器倒计时结束后执行 fn.apply(this, arguments)

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;这样如果在 1s 内我们多次触发这个行为，那么计时器会不停的重新计算，直到在 1s 内我们没有再次触发该行为，就会执行这个 print 事件

- 应用场景：
  - 按钮提交：防止多次提交，只执行最后一次得提交
  - 搜索框联想：防止联想发送请求，只发送最后一次

### 节流

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;函数的节流就是在高频的触发某个行为时，我们设置一个规定的时间，在该时间内只会执行一次，节流会稀释函数执行的频率。

> 简述：单位时间内多次触发只会执行一次

我们还是用上述的滚动事件来举例，代码如下

- 方案一

```js
function throttle(fn, wait) {
  let time = 0, timer = null
  return function () {
    let now = Date.now()
    let arg = arguments
    let context = this
    if (now - time > wait) {
      fn.apply(context, arg)
      time = now
    } else (
      timer && clearTimeout(timer)
      timer = setTimeout( () => {
        time = now
        fn.apply(context, arg)
      }, wait)
    )
  }
}
function print(){
  console.log('我是节流')
}
window.addEventListener('scroll', throttle(print, 1000))
```

- 方案二(时间戳版)

```js
function throttle(fn, wait) {
  let previous = 0
  return function () {
    let now = +new Date()
    let context = this
    let arg = arguments
    if (now - previous > wait) {
      fn.apply(context, arg)
      previous = now
    }
  }
}
```

- 方案三(定时器版)

```js
function throttle(fn, wait) {
  let timer
  return function () {
    let context = this
    let arg = arguments
    if (!timer) {
      timer = setTimeout(function () {
        timer = null
        fn.apply(context, arg)
      }, wait)
    }
  }
}
```

- 简述过程

  - 时间戳版
  - 接收两个参数，fn 要执行的回调函数，delay 延时的时间
  - 用一个变量 pre 存储初始时间 0
  - 返回一个闭包函数
  - 在闭包函数中，获取事件参数，以及当前调用的时间 now
  - 判断，如果当前调用的时间 now 减去 初始时间 pre 是否大于 延时时间 delay
  - 大于则执行 fn.apply(this, arguments)
  - 并更新 pre
  - 、、、
  - 定时器版
  - 接收两个参数，fn 要执行的回调函数，delay 延时的时间
  - 初始化变量 timer 为 null
  - 返回一个闭包函数
  - 在闭包函数中，获取事件参数 arguments
  - 判断定时器是否为空
  - 如果是的话，则开启定时器，延时为 delay
  - 定时器倒计时结束，则执行 fn.apply(this, aruments)，再将定时器 timer 设置为 null

- 应用场景：
  - 拖拽：固定时间内只执行一次，防止超高频次触发位置变动
  - 缩放：监控浏览器 resize

### 节流和防抖之间的区别

- 节流是不管触发事件多频繁，都会在一定的时间内执行一次，而防抖会在频繁触发的最后一次才执行

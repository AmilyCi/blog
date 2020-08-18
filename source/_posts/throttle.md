---
title: 节流与防抖
date: 2020-08-17 20:20:30
tags: JS
categories:
- 性能优化
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;节流和防抖是我们平常开发经常会用到的性能优化之一，它们都是用来限制函数的执行频率，用来优化函数触发频率过高导致的响应速度跟不上，延迟假死或卡顿的现象，下面就让我们来详细的分析一下怎样来做节流与防抖。
### 防抖
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;函数的防抖就是在一定时间内如果某个事件被高频的触发,那么我们就延时让他执行，比如，我们可以设置一个定时器，时间为3s，当我们第一次触发行为时，计时器开始计时，如果在倒计时时间内没有再次触发行为，那么计时结束后会执行函数，但是在计时时间内如果又触发了该行为，则计时器开始重新计时，以此类推，其实防抖就是在触发高频率的行为时只执行一次函数，这样通过控制次数来提高性能。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;假如我们有一个滚动事件，我们想监听滚动行为来执行函数，用防抖可以这样来做

```js
  function debounce(fn, wait) {
    let timeout
    return function () {
      if(!timeout){
        let context = this
        let arg = arguments
        timeout = setTimeout(() => {
          fn.apply(context, arg)
        },wait)
      }else {
        clearTimeout(timeout)
      }
    }
  }
  function prin(){
    console.log('我是防抖')
  }
  window.addEventListener('scroll', debounce(prin, 1000))
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;这样如果在1s内我们多次触发这个行为，那么计时器会不停的重新计算，直到在1s内我们没有再次触发该行为，就会执行这个 prin 事件

### 节流
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;函数的节流就是在高频的触发某个行为时，我们设置一个规定的时间，在该时间内只会执行一次，节流会稀释函数执行的频率。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;我们还是用上述的滚动事件来举例，代码如下

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
function prin(){
  console.log('我是节流')
}
window.addEventListener('scroll', throttle(prin, 1000))
```
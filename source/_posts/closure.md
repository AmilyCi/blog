---
title: 闭包
date: 2020-09-01 17:12:13
tags: JS
categories:
- JS基础
---
### 什么是闭包
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;一个函数A中包含另一个函数B，函数B可以获取函数A中的变量，函数B就是闭包。通常情况下我们在子作用域中可以获取父作用域中的变量，但是我们想要从父作用域中获取子作用域中的变量该怎么办呢，这时候我们可以通过闭包，返回子作用域中的变量，这样我们就可以获取了。

> 简述：闭包就是能够读取其他函数内部变量的函数。

```js
let a = 3;
function father(x){
  let b = 1 + x;
  return (function(){
    console.log('b', b) // b 6
  })()
}
console.log('a', a) // a 3
father(5)
```
这段代码中，我们就可以在全局作用域中获取函数作用域中的变量b了。

### 闭包实例
```js
for(var i = 0; i < 5; i++){
  setTimeout(function(){
    console.log(new Date, i)
  }, 1000)
}
console.log(new Date, i)
```
这是一道经典的面试题，控制台依次会输出什么，正确答案为：5，5，5，5，5，5假如用'->'表示为隔一秒钟，用','表示不间隔，那么怎么形容控制台的输出呢？答案为：5->5,5,5,5,5，是的，控制台会先输出一个5，之后过了一秒钟会连续输出五个5。
那么，让我们来分析一下，为什么并不是我们想要的输出：5，0，1，2，3，4呢？
在循环执行的过程中，几乎同时设置了5个定时器，这些定时器会在一秒之后触发，而循环之后的输出是立即执行的。
如果我们想让输出变为5->0,1,2,3,4，那应该怎么改写代码呢？
```js
for(var i = 0; i < 5; i++){
  (function(j){
    setTimeout(function(){
      console.log(new Date, j)
    }, 1000)
  })(i)
}
console.log(new Date, i)
```
或者可以用另一个办法
```js
for(var i = 0; i < 5; i++){
  setTimeout(function(j){
    console.log(new Date, j)
  }, 1000, i)
}
console.log(new Date, i)
```
还有一个最简单得办法
```js
for(let i = 0; i < 5; i++){
  setTimeout(function(){
    console.log(new Date, i)
  }, 1000)
}
console.log(new Date, i)
```
以上的几个方法都可以达到我们想要得结果

现在如果我们想要输出得结果为：0->1->2->3->4->5,那么我们应该怎么改写呢
```js
for(var i = 0; i < 5; i++){
  (function(j){
    setTimeout(function(){
      console.log(new Date, j)
    }, 1000 * j)
  })(i)
}
setTimeout(function(){
  console.log(new Date, i)
}, 1000 * i)
```
这是最简单直接得方法，我们还可以使用异步得Promise来完成

```js
const tasks = []
for (var i = 0; i < 5; i++){
  ((j) => {
    tasks.push(new Promise((resolve) => {
      setTimeout(() => {
        console.log(new Date, j)
        resolve()
      }, 1000 * j)
    }))
  })(i)
}
Promise.all(tasks).then(() => {
  setTimeout(() => {
    console.log(new Date, i)
  }, 1000)
})
```

以上的代码可以简写为下面这种方式

```js
const tasks = []
const output = (i) => new Promise((resolve) => {
  setTimeout(() => {
    console.log(new Date, i)
    resolve()
  }, 1000 * i)
 })

for(var i = 0; i < 5; i++){
  tasks.push(output(i))
}
Promise.all(tasks).then(() => {
  setTimeout(() => {
    console.log(new Date, i)
  }, 1000)
})
```
还可以使用async/await来实现
```js
const sleep = (time) => new Promise((resolve) => {
  setTimeout(resolve,time)
})
(async () => {
  for(var i = 0; i < 5; i++){
    if(i > 0){
      await sleep(1000)
    }
    console.log(new Date, i)
  }
  await sleep(1000)
  console.log(new Date, i)
})()
```
### 使用闭包应该注意的点

- 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

- 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值

### 闭包相关的文章

- 司徒正美：https://www.cnblogs.com/rubylouvre/p/3345294.html
- 阮一峰：http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html

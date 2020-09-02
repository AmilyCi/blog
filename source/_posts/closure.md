---
title: 闭包
date: 2020-09-01 17:12:13
tags: JS
categories:
- JS基础
---
### 什么是闭包
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;一个函数A中包含另一个函数B，函数B可以获取函数A中的变量，函数B就是闭包。通常情况下我们在子作用域中可以获取父作用域中的变量，但是我们想要从父作用域中获取子作用域中的变量该怎么办呢，这时候我们可以通过闭包，返回子作用域中的变量，这样我们就可以获取了。
```js
let a = 3;
function father(x){
  let b = 1 + x;
  return (frunction(){
    console.log(b)
  })()
}
console.log(a)
father(5)
```
这段代码中，我们就可以在全局作用域中获取函数作用域中的变量b了。
### 闭包经常考察的知识点
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

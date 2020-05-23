---
title: this
date: 2020-05-23 15:10:53
tags: JS
categories:
- JS基础
---
- 在构造函数中，this 指向 new 出来的实例本身。

- 在事件中，this 指向触发这个事件的对象。

- this 指向函数的直接调用者。
 
- 自执行函数中的 this 永远都是 window。

- 函数执行，首先看函数前面是否有"."，有的话，"."前面是谁 this 就是谁，没有的话，this 就是 window。

- 箭头函数中的 this 取决于包裹箭头函数的第一个普通函数 this。
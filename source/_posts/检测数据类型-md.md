---
title: 检测数据类型.md
date: 2020-09-28 21:00:30
tags: JS
categories:
- JS基础
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;在我们平时开发的过程中经常会需要检测数据类型，那么JS中检测数据类型的方式有几种呢，它们之间都有什么区别呢，下面就让我们深入的研究一下吧！

### typeof

typeof对于原始数据类型来说除了null以外，其它的都能正确检测出来。

```js
console.log(typeof 11n) // "bigint"
console.log(typeof 11) // "bigint"
console.log(typeof '') // "string"
console.log(typeof undefined) // "undefined"
console.log(typeof true) // "boolean"
console.log(typeof Symbol()) // "symbol"
console.log(typeof null) // "object"
```
- 用 typeof 检测数据类型，结果都会放在字符串中返回来。

- 通过上面的一些例子我们发现， typeof 检测 null 时返回的是 "object" 数据类型，这与我们所预期的结果不太一样。而这是JS历史遗留的问题，我们只要记住用 type 检测原始数据类型会存在这个坑就可以了。

下面我们来看看用 typeof 检测引用数据类型

```js
console.log(typeof []) // "object"
console.log(typeof {}) // "object"
console.log(typeof console.log) // "function"
```
从上面的几个例子我们可以看出来 typeof 检测引用数据类型，除了函数以外，其它的都会返回 "object"

所以 typeof 并不能准确的判断变量是什么数据类型，但是根据我们实际开发情况也可以选择使用。

### instanceof

instanceof 的内部机制是通过原型链来进行判断的。用来检测某一个实例是否属于某个类。

```js
function Person(){}
const person = new Person()
person instanceof Person // true
person instanceof Object // true

const str = 'Hello world'
str instanceof String // false

const str1 = new String('hello world')
str1 instanceof String // true
```
- 通过 instanceof 检测数据类型会返回一个 Boole 值

- 只要在当前实例的原型链上，我们用其检测出来的结果都为true

- 不能用来检测和处理字面量方式创建出来的基本数据类型值

用 instance 检测也不是那么完美，同样我们也可以根据开发需要来进行选择。
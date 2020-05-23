---
title: 检测数据类型的几种方式
date: 2020-05-23 13:13:40
tags: JS
categories:
- JS基础
---
### typeof
- typeof 检测基本数据类型，除了 null 以外其它的都可以检测出来，typeof(null) 返回的结果是 "object"。

- typeof 检测引用数据类型，除了函数以外其它返回的结果都是 "object"。

- 使用 typeof 检测数据类型，返回的结果是字符串包含着对应的数据类型。

```js
typeof(null); // "object"

typeof("2"); // "string"
```

### instanceof
- instanceof 检测某一个实例是否属于某个类。

- 不能用来检测和处理字面量方式创建出来的基本数据类型值。

- instanceof 的特性：只要在当前实例的原型链上，我们用其检测出来的结果都是true。

```js
const obj = new Object()

obj instanceof Object // true
```

### constructor
- 作用和 instanceof 非常的相似。

- constructor 可以处理基本数据类型的检测。

```js
const a = 111;
const b = {};
a.constructor === Number; // true
b.constructor === Object; // true
```

### Object.prototype.toString.call()

- 最准确常用的方式。

- 第一个object代表当前实例是对象数据类型的（这个是固定死的）第二个Object，代表的是obj所属的类是Object。

```js
Object.prototype.toString.call('111'); // "[object String]"

Object.prototype.toString.call([]); // "[object Array]"

Object.prototype.toString.call(()=>{}); // "[object Function]"

Object.prototype.toString.call(111); // "[object Number]"

Object.prototype.toString.call(null); //"[object Null]"
```


---
title: this
date: 2020-05-23 15:10:53
tags: JS
categories:
- JS基础
---
- 在构造函数中，this 指向 new 出来的实例本身。

```js
function Person(name, age){
  this.name = name
  this.age = age
  console.log('this:', this)
}
const person = new Person('Lucy', 18) 
// this: Person {name: "Lucy", age: 18}
```

- 在事件中，this 指向触发这个事件的对象。

```html
<button id="btn">按钮</button>
```
```js
let oBtn = document.getElementById('btn')
oBtn.onclick = function(){
  console.log(this)  // btn
}
```

- this 指向函数的直接调用者。

```js
function getName(name){
  this.name = name
  return function(){
    return this.name
  }
}

getName('AmilyCi')() // "AmilyCi"
```
 
- 自执行函数中的 this 永远都是 window。

```js
(function(){
  console.log(this)
})()   // window
```
- 函数执行，首先看函数前面是否有"."，有的话，"."前面是谁 this 就是谁，没有的话，this 就是 window。

```js
let user = {
  firstName: 'John',
  lastName: 'Dave',
  getFullName(){
    return this.firstName + ' ' + this.lastName
  }
}
user.getFullName() // "John Dave"
```

- 箭头函数中的 this 取决于包裹箭头函数的第一个普通函数 this。

```js
function getName(name){
  this.name = name
  return () => {
    return () => {
      console.log(this.name)
    }
  }
}
getName('AmilyCi')()() // AmilyCi
```

- 对于像 call,apply,bind 这种可以改变 this 指向的函数，this 取决于第一个参数，第一个参数为空的话，那么 this 就是 window。

```js
let user = {
  firstName: 'John',
  lastName: 'Smith'
}
function getFullName(){
  return this.firstName + ' ' + this.lastName
}
getFullName.call(user) // "John Smith"
```
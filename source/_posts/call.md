---
title: call、apply、bind
date: 2020-05-23 14:31:09
tags: JS
categories:
- JS基础
---
### 三者之间的区别
- call、apply、bind 都是用来改变 this 关键字的。

- call 和 apply 的用法是一样的，只不过在传参的时候，call 是一个一个的传，而 apply 是放在一个数组中传递。

- bind 方法在 IE6~8 下不兼容，在 call 和 apply 中改变 this 关键字和执行 fn 一起完成了，而在 bind 中只是改变了 this 关键字并且传参，但是 fn 并没有执行，执行 bind 会有一个返回值，而这个返回值就是我们把 fn 的 this 改变后的那个结果，所以 bind 只是对 fn 进行了预处理

### 实现call

```js
Function.prototype.myCall = function(context){  // 在函数的原型上绑定一个我们自定义的函数
  if(typeof this !== 'function'){  // 如果调用这个方法不是函数数据类型，那么就抛出一个错误
      throw new TypeError('Error')
  }
  context = context || window
  const fn = Symbol(1)  // 防止对象上有fn这个属性，被我们覆盖
  context[fn] = this  // 把函数作为对象的一个属性
  const args = [...arguments].slice(1)  // 获取传进来的参数
  const result = context[fn](...args)  // 将参数传入对象这个函数中，并执行返回结果
  delete context[fn]  // 删除对象这个函数属性
  return result   // 返回结果
}

let user = {
  name: 'John'
}

function sayHi(a,b){
  let sum = a + b
  return `Hi,${this.name},${sum}`
}

sayHi.myCall(user,1,2)  // "Hi,John,3"
```

## 实现apply

- apply 的实现方法其实和 call 基本一样，唯一的区别就是在传参时的处理，下面我们就看一下 apply 的实现大体思路

```js
Function.prototype.myApply = function(context){
  if(typeof this !== 'function'){
      throw new TypeError('Error')
  }
  context = context || window
  const fn = Symbol(1)
  context[fn] = this
  let result
  // 在处理参数上和 call 有一些区别
  if(arguments[1]){
    result = context[fn](...arguments[1])
  }else {
    result = context[fn]()
  }
  delete context[fn]
  return result
}

let user = {
  name: 'Alice'
}

function sayBye(a,b){
  const num = a + b
  return `Bye,${this.name},${num}`
}

sayBye.myApply(user,[2,3])  // "Bye,Alice,5"
```

### 实现Bind

```js
Function.prototype.myBind = function(context){
  if(typeof this !== 'function'){
      throw new TypeError('Error')
  }
  const _this = this
  const args = [...arguments].slice(1)
  return function F(){
    // 如果被当做构造函数调用
    if(this instanceof F){
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}

let user = {
  name: 'Lucy'
}

function other(age, birthday){
  this.age = age
  this.birthday = birthday
}

const detail = other.myBind(user,18)
detail(2020)
console.log(user) // {name: "Lucy", age: 18, birthday: 2020}

const newDetail = other.myBind(user, 17)
const detail2 = new newDetail(2020)
console.log(user) // {name: "Lucy"}
console.log(detail2) // {age: 17, birthday: 2020}
```
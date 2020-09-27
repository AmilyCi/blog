---
title: js中的继承
date: 2020-09-25 19:58:31
tags: JS
categories:
- JS基础
---
### 1、原型链继承
- 原理：原型链对象变成父类的实例，子类就可以调用父类的方法和属性（利用原型让一个引用类型继承另一个引用类型的属性和方法）
```js
function Parent(){ }

Parent.prototype.age = 13
Parent.prototype.getName = function(){
  return this.name
}

function Child(name){
  this.name = name
}

Child.prototype = new Parent()

let child = new Child('AmilyCi')

console.log(child.age) // 13
console.log(child.getName()) // 'AmilyCi'
```
- 优点：实现起来简单
- 缺点：不能向父类传递参数，引用类型的属性会被所有实例共享

```js
function Parent(){
  this.likeSports = ['badminton', 'basketball', 'swimming']
}

Parent.prototype.age = 13
Parent.prototype.getName = function(){
  return this.name
}

function Child(name){
  this.name = name
}

Child.prototype = new Parent()

let person1 = new Child('AmilyCx')
let person2 = new Child('Json')

person1.likeSports.push('shopping')
console.log(person1.likeSports) // ["badminton", "basketball", "swimming", "shopping"]
console.log(person2.likeSports) // ["badminton", "basketball", "swimming", "shopping"]
```
你会发现，当我们给 person1 增加一个喜欢的运动时，我们打印出 person2 喜欢的运动，这时候 person2 的喜欢运动中也增加了这个运动。这个属性会被所有的实例共享。

### 2、构造函数继承
- 原理：子类构造函数内部调用父类构造函数，并传入this
```js
function Parent(name){
  this.name = name
  this.likeSports = ['badminton', 'basketball', 'swimming']
}

function Child(name){
  Parent.call(this, name)
}

Parent.prototype.getName = function(){
  return this.name
}

let person1 = new Child('Lucy')
let person2 = new Child('LiLy')
person1.likeSports.push('shopping')

console.log(person1.likeSports) // ["badminton", "basketball", "swimming", "shopping"]
console.log(person2.likeSports) // ["badminton", "basketball", "swimming"]
console.log(person1.name) // Lucy
console.log(person1.getName()) // Uncaught TypeError: person1.getName is not a function
```
这次，我们通过 call 方法，可以给父类传参啦，并且我们给 person1 增加一项喜欢的运动，person2 不会也被加上啦。
但是我们发现，我们创建的实例并不能调用父类的方法了，会报错，这是因为我们现在用的构造函数方式导致父类并不在子类的原型链上，所以子类无法调用父类的方法。

### 3、组合继承

- 原理：结合了前两个方法的优点

```js
function Parent(name){
  this.name = name
  this.likeSports = ['badminton', 'basketball', 'swimming']
}

Parent.prototype.getName = function(){
  return this.name
}

function Child(name, age){
  Parent.call(this, name)
  this.age = age
}

Child.prototype = new Parent()
Child.prototype.constructor = Child
Child.prototype.getAge = function(){
  return this.age
}

let person1 = new Child('Lucy', 18)
let person2 = new Child('Lily', 20)
person1.likeSports.push('shopping')

console.log(person1.likeSports) // ["badminton", "basketball", "swimming", "shopping"]
console.log(person2.likeSports) // ["badminton", "basketball", "swimming"]
console.log(person1.name) // Lucy
console.log(person1.getAge()) // 18
console.log(person1.getName()) // Lucy
```
这样我们解决了传参的问题，引用类型的属性共享的问题，还有调用父类原型上的方法的问题都解决了。
但是现在还有一个不足之处，在给 Child 原型赋值的时候都会执行一次父类构造函数，所以无论什么情况下都会调用两次父类构造函数

### 4、寄生组合继承

```js
function inheritProtype(child,parent) {
    var prototype = Object.create(parent.prototype);// 创建对象
    prototype.constructor = child;
    child.prototype = prototype
}
function Parent(name) {
    this.name = name
}
Parent.prototype.sayName = function(){
    console.log('My name is'+this.name)
}
function Child(name,age){
    Parent.call(this,name);
    this.age = age;
}
inheritProtype(Child,Parent);
Child.prototype.sayAge = function(){
    console.log('My age is'+this.age)
} 

var child1 = new Child('Keven',13);
var child2 = new Child('Lucy',15);
child1.sayName();
child2.sayName();
```
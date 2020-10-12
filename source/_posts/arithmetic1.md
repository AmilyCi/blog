---
title: 常见的算法题
date: 2020-05-09 15:35:28
categories:
- 前端技术
tags:
- 算法
---
### 1、输入是以数字组成的数组，例如 arr = [1, -2, 3, 4, -9, 6]。</br>任务是：找出所有项的和最大的 arr 数组的连续子数组。</br>写出函数 getMaxSubSum(arr)，用其找出并返回最大和。</br>例如：
```js
getMaxSubSum([-1, 2, 3, -9]) == 5
getMaxSubSum([2, -1, 2, 3, -9]) == 6
getMaxSubSum([-1, 2, 3, -9, 11]) == 11
getMaxSubSum([-2, -1, 1, 2]) == 3
getMaxSubSum([100, -9, 2, -3, 5]) == 100
getMaxSubSum([1, 2, 3]) == 6
```
- 方案一：需要循环两次
```js
function getMaxSubSum(arr) {
  let maxSum = 0;
  for (let i = 0; i < arr.length; i++ ){
    let partSum = 0;
    for (let j = i; j < arr.length; j++){
      partSum += arr[j]
      maxSum = Math.max(maxSum, partSum)
    }
  }
  return maxSum
}
```
- 方案二：只需要循环一次，最优解
```js
function getMaxSubSum(arr) {
  let maxSum = 0;
  let partSum = 0;
  for (let item of arr){
    partSum += item
    maxSum = Math.max(maxSum, partSum)
    if (partSum < 0) partSum = 0
  }
  return maxSum
}
```

### 2、数组去重
```js
let arr = ["Hare", "Krishna", "Hare", "Krishna","Krishna", "Krishna", "Hare", "Hare", ":-O", 3, 11, 20, 11, 0, 3]
unique(arr) // ["Hare", "Krishna", ":-O", 3, 11, 20, 0]
```
- 方案一：(因为for循环对数组的每一项都进行了校验，所以仅适用于小型数组)
```js
function unique(arr) {
  let result = []
  for (let item of arr) {
    if(!result.includes(item)){
      result.push(item)
    }
  }
  return result
}
```
- 方案二：
```js
function unique(arr) {
  return Array.form(new Set(arr))
}
```

### 3、实现一个函数,可以计算出x的n次方
- 方案一：
```js
function pow(x, n) {
  let result = 1
  for (let i = 0; i < n; i++){
    result *= x
  }
  return result
}
pow(2, 3) //8
```
- 方案二：
```js
function pow(x, n) {
  if(n == 1){
    return x
  }else{
    return x * pow(x, n - 1)
  }
}
pow(2, 3) //8
```

### 4、写一个mySetInterval(fn, a, b)，每次间隔 a, a + b, a + 2b, ... , a + nb 的时间, 然后写一个myClear，停止上面的 mySetInterval。

```js
function mySetInterval(fn, a, b){
  this.a = a
  this.b = b
  this.fn = fn
  this.time = 0
  this.timer = -1
  this.start = () => {
    this.timer = setInterval(function(){
      fn()
      this.time ++
      this.start()
      console.log(this.a + this.time * this.b)
    }, this.a + this.time * this.b)
  }
  this.stop = () => {
    clearInterval(this.timer)
  }
}
function print(){
  console.log('123')
}
const my = new mySetInterval(print, 1000, 2000)
my.start()
my.stop()
```
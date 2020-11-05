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

### 5、计算任意位数的两个正整数字符串之和

- 方法一：

```js
function add(a, b){
  let number1, number2, MAX_LENGTH, result = []
  if(a.length > b.length){
    number1 = a.split('').reverse()
    number2 = b.split('').reverse()
    MAX_LENGTH = a.length
  }else {
    number1 = b.split('').reverse()
    number2 = a.split('').reverse()
    MAX_LENGTH = b.length
  }
  for (let i = 0;i < MAX_LENGTH; i++){
    let sum 
    if(number2[i]){
      sum = (+number1[i]) + (+number2[i])
    }else {
      sum = (+number1[i])
    }
    if(result[i]){
      sum += 1
    }
    result[i] = String(sum % 10)
    if(sum >= 10){
      result[i+1] = '1'
    }
  }
  return result.reverse().join('')
}
```

- 方法二：

```js
function add(a, b){
  let result = '', c = 0
  a = a.split('')
  b = b.split('')
  while(a.length || b.length || c){
    c += ~~a.pop() + ~~b.pop()
    result = c % 10 + result
    c = c > 9
  }
  return result
}
```

### 6、给定一个对象，存储了世界富豪的名字(key)和资产(value)，求排行榜（按资产从大到小的富豪名字数组）。譬如输入{bill: 500, sam: 480, roark: 501}，返回['roark', 'bill', 'sam']

```js
function richRank(obj){
  let valueSort = Object.values(obj).sort((a,b) => b - a)
  let richName = []
  for(let i = 0; i < valueSort.length; i++){
    Object.keys(obj).map(item => {
      if(valueSort[i] == obj[item]){
        richName.push(item)
      }
    })
  }
  return [...new Set(richName)]
}

let obj ={bill: 500, sam: 480, roark: 501, dave: 501}
console.log(richRank(obj))
```

### 7、实现一个函数，判断输入的是否为回文字符串

- 方法一：
```js
function plalindrome(str){
  if (typeof str !== 'string') return false
  return str.split('').reverse().join('') === str
}
```

- 方法二：
```js
function plalindrome(str){
  if(typeof str !== 'string') return false
  let i = 0, j = str.length - 1
  while(i < j){
    if(str.charAt(i) !== str.charAt(j)) return false
    i++
    j--
  }
  return true
}
```

### 8、请写一个抽奖程序，已有参与抽奖的员工工号组成的数组staffIds。（规则：同一员工不可重复中奖，每轮执行抽奖程序，入参是本轮要抽取的中奖人数n，将中奖人工号打印出来）

```js
function shuffle(arr, n){
  for (let i = arr.length; i > 0; i--){
    const {index} = randomItem(arr, 0, i);
    [arr[index], arr[i-1]] = [arr[i-1], arr[index]]
  }
  return  arr.slice(0, n)
}
function randomItem(arr, from = 0, to = arr.length){
  const index = random(from, to)
  return {
    index,
    value: arr[index]
  }
}
function random(m, n){
  return m + Math.floor(Math.random() * n)
}
```
### 9、给出一个32位有符号的整数，你需要将这个整数中每位上的数字进行反转。如：123，输出321；-130，输出：-31；数值范围为[−2 31,  2 31 − 1]

```js
function reverse(x){
  let str = x + ''
  let result = ''
  if(x > 0){
    for(let i = str.length - 1; i >= 0; i--){
      result += str[i]
    }
  }else{
    for(let i = str.length -1; i > 0; i--){
      result += str[i]
    }
    result = -result
  }
  if(result < Math.pow(-2, 31) || result > Math.pow(2, 31) - 1){
    return 0
  }
  return result
}
```
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
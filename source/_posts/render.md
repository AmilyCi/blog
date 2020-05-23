---
title: 浏览器渲染整个页面的过程
date: 2020-05-23 14:46:39
tags: JS
categories:
- JS基础
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;首先在地址栏中输入 url 地址，浏览器先查找自己本地的 DNS 记录找到对应的 IP 地址，如果本地没有找到就去 DNS服务器上查找，获取 IP 地址后开始建立 TCP 连接遵循三次握手就可以获取数据，浏览器开始解析 Html 构建 DOM 树，并行请求 css/image/js，CSS 文件下载完成，开始构建 CSS 树，CSS 树构建完成后和 DOM 树一起生成渲染树，计算出每个节点在屏幕中的位置，通过显卡把页面画到屏幕上。
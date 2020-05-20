---
title: http和https
date: 2020-05-20 11:46:50
tags: http
categories:
- 网络基础
---
### http
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;http 协议的全称为```超文本传输协议```。http 是以明文的方式发送内容，不提供任何方式的数据加密，因此 http 不适合传输敏感信息。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;http 默认工作在TCP协议 80 端口。
### https
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;https 协议的全称为```超文本传输安全协议```。它是为了解决 http 传输不安全而诞生的。https 在 http 的基础上加入了 SSL 协议，SSL 依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;https 默认工作在 TCP 协议 443 端口。
### https与http的区别
- http 是明文传输，数据都是未加密的，安全性较差，https 数据传输的过程是加密的，安全性好。
- https 协议需要到CA申请证书，免费的证书比较少，所以需要一定的费用
- http 页面响应速度比 https 快，因为 http 使用 TCP 三次握手建立连接，客户端和服务器需要交换3个包，https 除了 TCP 的三个包，还需要加上ssl握手需要的9个包，一共是12个包。
- https 和 http 使用的是完全不同的连接方式，用的端口也不一样，http 用的是 80 端口，https 用的是 443 端口。
- https 其实就是建构在 SSL/TLS 之上的 http 协议，所以 https 比 http 要更耗费服务器资源。
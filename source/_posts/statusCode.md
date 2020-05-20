---
title: HTTP状态码
date: 2020-05-20 22:23:32
tags: http
categories:
- 网络基础
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;状态码的职责是当客户端向服务器端发送请求时，描述返回的请求结果。
- 2XX 成功
  - 200 OK：表示从客户端发来的请求在服务器端被正常处理了。
  - 204 No Content：表示服务器接收的请求已成功处理，但在返回的响应报文中 不含实体的主体部分。另外，也不允许返回任何实体的主体。
  - 206 Partial Content：表示客户端进行了范围请求，而服务器成功执行了这部分的 GET 请求。
- 3XX 重定向
  - 301 Moved Permanently：永久性重定向。表示请求的资源已被分配了新的 URI，以后 应使用资源现在所指的 URI。
  - 302 Found：临时性重定向。表示请求的资源已被分配了新的 URI，希望 用户(本次)能使用新的 URI 访问。
  - 303 See Other：表示由于请求对应的资源存在着另一个 URI，应使用 GET 方法定向获取请求的资源。
  - 304 Not Modified：表示客户端发送附带条件的请求时，服务器端允许请求访 问资源，但未满足条件的情况。
  - 307 Temporary Redirect：临时重定向。该状态码与 302 Found 有着相同的含义。
- 4XX 客户端错误
  - 400 Bad Request：表示请求报文中存在语法错误。
  - 401 Unauthorized：表示发送的请求需要有通过 HTTP 认证(BASIC 认证、 DIGEST 认证)的认证信息。另外若之前已进行过 1 次请求，则表示 用 户认证失败。
  - 403 Forbidden：表示对请求资源的访问被服务器拒绝了。
  - 404 Not Found：表示服务器上无法找到请求的资源。
- 5XX 服务器错误
  - 500 Internal Server Error：表示服务器端在执行请求时发生了错误。
  - 503 Service Unavailable：表示服务器暂时处于超负载或正在进行停机维护，现在无法 处理请求。
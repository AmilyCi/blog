---
title: 前端跨域解决方案
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;说到跨域我们不得不先来说说为什么会产生跨域。其实跨域就是指浏览器不能执行其它网站的脚本，它是由浏览器的同源策略造成的，是浏览器对javascript施加的安全限制。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;那什么又是同源策略呢？同源策略是一种约定，它是浏览器最核心也最基本的安全功能，所谓的同源是指域名、协议、端口相同。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;下面就让我来介绍几种解决跨域的方法吧！
### 1、JSONP
- 原理：就是利用```<script> ```标签没有跨域限制的漏洞。通过```<script>```标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时。
客户端
```xml
<script>
    function jsonp(data) {
        console.log(data)
    }
</script>
<script type="text/javascript" src="http://127.0.0.1:8888/get/jsonp"></script>
```
服务端
```js
router.get('/get/jsonp', (ctx, next) => {
    let callbackName = ctx.callback || 'callback'
    let data = {
        "errcode": 0,
        "data": {
            "name": "cx",
            "sex": "girl",
            "address": "BeiJing"
        }
    let jsonpStr = `~${callbackName}(${JSON.stringify(data)})`
    ctx.type = 'text/javascript'
    ctx.body = jsonpStr
    }
})
```
页面显示
![](./hello-world/result.png)
- 优点
    - JSONP 使用简单且兼容性不错
- 缺点
    - 只限于 get 请求
    - 它只支持跨域HTTP这种情况
    - jsonp在调用失败的时候，不会返回各种HTTP状态码

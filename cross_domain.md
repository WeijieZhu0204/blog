# 同源策略

同源策略是一种安全机制，其限制了从一个源加载的文档或脚本，与另外一个源的交互。



### 什么是同源

两个资源的协议、主机、端口三者均一致则为同源，否则为不同源（跨域）。其实跨域和跨源是同一个概念。



### 同源策略限制了什么？

同源策略限制主要有以下两种：

1. 跨源脚本API访问。

   无法访问iframe.contentWindow、window.parent、window.open等文档对象的属性和方法。(对于跨源文档通讯，一般使用**postMessage**解决)

2. 跨源网络访问。

   无法通过XMLHttpRequest请求跨源的资源。



### 为什么需要同源策略？

很多人认为跨域请求限制是为了保护服务端（服务的提供者），防止服务被入侵之类的。其实不然，反而是保护了服务的使用者(即用户)。

如果用户在浏览alipay.com的时候，无意中打开了一个钓鱼网站allpay.com，在allpay.com中随意向alipay.com的域发送提现请求（并且带上alipay.com的cookie凭证），用户的钱就会不翼而飞。这个案例其实可以延伸到CSRF问题，同源策略默认对script、iframe、img等标签不生效，可以绕过同源策略，有兴趣可以了解。

还有一种场景就是，钓鱼网站通过iframe内嵌了alipay.com的页面，用户在iframe内正常输入账号密码，钓鱼网站通过iframe.contentWindow监听用户的输入，就会泄漏账号密码。

因此同源策略是一种保护浏览器使用者的安全机制。



### 跨域资源共享(CORS)

CORS机制允许浏览器和服务器进行安全的跨域控制。简单的来讲，就是通过HTTP协议来告诉浏览器该资源是否允许跨源网络访问。



**浏览器如何验证是否允许跨域**

情况一：

对于某些不会对服务器数据产生副作用的HTTP请求方法，如：GET、HEAD。浏览器会直接发出请求，在请求响应头中根据headers判断是否拦截并抛出错误。实际上请求已经发出并响应。



情况二：

对于会有副作用的请求方法，如：POST、PUT、DELETE。浏览器会先发出OPTIONS预检请求，从响应中得知是否允许跨域访问。允许才会发出实际的HTTP请求。对于什么是产生副作用的请求可以自行了解。



**Access-Control-Allow headers**

下面列出用以控制跨域的请求头：

* Access-Control-Allow-Origin：允许的跨域来源
* Access-Control-Allow-Methods：允许的跨域请求方法
* Access-Control-Allow-Headers：允许的跨域请求headers
* Access-Control-Allow-Max-Age：在有效时间内，浏览器无须为同一请求再次发起预检请求



**跨域请求的cookie限制**

如果发出跨域http请求，浏览器默认不会带上cookie，因此无法识别用户的登录状态等信息。因此在发送请求的时候需要加上以下配置：

fetch需要设置**credentials：'include'**，如果是XMLHttpRequest需要设置**withCredentials：true**。

同时服务器响应应该返回**Access-Control-Allow-Credentials：true**。

对于需要携带cookie的请求，服务器不能设置Access-Control-Allow-Origin 的值为“*”。


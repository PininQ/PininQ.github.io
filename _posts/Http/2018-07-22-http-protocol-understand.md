---
layout: post
title: HTTP 协议理解
categories: HTTP
description: HTTP 协议的一些理解
keywords: HTTP, Web, request， response
---

HTTP 是 hypertext transfer protocol（超文本传输协议）的简写，它是 TCP/IP 协议的一个应用层协议，用于定义 Web 浏览器与 Web 服务器之间交换数据的过程。

HTTP 协议是学习 Web 开发的基石，不深入了解 HTTP 协议，就不能说掌握了 WEB 开发，更无法管理和维护一些复杂的 Web 站点。

HTTP 协议的版本：HTTP/1.0、HTTP/1.1


## 什么是 HTTP 协议

超文本传输协议（HTTP）的设计目的是保证客户机与服务器之间的通信。

HTTP 的工作方式是客户机与服务器之间的请求 - 应答协议。

Web 浏览器可能是客户端，而计算机上的网络应用程序也可能作为服务器端。

举例：客户端（浏览器）向服务器提交 HTTP 请求；服务器向客户端返回响应。响应包含关于请求的状态信息以及可能被请求的内容。

客户端连上 Web 服务器之后，若想获得 Web 服务器中的某个 Web 资源，需遵守一定的通讯格式，HTTP 协议用于定义客户端与 Web 服务器通讯的格式。简单来讲，HTTP 协议是对浏览器客户端和服务器端之间数据传输的格式规范。

## 查看 HTTP 协议的工具

1. 使用火狐的 firebug 插件「右键 → firebug → 网络」
1. 使用谷歌的「审查元素」
1. 使用系统自带的 telnet 工具「远程访问工具」
    - telnet localhost 8000 → 访问 Nginx 服务器
    - ctrl+] → 回车 → 可以看到回显
    - 输入请求内容
    - 回车，即可查看到服务器响应信息。

输入请求内容如下：
```
GET /login/ HTTP/1.1
Host: www.xhuajiao.top
```

##  HTTP 协议内容

请求「浏览器 -> 服务器」
```
GET /video/21/1/ HTTP/1.1
Host: www.xhuajiao.top
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: https://www.xhuajiao.top/1/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cookie: session=eyJjc3JmX3Rva2VuIjoiMjgxNDE2Y2Q0YThhYzhkNzUxMzIxMWRiNzgzNGM1NDYwYWY2YTc2NiJ9.DkBi0A.PGZxS2TIX31LJsyLvWT071faM0M
```


响应「服务器 -> 浏览器」
```
HTTP/1.1 200 OK
Server: nginx/1.12.0
Date: Mon, 30 Jul 2018 09:01:40 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 29872
Connection: keep-alive

响应的网页内容，较多，不粘贴了。
```

## HTTP 请求

![HTTP 请求](/images/posts/http/xhuajiao-request.png)

### 1. 请求行

GET /video/21/1/ HTTP/1.1

请求行用于描述客户端的请求方式、请求的资源名称，以及使用的 HTTP 协议版本号

#### 1.1 HTTP 协议版本

- HTTP1.0：当前浏览器客户端与服务器端建立连接之后，只能发送一次请求，一次请求之后连接关闭。
- HTTP1.1：当前浏览器客户端与服务器端建立连接之后，可以在一次连接中发送多次请求。（基本都使用 1.1）

注：通俗来讲，区别只有一个：当客户端与 Web 服务器建立连接后（底层是流的操作），如果采用 HTTP1.0 协议，流会立即断开；HTTP1.1 协议，流会保存一段时间，下次重新连接时，还继续使用这个流。

问题：一个 Web 页面中，使用 img 标签引用了三幅图片，当客户端访问服务器中的这个 Web 页面时，客户端总共会访问几次服务器，即向服务器发送了几次 HTTP 请求。

答案：浏览器一共对服务器发送 4 次请求。第一次请求页面，第 2~4 次依次请求图片资源。图片是一个资源，只要寻找另外一个资源就需要独立发送一个请求。但是如果三个 img 标签引用的是同一张图片，就只请求 2 次「事实上还是会请求 4 次」。这个是浏览器的作用，浏览器把对第 2、3 张图片的请求拦截下来，然后使用第 1 次请求到的资源返回给第 3~4 次请求。

```html
<img src="images/1.jpg"/>
<img src="images/1.jpg"/>
<img src="images/1.jpg"/>
```


PS：所以，在网站中没有用到的资源引用最好是删除，以减少浏览器对服务器的请求次数。

#### 1.2 请求资源

- URL：统一资源定位符。[https://www.xhuajiao.top/video/21/1/](https://www.xhuajiao.top/video/21/1/) 只能定位互联网资源。是 URI 的子集。
- URI：统一资源标记符。/video/21/1/ 用于标记任何资源。可以是本地文件系统，局域网的资源 [//192.168.14.102/video/21/1/](//192.168.14.102/video/21/1/)，可以是互联网。

#### 1.3 请求方式

常见的请求方式：GET、POST、HEAD、TRACE、PUT、CONNECT、DELETE

常用的请求方式：GET 和 POST

表单提交：

```html
<form action="提交地址" method="GET/POST">

<form>
```

GET 和 POST 区别

1）GET 方式提交
```
GET /login/?name=qinbin&password=123456 HTTP/1.1
Host: www.xhuajiao.top
Connection: keep-alive
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: https://www.xhuajiao.top/login/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
```

- 地址栏 `URI` 会跟上参数数据。以 ? 开头，多个参数之间以 & 分割。
- GET 提交参数数据有限制，不超过 1KB。
- GET 方式不适合提交敏感密码。
- 浏览器直接访问的请求，默认提交方式是 GET 方式。

2）POST 方式提交

```
POST /login/ HTTP/1.1
Host: www.xhuajiao.top
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: https://www.xhuajiao.top/login/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9

name=qinbin&pwd=123456
```
- 参数不会跟在 URI 后面。参数跟在请求的实体内容中。没有 ? 开头，多个参数之间以 & 分割。
- POST 提交的参数数据没有限制。
- POST 方式提交敏感数据

### 2. 请求头
```
Host: www.xhuajiao.top --- 当前请求访问的目标地址「主机: 端口」
Connection: keep-alive --- 浏览器跟服务器连接状态。close：连接关闭，keep-alive：保持连接
Cache-Control: max-age=0 --- 强制每次请求直接发送给源服务器，而不经过本地缓存版本的校验
Upgrade-Insecure-Requests: 1 --- 将所有连接强制以 https 或 wss 等 SSL 加密形式发送请求
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36 --- 浏览器类型
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8 --- 浏览器接收的数据类型
Referer: https://www.xhuajiao.top/1/ --- 当前请求来自于哪里
Accept-Encoding: gzip, deflate, br --- 浏览器接受的数据压缩格式
Accept-Language: zh-CN,zh;q=0.9 --- 浏览器接受的语言
Cookie: session=eyJjc3JmX3Rva2VuIjoiMjgxNDE2Y2Q0YThhYzhkNzUxMzIxMWRiNzgzNGM1NDYwYWY2YTc2NiJ9.DkBi0A.PGZxS2TIX31LJsyLvWT071faM0M --- 存储在用户本地终端上的数据
```
### 3. 实体内容

只有 POST 提交的参数会放到实体内容中

### 4. request 常见应用

- 获取浏览器类型
- 请求参数的中文乱码问题
- 各种表单输入项数据的获取


## HTTP 响应

![HTTP 响应](/images/posts/http/xhuajiao-response.png)

### 1. 响应行

#### HTTP 协议版本

#### 状态码: 服务器处理请求的结果（状态）

常见的状态：
- 200：表示请求处理完成并完美返回。
- 302：表示请求需要进一步细化。
- 404：表示客户访问的资源找不到。
- 500：表示服务器的资源发送错误。（服务器内部错误）

状态描述

### 2. 响应头

```
Server: nginx/1.12.0 --- 表示服务器的类型
Date: Mon, 30 Jul 2018 09:36:16 GMT --- 响应发出的时间
Content-Type: text/html; charset=utf-8 --- 表示服务器发送给浏览器的数据类型及内容编码
Content-Length: 219 --- 表示服务器发送给浏览器的数据长度
Connection: keep-alive --- 表示服务器和浏览器的连接状态。close：关闭连接 keep-alive: 保存连接
Location: https://www.xhuajiao.top/user/ --- 表示重定向的地址，该头和 302 的状态码一起使用。
Set-Cookie: session=.eJwtyzEOgCAMQNG7dHagUgo6OnsEEoMFEmOiCchkvLsObn94_wapJS_XuacDRugdErJECi6Ii9ag7hHjap0mMcQqZA6WGTpoNZXv8M2SYt9YZe0bJTV8bZJM82-WLcKI-nkBchAeww.DkBtkA.Ji-8izBd95tfTHVEFiJhVKCDh-4; HttpOnly; Path=/ --- 表示服务器发送给浏览器的 cookie 信息（会话管理用到）
```
### 3. response 常见应用

- 文件下载功能
- 302+location 实现重定向
    - 通过 response 实现请求重定向。
    - 请求重定向指：一个 Web 资源收到客户端请求后，通知客户端去访问另外一个 Web 资源，这称之为请求重定向。
    - 应用场景：用户注册。
    - Flask 中实现方式
        - redirect(url_for("login"))，url_for() 用来拼接 URL
        - 实现原理：302 状态码和 location 头即可实现重定向


## 总结

HTTP 协议：浏览器和服务器之间数据传输的格式规范

1）HTTP 请求格式：

```
请求行
请求头
空行
实体内容（POST 提交的数据在实体内容中）
```

2）HTTP 响应格式：

```
响应行
响应头
空行
实体内容（浏览器看到的内容）
```

## 参考文档


[HTTP 方法：GET 对比 POST](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

[JavaWeb 学习 ----http 协议](https://www.cnblogs.com/smyhvae/p/4126689.html)
---
title: "网络及其计算 - Lab4"
description: 
date: 2026-11-05T22:48:31+08:00
image: 
math: 
license: 
comments: true
draft: false
build:
    list: always    # Change to "never" to hide the page from the list
categories:
- "网络及其计算"
- "大学课程"
tags:
- "实验"
- "抓包"
- "HTTP"
---

# 网络及其计算 - 实验四

## HTTP 请求报文

```http
GET / HTTP/1.1
Host: 127.0.0.1:8080
User-Agent: ...
Accept-Language: ...

Content-Length: 15

username=admin
```

### 请求行

[方法] [请求路径] [版本号]

### 请求头（键值对）

Host: 主机及端口，这个是必须写的

User-Agent（可选）

Accept-Language（可选）

Content-Length（POST 的时候有）

### 请求体（POST 方法）

请求体和请求头之间**必须有一个空行**，这样服务器才能分开请求头和请求体。

即使 GET 方法没有请求体，最后也要写一个空行，标识请求头的结束。

请求体就是原始的二进制。

通过 Content-Length 告诉服务器有几个字节，

Context-Type 告诉服务器用什么方式去读这个二进制数据。

## HTTP 响应报文

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 108
Connection: close

<!DOCTYPE html>
<html>
	<body>
		<h1>Test</h1>
	</body>
</html>
```

### 状态行

[版本] [状态码] [描述]

一个 HTTP 响应报文最少只包含**一个状态行**和**一个空行**就可以。

### 响应头（可选键值对）

Content-Type：告诉浏览器响应体的类型，方便解析

Content-Length：二进制数据有几字节

Connection：告诉浏览器断开 TCP 连接还是保持

### 响应体

同样，响应体和响应头之间也必须有一个空行。

仍然是传输二进制数据，只需要在响应头告诉浏览器长度和类型就行。

## 代码及思路

### handleClient(Socket socket)

短连接，浏览器发一个请求，服务器回一个响应就断开连接。

在响应头加上 Connection: close。

在 Wireshark 里能抓到四次挥手的过程。

接收从 main 里传来的 socket，处理请求。

先从 socket 里读取请求行，拆出来**请求目录和方法**

GET / HTTP/1.1，通过空格分离。

#### GET

如果方法是 GET，调用 handleGet()。

这个函数只需要资源地址和一个输出流即可，不需要 Socket 了。

> 为什么分成这么多函数？
>
> 因为一个函数只干一件事，handleClient 只处理一次客户端的连接，
>
> 判断 GET 还是 POST 之后分给另外两个函数处理
>
> sendResponse 这个函数让 handleGet 和 handlePost 共用一个函数发送响应体
>
> 只需要一个输出流，状态码，类型和响应体就可以，Content-Length 可以直接计算
>
> 能共用的原因是，GET 和 POST 发送的响应体几乎格式相同


### handleGet(String url, BufferedOutputStream outputStream)

从 handleClient 接收资源地址和输出流，把对应资源输出给客户端。




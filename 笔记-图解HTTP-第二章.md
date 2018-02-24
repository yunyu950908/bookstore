# 笔记 - 《图解 HTTP》 第二章

> <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">知识共享署名-相同方式共享 4.0 国际许可协议</a>进行许可。

## 一、请求报文与响应报文

1. 请求报文
- 方法 URI 协议版本
- 请求首部字段
- 空行
- 请求实体

```bash
POST /user HTTP/1.1
Host: example.com
Content-Type: application/json

{"name":"liyu","age":"18"}
```

2. 响应报文
- 协议版本 状态码 原因短语
- 响应首部字段
- 空行
- 响应实体

```bash
HTTP/1.1 200 OK
Date:Sat, 24 Feb 2018 01:20:59 GMT
Cache-Control:private
Connection:Keep-Alive
Content-Length:95

{"name":"liyu","age":"18"}
```

## 二、HTTP 是无状态 (stateless) 协议

- HTTP 协议对发送过的请求和响应不做持久化处理
- 减少服务器 CPU 及内存资源的消耗
- 能更快地处理大量事务
- 确保协议的可伸缩性 （伸缩性???喵喵喵???）
- 通过 Cookie 控制“状态”

## 三、告知服务器意图的 HTTP 方法

1. `GET` 获取资源
2. `POST` 传输实体主体
3. `PUT` 传输文件
4. `DELETE` 删除文件
- HTTP/1.1 PUT 与 DELETE 方法自身不带验证机制，存在安全性问题，一般不使用
5. `HEAD` 获得报文首部
6. `OPTIONS` 询问支持的方法
- 如果不是访问特定资源而是对服务器本身发起请求，可以用一个 `*` 代替请求 URI

```bash
OPTIONS * HTTP/1.1
Host: example.com

HTTP/1.1 200 OK
Allow: GET,POST,HEAD,OPTIONS
```
7. `TRACE` 追踪路径
- TRACE 方法容易引发 XST(Cross-Site-Tracing 跨站追踪) 攻击，一般不使用
8. `CONNECT` 要求用隧道协议连接代理

## 四、持久连接节省通信量

1. 从建立 TCP 连接到断开 TCP 连接的过程

```bash
# client <==> server
SYN ==>
<== SYN/ACK
ACK ==>

(HTTP Request ==>)
(<== HTTP Response)

<== FIN
ACK ==>
FIN ==>
<== ACK
```

2. 持久连接 (HTTP Persistent Connections, 也称 HTTP keep-alive 或 HTTP connection reuse)
- 只要任意一端没有明确提出断开连接，则保持 TCP 连接状态
- 减少了 TCP 连接的重复建立和断开所造成的额外开销，减轻了服务器端的负载
- 减少开销的时间使 HTTP 请求和响应能够更早地结束， WEB 页面显示速度也响应提高
- HTTP/1.1 默认持久链接

```bash
# client <==> server
SYN ==>
<== SYN/ACK
ACK ==>

(HTTP Request ==>)
(<== HTTP Response)
(HTTP Request ==>)
(<== HTTP Response)
·
·
·
(HTTP Request ==>)
(<== HTTP Response)

<== FIN
ACK ==>
FIN ==>
<== ACK
```

## 五、管线化 (pipelining)

- 不用等待响应可直接发送下一个请求，同时并发多个请求，无需一个一个等待响应。

```bash
# client <==> server
SYN ==>
<== SYN/ACK
ACK ==>

(HTTP Request 1 ==>)
(HTTP Request 2 ==>)
(<== HTTP Response 1)
(<== HTTP Response 2)

<== FIN
ACK ==>
FIN ==>
<== ACK
```

## 六、使用 Cookie 的状态管理

> 通过在请求和响应报文中写入 Cookie 信息来控制客户端状态

- 响应报文首部字段 `Set-Cookie` 告知客户端保存 Cookie
- 下次请求时，客户端会自动在请求报文首部中添加 `Cookie` 字段值
- 服务器通过对比 Cookie 记录来获得之前的状态信息

1. 响应报文：

```bash
HTTP/1.1 200 OK
Date:Sat, 24 Feb 2018 01:20:59 GMT
Connection:Keep-Alive
Content-Type: text/plain; charset=UTF-8
Set-Cookie:user.id=ImLY; path=/; secure; HttpOnly
```

2. 请求报文：

```bash
GET /unread_count.json HTTP/1.1
Host: example.com
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
Accept: application/json, text/plain, */*
Cookie: user.id=ImLY
```

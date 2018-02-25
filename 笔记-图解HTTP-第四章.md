# 笔记 - 《图解 HTTP》 第四章

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">知识共享署名-相同方式共享 4.0 国际许可协议</a>进行许可。

## 一、状态码告知服务器端返回的请求结果

|     | 类别 | 原因短语 |
| --- | --- | --- |
| 1XX | Informational ( 信息性状态码 ) | 接收的请求正在处理 |
| 2XX | Success ( 成功状态码 ) | 请求正常处理完毕 |
| 3XX | Redirection ( 重定向状态码 ) | 需要进行附加操作一完成请求 |
| 4XX | Client Error ( 客户端错误状态码 ) | 服务器无法处理请求 |
| 5XX | Server Error ( 服务器错误状态码 ) | 服务器处理请求出错 |

## 二、14 个常用状态码

### 2XX 请求被正常处理

1. `200 OK`
- 表示从客户端发来的请求在服务器端被正常处理。
2. `204 No Content`
- 表示服务器接收的请求已经成功处理，但返回的响应报文中不含实体的主体部分，也不允许返回任何实体的主体。
3. `206 Partial Content`
- 表示客户端进行了范围请求，服务器执行成功，响应报文中包含由 `Content-Range` 指定范围的实体内容。

### 3XX 表明浏览器需要执行某些特殊的处理以正确处理请求

1. `301 Moved Permanently`
- 永久重定向。请求的资源被分配了新的 URI ，以后应使用资源现在所指的 URI 。如果用户已经把资源对应的 URI 保存为书签，这时应该按 `Location` 首部字段提示的 URI 重新保存。
2. `302 Found`
- 临时重定向。请求的资源被分配了新的 URI ，希望用户本次能使用新的 URI 访问。不更新书签。
3. `303 See Other`
- 临时重定向。明确表示客户端应当采用 `GET` 方法获取资源。
- 其他功能与 `302 Found` 相同。

> 当 301、302、303 响应状态码返回时，几乎所有的浏览器都会把 POST 改成 GET，并删除请求报文内的主体，之后请求会自动再次发送。

> 301、302 标准是禁止将 POST 方法改成 GET方法的，但实际使用时大家都会这么做。

4. `304 Not Modified`
- 客户端发送附带条件的请求，如果请求资源未满足条件，直接返回 `304 Not Modified` (服务器端资源未改变，可直接使用客户端未过期的缓存)。 
- 304状态码返回时，不包含任何响应的主体部分。 
- 304 和重定向没有关系。
 
> 附带条件指请求报文中包含 `If-Match`, `If-Modified-Since`, `If-None-Match`, `If-Range`, `If-Unmodified-Since` 中的任一首部。

5. `307 Temporary Redirect`
- 临时重定向。遵照浏览器标准，不会从 POST 变成 GET。但对于处理响应时的行为，各个浏览器可能出现不同情况。
- 其他功能与 `302 Found` 相同。

### 4XX 客户端是发生错误的原因所在

1. `400 Bad Request`
- 请求报文中存在语法错误
2. `401 Unauthorized`
- 表示发送的请求需要通过 HTTP 认证 (BASIC 认证、DIGEST 认证) 的认证信息。
- 响应必须包含一个适用于被请求资源的 `WWW-Authenticate` 首部用以质询 (challenge) 用户信息。
- 浏览器初次接收到 401 响应，会弹出认证用的对话窗口。
- 若之前已进行过 1 次请求，则表示用户认证失败。
3. `403 Forbidden`
- 服务端拒绝客户端的资源请求，客户端未获得文件系统的访问授权，或者访问权限出现了某些问题。
4. `404 Not Found`
- 服务器上无法找到客户端的请求资源，或服务器端拒绝请求且不想说明理由。

### 5XX 服务器自身发生错误

1. `500 Internal Server Error`
- 服务器端在执行请求时发生了错误，可能是服务端 Web 应用的 Bug 或某些临时故障。
2. `503 Service Unavailable`
- 服务端暂时处于超负载或正在停机维护，现在无法处理请求。
- 可以写入 `Retry-After` 首部字段返回给客户端。

> 502 Bad Gateway 表示网关或代理炸了

---

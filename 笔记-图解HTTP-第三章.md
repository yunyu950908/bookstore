# 笔记 - 《图解 HTTP》 第三章

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">知识共享署名-相同方式共享 4.0 国际许可协议</a>进行许可。

## 一、HTTP 报文

1. 报文首部

```bash
1. 请求行 / 状态行
2. 请求/响应 首部字段
3. 通用首部字段
4. 实体首部字段
5. 其他
```

2. 空行 (CR + LF)
3. 报文主体

## 二、编码提升传输速率

1. 报文主体 与 实体主体 差异

- 报文 (message)：HTTP 通信中的基本单位，由 8 位组字节流组成，通过 HTTP 通信传输。
- 实体 (entity)：作为请求或响应的有效荷载被传输，其内容由 实体首部 和 实体主体 组成。

> 通常，报文主体等于实体主体。只有当传输中进行编码操作时，实体主体的内容发生变化，导致与报文主体产生差异。

2. 压缩传输的内容编码

常用的内容编码：
- gzip (GUN zip)
- compress (UNIX 系统的标准压缩)
- deflate (zlib)
- identity (不进行编码)

3. 分割发送的分块传输编码

- 实体分割
- 每一块用十六进制标记大小
- 最后一块使用 "0(CR+LF)" 标记

4. 发送多种数据的多部分对象集合

- `multipart/form-data`
- `multipart/byteranges`

```bash
1. 使用 boundary 字符串划分多部分对象集合知名的各类实体。
2. 在boundary 字符串标记的各个实体的起始行之前插入 "--" 标记。
3. 在多部分对象集合对应的字符串的最后插入 "--" 标记作为结束。
```

5. 获取部分内容的范围请求 (Range Request)
> 应该就是断点续传吧 =。=

- 请求报文：
-请求 5001 之后的全部字节 `Range: byte=5001-` 
- 请求从零开始到 5000 字节 `Range: byte=-5000`

```bash
GET / HTTP/1.1
Host: example.com
Range: byte=5001-10000
```

- 响应报文
- 状态码 `206 Partial Content`
- 多重范围请求 `Content-Type: multipart/byteranges`

```bash
HTTP/1.1 206 Partial Content
Date: Sat, 24 Feb 2018 03:45:25 GMT
Content-Range: bytes 5001-10000/10000
Content-Length: 5000
Content-Type: image/jpeg
```

6. 内容协商(Content Negotiation) 返回最合适的内容

> 客户端及服务器就响应的资源进行交涉，然后提供给客户端最为合适的资源。

- Accept
- Accept-Charset
- Accept-Encoding
- Accept-Language
- Content-Language

1. 服务器驱动协商 (Server-driven Negotiation)
以请求的首部字段为参考，在服务端自动处理。以浏览器发送的信息作为判定依据，并不一定能筛选出最优内容。

2. 客户端驱动协商 (Agent-driven Negotitation)
由前端控制显示内容。

3. 透明协商 (Transparent Negotitation)
服务器驱动和客户端驱动的结合体，是由服务器端和客户端各自进行内容协商的一种方法。

---

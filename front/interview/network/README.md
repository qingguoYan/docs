# 301、302、304状态码代表着什么

## 301
    - 301 这是一个永久性重定向状态码,表示请求的资源已经被永久性地移动到了一个新的 URL
    - 当客户端再次请求该资源时,应该使用新的 URL

## 302
    - 这是一个临时性重定向状态码,表示请求的资源暂时位于一个不同的 URL
    - 当客户端再次请求该资源时,应该使用原始的 URL

## 304
    - 这是一个缓存相关的状态码,表示客户端发送的条件请求(如 If-Modified-Since、If-None-Match 头)已经被服务器验证,资源未发生变化
    - 服务器返回 304 Not Modified 状态码,同时不返回资源内容,而是让客户端使用缓存的副本

# Http缓存

## 强缓存
- 强缓存是一种客户端缓存机制,当客户端再次请求资源时,不需要向服务器发送请求,直接使用本地缓存
- 强缓存通常通过 Expires 或 Cache-Control 头来控制

假设有一个网页 index.html，它包含了一些静态资源,如 CSS 文件 style.css 和 JavaScript 文件 script.js。
当用户第一次访问 index.html 时,服务器会返回以下响应头:

```yaml
HTTP/1.1 200 OK
Cache-Control: max-age=3600
Expires: Sat, 06 Jul 2024 11:41:00 GMT
Content-Type: text/html
```
这里的关键点是:
1. Cache-Control: max-age=3600 表示资源可以被缓存 3600 秒(1 小时)
2. Expires: Sat, 06 Jul 2024 11:41:00 GMT 表示资源在指定时间之前都可以被缓存
3. 优先使用max-age判断,如果没有到期,直接使用缓存
4. 如果没有max-age或者max-age过期,使用Expires判断,没有超过Expires时间,使用缓存资源

## 协商缓存
- 协商缓存是一种客户端和服务器共同参与的缓存机制。
- 当客户端再次请求资源时,会向服务器发送条件请求(如 If-Modified-Since、If-None-Match 头),服务器根据请求头验证资源是否已经发生变化,如果未变化则返回 304 Not Modified 状态码

### Last-Modified缓存

假设用户第一次访问一个图片资源 image.jpg。服务器返回的响应头如下:
```yaml
HTTP/1.1 200 OK
Last-Modified: Fri, 05 Jul 2024 12:34:56 GMT
Cache-Control: no-cache
Content-Type: image/jpeg
```
这里有几个重要的点:

1. Last-Modified 头表示资源最后一次被修改的时间
2. Cache-Control: no-cache 表示资源不应该被强缓存,需要使用协商缓存

当用户再次访问 image.jpg 时,浏览器会在请求头中添加 If-Modified-Since 头,值为之前服务器返回的 Last-Modified 时间:

```yaml
GET /image.jpg HTTP/1.1
If-Modified-Since: Fri, 05 Jul 2024 12:34:56 GMT
```

服务器收到这个请求后,会检查资源自上次返回 Last-Modified 以来是否有修改。如果资源未修改,服务器会返回 304 Not Modified 响应:

```yaml
HTTP/1.1 304 Not Modified
Last-Modified: Fri, 05 Jul 2024 12:34:56 GMT
```
这样浏览器就可以直接使用本地缓存的资源,不需要再次下载。
如果资源确实发生了修改,服务器会返回 200 OK 响应,并在响应头中更新 Last-Modified 时间戳:
```yaml
HTTP/1.1 200 OK
Last-Modified: Sat, 06 Jul 2024 10:20:30 GMT
Cache-Control: no-cache
Content-Type: image/jpeg
```
这样浏览器就会使用最新版本的资源,并更新本地缓存

### ETag缓存
> Last-Modified并不能精准的判断资源是否发生变化,只能精确到秒级,如果资源在一秒内发生改变,Last-Modified不会改变,
> 服务器时间可能会出现不准确的问题,
> 使用Etag可以更精确的判断资源是否过期

1. 当服务器第一次返回资源时,会在响应头中包含 ETag 信息:

```yaml
HTTP/1.1 200 OK
ETag: "abc123def456"
Cache-Control: no-cache
Content-Type: image/jpeg
```
2. 当浏览器再次请求该资源时,会在请求头中包含 If-None-Match 头,值为之前服务器返回的 ETag:
```yaml
GET /image.jpg HTTP/1.1
If-None-Match: "abc123def456"
```
3. 服务器收到请求后,会根据 If-None-Match 头中的 ETag 值来判断资源是否发生变化:

- 如果资源未变化,服务器会返回 304 Not Modified 响应,不返回资源内容
- 如果资源发生了变化,服务器会生成新的 ETag 值,并返回 200 OK 响应,连同新的资源内容

4. 优先使用ETag资源,然后使用Last-Modified

# HTTPS 与 HTTP、HTTP2.0与 HTTP1.0的区别

## HTTPS vs HTTP

    HTTP (Hypertext Transfer Protocol): 是一种明文传输的网络协议,数据容易被窃取和篡改。
    HTTPS (Hypertext Transfer Protocol Secure): 是在 HTTP 的基础上加入了 SSL/TLS 层,提供了加密、认证和完整性保护,可以更安全地传输数据。
    主要区别:
    安全性: HTTPS 使用加密算法对数据进行加密,提高了数据传输的安全性。
    认证: HTTPS 通过数字证书验证服务器身份,确保连接的安全性。
    完整性: HTTPS 使用消息摘要算法验证数据在传输过程中是否被篡改。

## HTTP/1.0 vs HTTP/2.0

    HTTP/1.0: 是最初的 HTTP 协议版本,存在一些缺陷,如:
    每个 HTTP 请求都需要建立一个新的 TCP 连接,连接建立和断开过程耗时。
    无法复用连接,每次请求都需要重新建立连接。
    只支持文本传输,无法压缩数据。

    HTTP/2.0: 是 HTTP 协议的新版本,改善了上述缺陷:
    使用二进制格式传输数据,而不是 HTTP/1.x 的文本格式。
    支持多路复用,一个 TCP 连接可以处理多个请求。
    支持头部压缩,减少了传输数据的大小。
    支持服务器推送(Server Push),可以主动向客户端推送资源

    主要区别:
    传输格式: HTTP/1.0 使用文本格式,HTTP/2.0 使用二进制格式。
    连接方式: HTTP/1.0 需要建立多个 TCP 连接,HTTP/2.0 可以在一个 TCP 连接上处理多个请求。
    性能: HTTP/2.0 的性能更优,传输效率更高。


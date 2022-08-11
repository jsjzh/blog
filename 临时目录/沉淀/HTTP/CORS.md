# CORS 跨域资源共享

协议 域名 端口，一个不同就是跨域。

XMLHttpRequest 和 Fetch API 发起的请求都遵循同源策略，其他的还有 Web 字体（CSS 中通过 @font-face 使用跨域字体资源），WebGL 贴图，使用 drawImage 将 Images/video 画面绘制到 canvas。

跨域资源共享标准通过新增了一组 HTTP headers，允许服务器声明哪些源站通过浏览器有权限访问哪些资源。

并且，对那些可能对服务器产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求，从而获知服务端是否允许该跨域请求，在预检请求的返回中，服务端可以通知客户端是否需要携带身份凭证（包括 Cookies 和 HTTP 认证相关数据）。

## 简单请求

> 该术语不属于 Fetch 规范  
> 也就是说，这个术语只属于 XMLHttpRequest?

若请求满足所有下述条件，则请求可视为简单请求

- method 为下列方法之一
  - `GET`
  - `POST`
  - `HEAD`
- 除了被用户代理自动设置的首部字段（`Connection User-Agent`）和在 `Fetch` 规范中定义为禁用首部名称的其他首部，允许人为设置的字段为 `Fetch` 规范定义的对 CORS 安全的首部字段集合
  - `Accept`
  - `Accept-Language`
  - `Content-Language`
  - `Content-Type`
    - `text/plain`
    - `multipart/form-data`
    - `application/x-www-form-urlencoded`
  - `DPR`
  - `Downlink`
  - `Save-Data`
  - `Viewport-Width`
  - `Width`
- 请求中的任意 `XMLHttpRequestUpload` 对象均没有任何事件监听器
  - `XMLHttpRequestUpload` 对象可以使用 `XMLHttpRequest.upload` 属性访问
- 请求中没有使用 `ReadableStream` 对象

```
* GET /resources/public-data/ HTTP/1.1
* Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
* Origin: http://foo.example


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[XML Data]
```

## 预检请求

和简单请求不同，预检请求必须首先使用 `OPTIONS` 方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。

如下为一个预检请求的 `OPTIONS`

```
* OPTIONS /resources/post-here/ HTTP/1.1
* Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
* Origin: http://foo.example
* Access-Control-Request-Method: POST
* Access-Control-Request-Headers: X-PINGOTHER, Content-Type


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
* Access-Control-Allow-Origin: http://foo.example
* Access-Control-Allow-Methods: POST, GET, OPTIONS
* Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
* Access-Control-Max-Age: 86400
* Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

请求端发送了 `Access-Control-Request-Method、Access-Control-Request-Headers、Origin`，服务端经过验证，返回了 `Access-Control-Allow-Methods、Access-Control-Allow-Headers、Access-Control-Allow-Origin`，经过对比可以看出上下吻合。

`Access-Control-Max-Age` 表明该响应的有效时间为 86400 秒，也就是 24 小时，在有效时间内，浏览器无须为同一请求再次发起预检请求，**请注意，浏览器自身维护了一个最大有效时间，如果该首部字段的值超过了最大有效时间，该字段不会生效。**

```
* POST /resources/post-here/ HTTP/1.1
* Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
* X-PINGOTHER: pingpong
* Content-Type: text/xml; charset=UTF-8
Referer: http://foo.example/examples/preflightInvocation.html
Content-Length: 55
* Origin: http://foo.example
Pragma: no-cache
Cache-Control: no-cache

* <?xml version="1.0"?><person><name>Arun</name></person>


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:40 GMT
Server: Apache/2.0.61 (Unix)
* Access-Control-Allow-Origin: http://foo.example
* Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 235
Keep-Alive: timeout=2, max=99
Connection: Keep-Alive
Content-Type: text/plain

[Some GZIP'd payload]
```

### 预检请求与重定向

重定向：服务端返回的 `Location` 字段。

> 大多数浏览器不支持针对预检请求的重定向（服务端返回 Location 字段），如果一个预检请求发生了重定向，浏览器将报告错误，不过在后续的修订中废弃了这一要求

## 附带身份凭证的请求

> 一般而言，对于跨域 XMLHttpRequest 或 Fetch 请求，浏览器不会发送身份凭证信息，如果需要发送凭证，需要设置 XMLHttpRequest 的某个特殊标志位

```js
const request = new XMLHttpRequest();
const url = "http://www.foo.com/";
if (request) {
  request.open("GET", url, true);
  request.withCredentials = true;
  request.onreadystatechange = handler;
  request.send();
}
```

设置 `withCredentials = true`，从而向服务器发送 cookie，因为这是一个简单请求，所以浏览器不会发起预检请求，但是，**如果服务端的响应中未携带 `Access-Control-Allow-Credentials: true`**，浏览器将不会吧相应内容返回给请求的发送者，也就是会发生跨域错误。

```
* GET /resources/access-control-with-credentials/ HTTP/1.1
* Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Referer: http://foo.example/examples/credential.html
* Origin: http://foo.example
* Cookie: pageAccess=2


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2
* Access-Control-Allow-Origin: http://foo.example
* Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
* Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
* Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain


[text/plain payload]
```

### 附带身份凭证的请求与通配符

**对于附带了身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin: \*`。**

### 第三方 cookie

在 CORS 响应中设置的 cookie 适用一般性第三方 cookie 策略。如果用户设置其浏览器拒绝所有第三方 cookie，则如下情况 cookie 不会设置成功：页面在 `foo.example` 加载，`set-cookie` 是由 `bar.other` 服务端发送的。

如果 `set-cookie` 失败，将会抛出异常。

## CORS 请求首部字段

> 这些字段无需手动设置，当开发者使用 XMLHttpRequest 对象发起跨域请求时他们已经被设置就绪。

### Origin

```
Origin: <origin>
```

该字段表明预检请求嚯实际请求的源站。

> 有时候将该字段的值设置为空字符串是有用的，例如：当源站是一个 data URL 时。

在所有带有 `Access-Controll-Request-\*` 控制访问请求中，`Origin` 字段总是被发送。

### Access-Control-Request-Method

```
Access-Control-Request-Method: <method>
```

该字段用于预检请求，用于将实际请求里将要所使用的 HTTP 方法告诉服务器。

### Access-Control-Request-Headers

```
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```

用于预检请求，将实际请求所携带的首部字段告诉服务器。

## CORS 响应首部字段

### Access-Control-Allow-Origin

```
Access-Control-Allow-Origin: <origin> | *
```

对于不需要携带身份凭证的请求，服务器可以指定该字段为 \*。

**如果服务端制定了具体的域名而非 \*，那响应首部中的 `Vary` 字段的值必须包含 `Origin`，这将告诉客户端：服务器对不同的源站返回不同的内容。**

### Access-Control-Expose-Headers

```
Access-Control-Expose-Headers: X-Ny-Custom-Header, X-Another-Custom-Header
```

在跨域访问时，`XMLHttpRequest` 对象的 `getResponseHeader()` 方法只能拿到一些最基本的响应头，`Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma`，如果需要访问其他头，则需要服务端设置该字段。

### Access-Control-Max-Age

```
Access-Control-Max-Age: <delta-seconds>
```

该头部指定了预检请求能够被缓存多久，即对于同一个请求，浏览器需要间隔多久才需要再次发起预检请求。

### Access-Control-Allow-Credentials

```
Access-Control-Allow-Credentials: true
```

该头指定了当浏览器设置 `XMLHttpRequest = true` 时，是否允许浏览器读取 response 的内容。

### Access-Control-Allow-Methods

```
Access-Control-Allow-Methods: <method>[, <method>]*
```

用于预检请求的响应，指明了实际请求所允许使用的 HTTP 方法。

### Access-Control-Allow-Headers

```
Access-Control-Allow-Headers: <field-name>[, <field-name>]*
```

和 `Access-Control-Expose-Headers` 不同的是，**这个字段表示实际请求中可以携带的 header 字段。**

## 参考

- https://fetch.spec.whatwg.org/#forbidden-header-name
- https://fetch.spec.whatwg.org/#cors-preflight-fetch
- https://tools.ietf.org/html/rfc6265#section-5.3

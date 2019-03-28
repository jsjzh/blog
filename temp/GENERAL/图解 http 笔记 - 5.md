# HTTP 首部

请求报文首部信息

```
GET / HTTP/1.1
Host: github.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
If-None-Match: W/"67cce0b0f17024dc434b2e855bf26465"
```

响应报文首部信息

```
HTTP/1.1 200 OK
Date: Thu, 28 Mar 2019 01:16:25 GMT
Content-Type: text/html; charset=utf-8
Transfer-Encoding: chunked
Server: GitHub.com
Status: 200 OK
Vary: X-PJAX
ETag: W/"e5baa97c63817ec2e29ca188016aaf05"
Cache-Control: max-age=0, private, must-revalidate
X-Request-Id: 9cb54431-58f2-4c01-b457-029376ab27c1
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
X-Frame-Options: deny
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
Expect-CT: max-age=2592000, report-uri="https://api.github.com/_private/browser/errors"
Content-Security-Policy: default-src 'none'; base-uri 'self'; block-all-mixed-content; connect-src 'self' uploads.github.com www.githubstatus.com collector.githubapp.com api.github.com www.google-analytics.com github-cloud.s3.amazonaws.com github-production-repository-file-5c1aeb.s3.amazonaws.com github-production-upload-manifest-file-7fdce7.s3.amazonaws.com github-production-user-asset-6210df.s3.amazonaws.com wss://live.github.com; font-src github.githubassets.com; form-action 'self' github.com gist.github.com; frame-ancestors 'none'; frame-src render.githubusercontent.com; img-src 'self' data: github.githubassets.com identicons.github.com collector.githubapp.com github-cloud.s3.amazonaws.com *.githubusercontent.com customer-stories-feed.github.com; manifest-src 'self'; media-src 'none'; script-src github.githubassets.com; style-src 'unsafe-inline' github.githubassets.com
Content-Encoding: gzip
Vary: Accept-Encoding
X-GitHub-Request-Id: DA61:4396:12C099:1B4CE4:5C9C2067
```

## HTTP 首部字段

### HTTP 首部字段结构

HTTP 首部字段是由首部字段名和字段值构成，中间用冒号分隔

`Content-Type: text/html`

`Keep-Alive: timeout=15,max=100`

> 若 HTTP 首部字段重复了会如何
> 根据浏览器内部处理逻辑结果可能并不一致，有些浏览器会优先处理第一次出现的首部字段
> 而有些会优先处理最后出现的首部字段

@@ TODO 发现 github 的响应报文中有好几个 Set-Cookie 是不是有什么特殊含义

### 4 种 HTTP 首部字段类型

- 通用首部字段
  - 请求报文和响应报文两方都会使用的首部
- 请求首部字段
  - 从客户端向服务器端发送请求报文时使用的首部，补充了请求的附加内容、客户端信息、响应内容相关优先级等信息
- 响应首部字段
  - 从服务器向客户端返回响应报文时使用的首部，补充了相应的附加内容，也会要求客户端附加额外的内容信息
- 实体首部字段
  - 针对请求报文和响应报文的实体部分使用的首部，补充了资源内容更新时间等与实体有关的信息

### HTTP/1.1 首部字段一览

RFC2616 定义了 47 种首部字段

通用首部字段

| 首部字段名        | 说明                       |
| :---------------- | :------------------------- |
| Cache-Control     | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理       |
| Date              | 创建报文的日期时间         |
| Pragma            | 报文指令                   |
| Trailer           | 报文末端的首部一览         |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Upgrade           | 升级为其他协议             |
| Via               | 代理服务器的相关信息       |
| Warning           | 错误通知                   |

请求首部字段

| 首部字段名          | 说明                                          |
| :------------------ | :-------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                      |
| Accept-Charset      | 优先的字符集                                  |
| Accept-Encoding     | 优先的内容编码                                |
| Accept-Language     | 优先的语言（自然语言）                        |
| Authorization       | Web 认证信息                                  |
| Expect              | 期待服务器的特定行为                          |
| From                | 用户的电子邮箱地址                            |
| Host                | 请求资源所在服务器                            |
| If-Match            | 比较实体标记（ETag）                          |
| If-Modified-Since   | 比较资源的更新时间                            |
| If-None-Match       | 比较实体标记（与 If-Match 相反）              |
| If-Range            | 资源未更新时发送实体 Byte 的范围请求          |
| If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反） |
| Max-Forwards        | 最大传输逐跳数                                |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                |
| Range               | 实体的字节范围请求                            |
| Referer             | 对请求中 URI 的原始获取方                     |
| TE                  | 传输编码的优先级                              |
| User-Agent HTTP     | 客户端程序的信息                              |

响应首部字段

| 首部字段名         | 说明                         |
| :----------------- | :--------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过时间         |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定 URI     |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP 服务器的安装信息        |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

实体首部字段

| 首部字段名       | 说明                         |
| :--------------- | :--------------------------- |
| Allow            | 资源可支持的 HTTP 方法       |
| Content-Encoding | 实体主体适用的编码方式       |
| Content-Language | 实体主体的自然语言           |
| Content-Length   | 实体主体的大小（单位：字节） |
| Content-Location | 替代对应资源的 URI           |
| Content-MD5      | 实体主体的报文摘要           |
| Content-Range    | 实体主体的位置范围           |
| Content-Type     | 实体主体的媒体类型           |
| Expires          | 实体主体过期的日期时间       |
| Last-Modified    | 资源的最后修改日期时间       |

### 非 HTTP/1.1 首部字段

除了 RFC2616 中定义的 47 种首部字段外，还有 Cookie、Set-Cookie 和 Content-Disposition 等在其他 RFC 中定义的首部字段使用频率也很高

### End-to-end 首部和 Hop-by-hop 首部

HTTP 首部将定义成缓存代理和非缓存代理的行为，分成 2 种类型

端到端首部

分在此类别中的首部会转发给请求/响应对应的最终接受目标，且必须保存在由缓存生成的相应中，另外规定它必须被转发

逐跳首部

分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发

HTTP/1.1 和之后版本中，如果要使用 hop-by-hop 首部，需要提供 Connection 首部字段

逐条首部字段，除了下列，其他都属于端到端首部

- Connection
- Keep-Alive
- Proxy-Authenticate
- Proxy-Authorization
- Trailer
- TE
- Transfer-Encoding
- Upgrade

## HTTP/1.1 通用首部字段

即请求和响应报文双方都会使用的首部

### Cache-Control

`Cache-Control: private, max-age=0, no-cache`

Cache-Control 请求指令

| 指令                | 参数   | 说明                         |
| :------------------ | :----- | :--------------------------- |
| no-cache            | 无     | 强制向源服务器再次验证       |
| no-store            | 无     | 不缓存请求或响应的任何内容   |
| max-age = [ 秒]     | 必需   | 响应的最大 Age 值            |
| max-stale( = [ 秒]) | 可省略 | 接收已过期的响应             |
| min-fresh = [ 秒]   | 必需   | 期望在指定时间内的响应仍有效 |
| no-transform        | 无     | 代理不可更改媒体类型         |
| only-if-cached      | 无     | 从缓存获取资源               |
| cache-extension     | -      | 新指令标记（token）          |

Cache-Control 响应指令

| 指令             | 参数   | 说明                                           |
| :--------------- | :----- | :--------------------------------------------- |
| public           | 无     | 可向任意方提供响应的缓存                       |
| private          | 可省略 | 仅向特定用户返回响应                           |
| no-cache         | 可省略 | 缓存前必须先确认其有效性                       |
| no-store         | 无     | 不缓存请求或响应的任何内容                     |
| no-transform     | 无     | 代理不可更改媒体类型                           |
| must-revalidate  | 无     | 可缓存但必须再向源服务器进行确认               |
| proxy-revalidate | 无     | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| max-age = [ 秒]  | 必需   | 响应的最大 Age 值                              |
| s-maxage = [ 秒] | 必需   | 公共缓存服务器响应的最大Age值                  |
| cache-extension  | -      | 新指令标记（token）                            |

表示是否能缓存的指令

#### public 指令

`Cache-Control: public`

当指定使用 public 指令时，则明确表明其他用户也可利用缓存

`Cache-Control: private`

当指定 private 指令后，响应只能让特定的用户缓存

缓存服务器会对该特定用户提供资源缓存的服务，对于其他用户发送的请求不会缓存

#### no-cache 指令

`Cache-Control: no-cache`

使用 no-cache 指令的目的是**为了防止从缓存中返回过期的资源**

客户端发送请求首部若包含 no-cache 指令，表示客户端不会接收缓存过的响应，缓存服务器必须把请求转发给源服务器

服务器返回的响应首部若包含 no-cache 指令，表示缓存服务器不能对资源进行缓存，且缓存服务器若提出要确认该资源的有效性，源服务器不响应

`Cache-Control: no-cache=Location`

由服务器返回的响应中，若 no-cache 有具体参数值，客户端就不会对该资源进行缓存，无参数值的首部字段资源可以缓存

这只能在响应指令中指定该参数


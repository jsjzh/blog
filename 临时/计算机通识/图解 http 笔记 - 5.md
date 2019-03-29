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

由服务器返回的响应中，若 no-cache 有具体参数值，客户端就不会对该资源进行缓存，无参数值的首部字段资源可以缓存，且这只能在响应指令中指定该参数

控制可执行缓存的对象的指令

#### no-store 指令

`Cache-Control: no-store`

> 从字面意思上理解 no-cache 很容易理解成不缓存，其实 no-cache 掉膘不缓存过期的资源，缓存会向源服务器进行有效期确认后处理资源
> no-store 才是不进行缓存

该指令规定缓存不能在本地存储请求或响应的任一部分

#### s-maxage 指令

`Cache-Control: s-maxage=604800`（单位：秒）

s-maxage 指令的功能和 max-age 指令的相同，它们的不同点是 s-maxage 指令只适用于供多为用户使用的公共缓存服务器（这里一般指代理）

另外，当使用 s-maxage 指令后，直接忽略对 Expires 首部字段及 max-age 指令的处理

#### max-age 指令

![max-age](./img/max-age.png)

`Cache-Control: max-age=604800`（单位：秒）

当客户端发送的请求中包含 max-age 指令时，如果判定缓存资源的缓存时间数值比指定时间的数值更小，那么客户端就接受缓存的资源

另外，当指定 max-age 的值为 0 时，缓存服务器通常将请求转发给源服务器

当服务器返回的响应中包含 max-age 指令时，缓存服务器将不对资源的有效性再做确认，而 max-age 数值代表资源保存为缓存的最长时间

应用 HTTP/1.1 版本的缓存服务器若遇到 Expires 和 max-age 同时存在，会优先处理 max-age 而忽略 Expires 首部字段，但是 HTTP/1.0 正好相反

#### min-fresh 指令

`Cache-Control: min-fresh=60`（单位：秒）

min-fresh 指令要求缓存服务器返回至少还未过指定时间的缓存资源

比如当指定 min-fresh 为 60 秒后，过了 60 秒的资源都无法作为响应返回了

#### max-stale 指令

`Cache-Control: max-stale=3600`（单位：秒）

使用 max-stale 可指示缓存资源，即使过期也照常接收

如果指令未指定参数值，那么无论经过多久客户端都会接收响应

如果指令中制定了具体数值，那么即使过期，只要仍处于 max-stale 指定的时间内，仍旧会被客户端接收

#### only-if-cached 指令

`Cache-Control: only-if-cached`

使用 only-if-cached 指令表示客户端仅在缓存服务器本地缓存目标资源的情况下才会要求返回

换言之，该指令要求缓存服务器不重新加载响应，也不会再次确认资源有效性，若发生请求缓存服务器的本地缓存无响应，则返回状态码 504 Gateway Timeout

#### must-revalidate 指令

`Cache-Control: must-revalidate`

使用 must-revalidate 指令，代理会像源服务器再次验证即将返回的响应缓存目前是否仍然有效

若代理无法连通源服务器再次获取有效资源的话，缓存必须给客户端一条 504 Gateway Timeout 状态码

另外，使用 must-revalidate 会忽略请求的 max-stale 指令

#### proxy-revalidate 指令

`Cache-Control: proxy-revalidate`

proxy-revalidate 指令要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前，必须再次验证缓存的有效性

#### no-transfrom 指令

`Cache-Control: no-transform`

使用 no-transform 指令规定无论是在请求还是响应中，缓存都不能改变实体主体的媒体类型

这样做可防止缓存或代理压缩图片等类似操作

Cache-Control 扩展

`cache-extension token`

`Cache-Control: private, commuity="UCI"`

通过 cache-extension 标记（token），可以扩展 Cache-Control 首部字段内的指令

如果缓存服务器不能理解 community 指令就会直接忽略

### Connection

Connection 首部字段具备如下两个作用

- 控制不再转发给代理的首部字段
  - ![Connection不再转发](./img/Connection不再转发.png)
  - 在客户端发送请求和服务器返回响应内，使用 Connection 首部字段，可控制不再转发给代理的首部字段
- 管理持久连接
  - ![Connection-close](./img/Connection-close.png)
  - `Connection: close`
  - ![Connection-keep-alive](./img/Connection-keep-alive.png)
  - HTTP/1.1 版本的默认连接都是持久连接，为此，客户端会在持久连接上连续发送请求
  - 当服务器想明确断开连接时，则指定 Connection 首部字段的值为 Close
  - HTTP/1.1 之前的版本默认是非持久连接，若想要维持持续连接，需要指定 Connection 值为 Keep-Alive

### Date

表明创建 HTTP 报文的日期和时间

### Pragma

Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与 HTTP/1.0 的向后兼容而定义

`Pragma: no-cache`

该首部字段属于通用首部字段，但只用在客户端发送的请求中，客户端会要求所有的中间服务器不返回缓存的资源

为了兼容性，发送的请求会同时还有两个首部字段

```
Cache-Control: no-cache
Pragma: no-cache
```

### Trailer

首部字段 Trailer 会事先说明在报文主体后记录了那些首部字段，该首部字段可应用在 HTTP/1.1 版本分块传输编码时

```
HTTP/1.1 200 OK
Date: Tue, 03 Jul 2012 04:40:56 GMT
Content-Type: text/html
Trailer: Expires

...
报文主体
...
0
Expires: Tue, 28 Sep 2004 23:59:59 GMT
```

以上用例中，指定首部字段 Trailer 的值为 Expires，在报文主体之后（分块长度 0 之后）出现了首部字段 Expires

### Transfer-Encoding

Transfer-Encoding 规定了传输报文主体时采用的编码方式

HTTP/1.1 的传输编码方式仅对分块传输编码有效

```
HTTP/1.1 200 OK
Date: Tue, 03 Jul 2012 04:40:56 GMT
Transfer-Encoding: chunked

cf0 ←16进制(10进制为3312)
...3312字节分块数据...
392 ←16进制(10进制为914)
...914字节分块数据...
0
```

以上用例中，正如首部字段 Transfer-Encoding 中指定的那样，有效使用分块传输编码，且被分成 3312 字节和 914 字节大小的分块数据

### Upgrade

首部字段 Upgrade 用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议

![Upgrade](./img/Upgrade.png)

上图用例中，首部字段 Upgrade 指定的值为 TLS/1.0

Upgrade 首部字段产生作用的 Upgrade 对象仅限于客户端和邻接服务器之间，因此，使用 Upgrade 时还需要额外指定 Connection: Upgrade

对于附有首部字段 Upgrade 的请求，服务器可用 101 Switching Protocols 状态码作为响应返回

### Via

使用 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径

报文经过代理或网关时，会先在首部 Via 中附加该服务器的信息再进行转发

Via 不仅用于追踪报文的转发，还可避免请求回环的发生

![Via](./img/Via.png)

Via 首部是为了追踪传输路径，所以经常和 TRACE 一起使用

比如代理服务器接收到由 TRACE 方法发送过来的请求（其中 Max-Forwards: 0）时，代理服务器就不能再转发该请求了

这种情况下，代理服务器会将自身的信息附加到 Via 首部后返回该请求的响应

### Warning

HTTP/1.1 的 Warning 首部是从 HTTP/1.0 的响应首部（ Retry-After）演变过来的，该首部通常会告知用户一些与缓存相关的问题的警告

`Warning: 警告码 警告的主机:端口号 "警告内容" 日期时间`

HTTP/1.1 警告码

| 警告码 | 警告内容                                         | 说明                                                           |
| :----- | :----------------------------------------------- | :------------------------------------------------------------- |
| 110    | Response is stale（响应已过期）                  | 代理返回已过期的资源                                           |
| 111    | Revalidation failed（再验证失败）                | 代理再验证资源有效性时失败（服务器无法到达等原因）             |
| 112    | Disconnection operation（断开连接操作）          | 代理与互联网连接被故意切断                                     |
| 113    | Heuristic expiration（试探性过期）               | 响应的使用期超过24小时（有效缓存的设定时间大于24小时的情况下） |
| 199    | Miscellaneous warning（杂项警告）                | 任意的警告内容                                                 |
| 214    | Transformation applied（使用了转换）             | 代理对内容编码或媒体类型等执行了某些处理时                     |
| 299    | Miscellaneous persistent warning（持久杂项警告） | 任意的警告内容                                                 |

## 请求首部字段

请求首部字段是从客户端往服务器端发送请求报文中所使用的字段，用于补充请求的附加信息、客户端信息、对相应内容相关的优先级等内容

### Accept

`Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`

Accept 可通知服务器用户代理能够处理的媒体类型及媒体类型的相对优先级

- 文本文件
  - text/html,text/plain,text/css ...
  - application/xhtml+xml,application/xml ...
- 图片文件
  - image/jpeg,image/gif,image/png ...
- 视频文件
  - video/mpeg,video/quicktime ...
- 应用程序食用的二进制文件
  - application/octet-stream,application/zip ...

若想要增加优先级，使用 q= 来额外表示权重值，用 ; 进行分隔

### Accept-Charset

`Accept-Charset: iso-8859-5, unicode-1-1;q=0.8`

Accept-Charset 可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序

### Accept-Encoding

`Accept-Encoding: gzip, deflate`

Accept-Encoding 用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序

也可以使用 * 作为通配符，指定任意的编码格式

### Accept-Language

`Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3`

Accept-Language 用来告知服务器用户代理能够处理的自然语言集

### Authorization

`Authorization: Basic dWVub3NlbjpwYXNzd29yZA==`

Authorization 用来告知服务器，用户代理的认证信息（证书值）

### Expect

`Expect: 100-continue`

客户端使用 Expect 来告知服务器希望出现的某种特定行为，因为服务器无法理解客户端的期望作出回应而发生错误时，会返回状态码 417 Expectiation Failed

### From

From 用来告知服务器使用用户代理的用户的电子邮件地址

### Host

`Host: www.github.com`

当虚拟主机运行在同一个 IP 上，因此使用 Host 加以区分

Host 会告知服务器请求的资源所处的互联网主机名和端口号，**Host 是唯一一个必须被包含在请求内的首部字段**

若服务器未设定主机名，那直接发送一个空值即可

`Host: `

### If-Match

`If-Match: "123456"`

形如 If-xxx 这种形式的请求首部字段都可称为条件请求

![If-Match](./img/If-Match.png)

If-Match，他会告知服务器匹配资源所用的实体标记（ETag）值，这时的服务器无法使用弱 ETag 值

服务器会对比 If-Match 的字段值和资源的 ETag 值，仅当两者一致时才会执行请求，反之则返回状态码 412 Precondition Failed 的响应

还可以使用 * 制定 If-Match 字段值，这种情况服务器会忽略 ETag 的值，只要资源存在就处理请求

### If-Modified-Since

`If-Modified-Since: Thu, 15 Apr 2004 00:00:00 GMT`

![If-Modified-Since](./img/If-Modified-Since.png)

If-Modified-Since，他会告知服务器若该首部字段的字段值早于资源的更新时间，则希望服务端能处理该请求

而在指定 If-Modified-Since 字段值的日期之后，如果请求的资源都没有更新过，则返回状态码 304 Not Modified

### If-None-Match

![If-None-Match](./img/If-None-Match.png)

If-None-Match 和 If-Match 作用相反，用于指定该字段值的实体标记（ETag）值与资源的 ETag 不一致时，他就告知服务器处理该请求

在 GET 或 HEAD 中使用 If-None-Match 可获取最新的资源

### If-Range

![If-Range](./img/If-Range.png)

If-Range 告知服务器若指定的 If-Range 字段值（ETag 值或者时间）和请求资源的 ETag 值或时间相一致时，则作为范围请求处理，反之则返回全体资源

![no_If-Range](./img/no_If-Range.png)

若不使用 If-Range 发送请求，服务器端的资源如果更新，那客户端持有资源中的一部分也会随之无效，那范围请求作为前提也是无效的，这时，服务器会暂且以状态码 412 Precondition Failed 作为响应返回，目的时催促客户端再次发送请求，这样和 If-Range 比起来就要花费两倍时间

### If-Unmodified-Since

`If-Unmodified-Since: Thu, 03 Jul 2012 00:00:00 GMT`

If-Unmodified-Since 和 If-Modified-Since 相反，他的作用是告知服务器，制定的请求只有在字段值内指定的时间之后，未发生更新的情况下，才需要处理请求，如果在指定日期时间后发生了更新，返回状态码 412 Precondition Failed

### Max-Forwards

![Max-Forwards](./img/Max-Forwards.png)

`Max-Forwards: 10`

通过 TRACE 方法或 OPTIONS 方法，发送包含首部字段 Max-Forwards 的请求时，该字段表示可经过的服务器最大数，服务器每往下一个服务器转发请求之前，Max-Forwards 都会减 1，直到为 0，不进行转发，直接返回响应

使用 HTTP 协议通信时，请求可能会经过代理等多台服务器，途中，如果代理服务器由于某些原因导致请求转发失败，客户端也就等不到服务器返回的响应了

### Proxy-Authorization

`Proxy-Authorization: Basic dGlwOjkpNLAGfFY5`

接收到从代理服务器发来的认证质询时，客户端会发送包含 Proxy-Authorization 的请求，以告知服务器认证所需要的信息

### Range

`Range: bytes=5001-10000`

对于只需获取部分资源的范围请求，包含首部字段 Range 即可告知服务器资源的指定范围

服务器处理之后会返回 206 Partial Content 的响应，无法处理该范围请求时，返回状态码 200 OK 的响应和全部资源

### Referer

`Referer: https://github.com`

Referer 告知服务器请求的原始资源的 URI，点击网页上某一链接之后，会带上该字段

直接在地址栏输入 URI 时，或出于安全性考虑会不发送该首部字段

因为原始资源的 URI 中的查询字符串可能含有重要信息，要是转发给其他服务器会导致信息泄露

> Referer 的正确拼写时 Referrer

### TE

`TE: gzip, deflate;q=0.5`

TE 会告知服务器客户端能够处理相应的编码方式以及相对优先级

### User-Agent

`User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36`

User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器

## 响应首部字段

响应首部字段是由服务器端向客户端返回响应报文中所使用的字段，用于补充响应的附加信息、服务器信息，以及对客户端的附加要求等

### Accept-Ranges

`Accept-Ranges: bytes`

Accept-Ranges 用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源

可指定的字段值有两种，可处理范围请求时指定其为 bytes，反之为 none

### Age

`Age: 600`（单位：秒）

Age 能告知客户端源服务器在多久前创建了响应

若创建该相应的服务器时缓存服务器，Age 值是指缓存后的响应再次发起认证到认证完成的时间值
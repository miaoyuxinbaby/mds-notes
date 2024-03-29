# HTTP 基础概念

TCP/IP 协议族分四层

- 应用层: 应用层决定了向用户提供应用服务时通信的活动
  - FTP(文件传输协议)、DNS(域名系统)、HTTP 就是其中的 3 种
- 传输层: 传输层对上层应用层， 提供处于网络连接中的两台计算机之间的数据传输
  - TCP(传输控制协议),UDP(用户数据报协议)
- 网络层: 网络层用来处理在网络上流动的数据包
  - 数据包是网络传输的最小数据单位。 该层规定了通过怎样的路径（所谓的传输路线） 到达对方计算机， 并把数据包传送给对方
- 链路层: 用来处理连接网络的硬件部分

URI 包含 URL 和 URN，目前 WEB 只有 URL 比较流行，所以见到的基本都是 URL。

- URI（Uniform Resource Identifier，统一资源标识符）
- URL（Uniform Resource Locator，统一资源定位符）
- URN（Uniform Resource Name，统一资源名称）

请求和响应报文 都是分为头和 body，中间以一个空行分隔

## 特点

1. 灵活 HTTP 允许传输任意类型的数据对象
2. 无状态

## 二、HTTP 方法

客户端发送的 **请求报文** 第一行为请求行，包含了方法字段。

## GET

> 获取资源

当前网络请求中，绝大部分使用的是 GET 方法。

## HEAD

> 获取报文首部

和 GET 方法一样，但是不返回报文实体主体部分。

主要用于确认 URL 的有效性以及资源更新的日期时间等。

## POST

> 传输实体主体

POST 主要用来传输数据，而 GET 主要用来获取资源。

## PUT

> 上传文件

由于自身不带验证机制，任何人都可以上传文件，因此存在安全性问题，一般不使用该方法。

```html
PUT /new.html HTTP/1.1 Host: example.com Content-type: text/html Content-length:
16

<p>New File</p>
```

## PATCH

> 对资源进行部分修改

PUT 也可以用于修改资源，但是只能完全替代原始资源，PATCH 允许部分修改。

```html
PATCH /file.txt HTTP/1.1 Host: www.example.com Content-Type: application/example
If-Match: "e0023aa4e" Content-Length: 100 [description of changes]
```

## DELETE

> 删除文件

与 PUT 功能相反，并且同样不带验证机制。

```html
DELETE /file.html HTTP/1.1
```

## OPTIONS

> 查询支持的方法

查询指定的 URL 能够支持的方法。

会返回 Allow: GET, POST, HEAD, OPTIONS 这样的内容。

## 三、HTTP 状态码

| 状态码 |               类别               |          原因短语          |
| :----: | :------------------------------: | :------------------------: |
|  1XX   |  Informational（信息性状态码）   |     接收的请求正在处理     |
|  2XX   |      Success（成功状态码）       |      请求正常处理完毕      |
|  3XX   |   Redirection（重定向状态码）    | 需要进行附加操作以完成请求 |
|  4XX   | Client Error（客户端错误状态码） |     服务器无法处理请求     |
|  5XX   | Server Error（服务器错误状态码） |     服务器处理请求出错     |

## 1XX 信息

- **100 Continue** ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应。

## 2XX 成功

- **200 OK**

- **204 No Content** ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用。

- **206 Partial Content** ：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容。

## 3XX 重定向

- **301 Moved Permanently** ：永久性重定向

- **302 Found** ：临时性重定向

- **303 See Other** ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。

- 注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。

- **304 Not Modified** ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。

- **307 Temporary Redirect** ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。

## 4XX 客户端错误

- **400 Bad Request** ：请求报文中存在语法错误。

- **401 Unauthorized** ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。

- **403 Forbidden** ：请求被拒绝。

- **404 Not Found**

## 5XX 服务器错误

- **500 Internal Server Error** ：服务器正在执行请求时发生错误。

- **503 Service Unavailable** ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。

- **504 Gateway Time-out**: 网关超时

当浏览器访问一个包含多张图片的 HTML 页面时，除了请求访问 HTML 页面资源，还会请求图片资源。如果每进行一次 HTTP 通信就要新建一个 TCP 连接，那么开销会很大。

长连接只需要建立一次 TCP 连接就能进行多次 HTTP 通信。

从 HTTP/1.1 开始默认是长连接的

HTTP 是无状态的，可以通过 cookie 让服务器识别用户。 由响应报文的 set-cookie 字段设置
cookie 会字段带在 header 里发给后端，设置 httponly 可以拒绝 js 对 cookie 的操作（防止 XSS)

- 会话期 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。
- 持久性 Cookie：指定一个特定的过期时间（Expires）或有效期（max-age）之后就成为了持久性的 Cookie。

cookie 是有作用域的 Domain 标识设置

## 缓存

### 1. 优点

- 缓解服务器压力；
- 更快地获取资源：缓存通常位于内存中，读取速度快

### 2. 实现方法

- 让代理服务器进行缓存； cdn
- 让客户端浏览器进行缓存。

### 3. Cache-Control

HTTP/1.1 通过 Cache-Control 首部字段来控制缓存。

```txt
Cache-Control：
  no-store 禁止缓存
  no-cache 强制确认缓存，缓存服务器向源服务器确认资源的有效性
  private 和 public 私有和公有缓存
  max-age 过期时间
  expires 资源过期时间

  max-age 优先级大于expires
```

## 虚拟主机

HTTP/1.1 使用虚拟主机技术，使得一台服务器拥有多个域名，并且在逻辑上可以看成多个服务器。

### 1. 代理

代理服务器接受客户端的请求，并且转发给其它服务器。

使用代理的主要目的是：

- 缓存
- 负载均衡
- 网络访问控制
- 访问日志记录

代理服务器分为正向代理和反向代理两种：

- 用户察觉得到正向代理的存在。

- 而反向代理一般位于内部网络中，用户察觉不到。

## 六、HTTPs

HTTP 有以下安全性问题：

- 使用明文进行通信，内容可能会被窃听；
- 不验证通信方的身份，通信方的身份有可能遭遇伪装；
- 无法证明报文的完整性，报文有可能遭篡改。

HTTPs 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPs 使用了隧道进行通信。

通过使用 SSL，HTTPs 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）。

SSL(Secure Socket Layer)“安全套接层”协议，TLS(Transport Layer Security)“安全传输层”协议

HTTPS 在传输数据之前需要客户端（浏览器）与服务端（网站）之间进行一次握手，在握手过程中将确立双方加密传输数据的密码信息。

握手过程的简单描述如下：

浏览器将自己支持的一套加密规则发送给网站。

网站从中选出一组加密算法与 HASH 算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，加密公钥，以及证书的颁发机构等信息。

获得网站证书之后浏览器会 验证证书的合法性（浏览器那个小锁的图标）。如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密。

使用约定好的 HASH 计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。

网站接收浏览器发来的数据

使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证 HASH 是否与浏览器发来的一致。使用密码加密一段握手消息，发送给浏览器。

浏览器解密并计算握手消息的 HASH，如果与服务端发来的 HASH 一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密。

密码是随机数字符串，用随机数字符串 hash 握手信息，用公钥加密随机字符串。服务端解密出随机字符串后，解密握手信息

## get post 区别

```txt
GET在浏览器回退时是无害的，而POST会再次提交请求
GET请求会被浏览器主动缓存，而POST不会，除非手动设置
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留
GET请求在URL中传送的参数是有长度限制的，而POST没有限制
GET参数通过URL传递，POST放在Request body中
```

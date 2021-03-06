---
title: 'HTTP & HTTPS & TLS/SSL'
date: 2020-03-24 17:32:50
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
## OSI

![OSI模型](https://note.youdao.com/yws/api/personal/file/3E2AA26C69EA466D819AF0CEAFA08232?method=download&shareKey=890ceb7a1b8ed88eabba964a53289c23)

OSI 是用于描述网络通信的理论模型。 简单来说， 所有功能都被映射到七个层上。 最底层是最接近物理通信链路的层， 后面的层以次建立在其它层之上， 提供更高级别的抽象。 最顶层就是应用层， 携带应用数据。

OSI 模型的优点：

- 清晰的划分概念， 高层的协议不必担心在底层实现的功能。
- 不同层次的协议可以加入通信或者从通信中删除， 一种底层协议可以服务于多种上层协议。

## HTTP

http 处于 通信模型的应用层， 基于 TCP/IP ， 因为 TCP 协议能保证数据不丢失是一种可靠的协议， 而 UDP 则是不可靠的协议。

TCP/IP 协议你一定听过，TCP/IP 我们一般称之为协议簇，什么意思呢？就是 TCP/IP 协议簇中不仅仅只有 TCP 协议和 IP 协议，它是一系列网络通信协议的统称。而其中最核心的两个协议就是 TCP / IP 协议，其他的还有 UDP、ICMP、ARP 等等，共同构成了一个复杂但有层次的协议栈。

IP 协议的全称是 Internet Protocol 的缩写，它主要解决的是通信双方寻址的问题。IP 协议使用 IP 地址 来标识互联网上的每一台计算机。

DNS 的全称是域名系统（Domain Name System，缩写：DNS），它作为将域名和 IP 地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。

### HTTP 的影响因素

影响一个 http 请求的主要因素有两个： 带宽、 延迟。 带宽收到客户端环境因素影响， 所以主要优化的方向是延迟， 而影响延迟的主要有下面三点。

- 浏览器阻塞（HOL blocking）， 浏览器 http 请求是有并发限制的， 正常同个域名下是 4 个， 根据浏览器不同限制也不一样。
- DNS 查询 IP 也需要时间， 一般浏览器都会有 DNS 缓存来优化查询时间。
- 建立连接 ， HTTP 传输层使用的是 TCP ， 需要经过三次握手之后才能建立连接， 但是这些连接无法被复用， 导致每次请求都要重新建立连接。

#### HTTP 1.0 与 1.1 的区别

[参考](https://mp.weixin.qq.com/s/GICbiyJpINrHZ41u_4zT-A?)

两者主要区别体现在一下几点：

|             | http 1.0                                                   | http 1.1                                                                                                 |
| ----------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| 缓存处理    | header 中以 If-Modified-Since， Expires 做判断标准         | 引入了更多的缓存策略如 etag，If-None-Match                                                               |
| 状态码      |                                                            | 新增了 206（只请求资源的某个部分，对应 header 中的 range）； 新增了 409（conflict） 等错误状态码         |
| Host 头处理 | 认为每台服务器都绑定唯一一个 IP 所以请求头中没有带 host 头 | 随着虚拟机的发展，一个物理机可能对应多个虚拟主机，他们公用同一个 IP ， 请求头中必须带上 host             |
| 长连接      | 每次请求都要创建连接                                       | 支持长连接， 在一个 TCP 连接上可以传送多个 HTTP 请求和响应（对应 keep-alive）， 减少连接的建立及关闭消耗 |


#### HTTP 和 HTTPS 的区别
- HTTPS 的表示层中使用了 TLS 协议， 传输的内容都经过加密。
- HTTPS 的默认端口号为 443 ， HTTP 的则是 80。

#### SPDY  HTTP 1.X 的优化
SPDY 方案又花了 HTTP 1.x 的请求延时， 具体如下：
- 降低延时， SPDY 采用多路复用多个请求 stream 共享一个 tcp 连接的方式， 解决了浏览器阻塞问题。
- 请求优先级， SPDY 允许给每个 request 设置优先级， 优先相应重要的请求。
- header 压缩， 选择合适的算法可以减少包的大小。
- 基于 HTTPS 的加密传输协议
- 服务端推送， 采用了SPDY的网页，例如我的网页有一个sytle.css的请求，在客户端收到sytle.css数据的同时，服务端会将sytle.js的文件推送给客户端，当客户端再次尝试获取sytle.js时就可以直接从缓存中获取到，不用再发请求了。

SPDY 构件图如下：
![](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribjhshzcKo97UNNVIFgpOYZic95drsxo5TaiadPSSmcYhOI7GYAO99W6Sw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


SPDY位于HTTP之下，TCP和SSL之上，这样可以轻松兼容老版本的HTTP协议(将HTTP1.x的内容封装成一种新的frame格式)，同时可以使用已有的SSL功能。

#### HTTP2.0: SPDY 的升级版
HTTP2.0可以说是SPDY的升级版（其实原本也是基于SPDY设计的），但是，HTTP2.0 跟 SPDY 仍有不同的地方，如下：
1. HTTP2.0 支持明文 HTTP 传输，而 SPDY 强制使用 HTTPS。
2. HTTP2.0 消息头的压缩算法采用 HPACK 而非 SPDY 采用的 DEFLATE

#### HTTP2.0和 HTTP1.X 相比的新特性

- HTTP2.0 采用二进制格式， HTTP1.x 的解析是基于文本。
- HTTP2.0 采用多路复用， 多个请求可并行在一个连接上执行， 而 HTTP 1.1 中的长连接一个连接每次只能处理一个请求，无法并行。
- HTTP2.0 有 header 压缩
- HTTP2.0 有服务端推送



## TLS/SSL

SSL 是由 Netscape 公司开发的协议， TLS1.0 基于 SSL3 进行了一些修改并改了协议名为 TLS 。


## 面试解答

> 应用层 => 影响 http 因素 => http 1.0 & 1.1 & 2.0 各种优化及区别 => HTTPS

HTTP 协议基于 TCP/IP 处于通信模型的应用层， 影响一个 http 请求的主要因素有两个： 带宽和延迟。 而其中 HTTP 协议的主要优化在于延迟， 影响延迟的也有三个主要因素：
- 浏览器阻塞（http 请求的并发限制）
- DNS 域名解析
- TCP 连接

在 1.1 版本中通过长连接复用 TCP 连接， 在 2.0 中通过多路复用 TCP 可以并行处理 http 请求优化延迟。  
1.1 中还引入了 etag 、 If-None-Match 的缓存策略， 新增了 206 、409 等状态码， 请求头中必带 host 字段。

HTTPS 在通信模型的表示层应用了 TLS/SSL 协议， 对传输内容进行了加密。

对于 DNS 解析的优化， 浏览器本身就带有 dns 缓存数据， 对于需要预解析的域名可以使用 dns-prefetch 。

### https 握手过程

1. 客户端向服务器发送支持的SSL/TSL的协议版本号，以及客户端支持的加密方法，和一个客户端生成的随机数
2. 服务器确认协议版本和加密方法，向客户端发送一个由服务器生成的随机数，以及数字证书
3. 客户端验证证书是否有效，有效则从证书中取出公钥，生成一个随机数，然后用公钥加密这个随机数，发给服务器
4. 服务器用私钥解密，获取发来的随机数
5. 客户端和服务器根据约定好的加密方法，使用前面生成的三个随机数，生成对话密钥，用来加密接下来的整个对话过程


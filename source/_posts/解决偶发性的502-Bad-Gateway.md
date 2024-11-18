---
title: 解决偶发性的502 Bad Gateway
date: 2024-11-15 09:45:31
tags:
- HttpClient
- 502 Bad Gateway
categories: HttpClient
---

**现象：**

我们在对一些接口进行压测时，会偶发性的出现**502 Bad Gateway** 的报错，大约是2亿笔交易中会有2-3笔出现**502 Bad Gateway** 的报错。

**HTTP** 数据流向如下：

```
[前置服务] --- [Ingress] --- [网关] --- [聚合服务] --- [基础服务]
```

我们最开始怀疑这个错误是**ingress**爆出来的，怀疑是**ingress**导致的，然后我们就把**ingress**去掉，继续压测试试，此时的**HTTP**数据流如下：

```
[前置服务] --- [网关] --- [聚合服务] --- [基础服务]
```

此时，**502 Bad Gateway** 的报错虽然没有出现，但是会偶发性的出现**NoHttpResponseException**的报错，大搞也是2亿笔出现3-4笔的**NoHttpResponseException**的报错。然后我们就怀疑**502 Bad Gateway** 报错和**NoHttpResponseException**报错本质上是一样的。

**NoHttpResponseException**错误详情：

```java
org.apache.http.NoHttpResponseException: target failed to respond
  at org.apache.http.impl.conn.DefaultHttpResponseParser.parseHead(DefaultHttpResponseParser.java:141)
  at org.apache.http.impl.conn.DefaultHttpResponseParser.parseHead(DefaultHttpResponseParser.java:56)
  at org.apache.http.impl.io.AbstractMessageParser.parse(AbstractMessageParser.java:259)
  at org.apache.http.impl.DefaultBHttpClientConnection.receiveResponseHeader(DefaultBHttpClientConnection.java:163)
  at org.apache.http.impl.conn.CPoolProxy.receiveResponseHeader(CPoolProxy.java:157)
  at org.apache.http.protocol.HttpRequestExecutor.doReceiveResponse(HttpRequestExecutor.java:273)
  at org.apache.http.protocol.HttpRequestExecutor.execute(HttpRequestExecutor.java:125)
  at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:272)
  at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:186)
  at org.apache.http.impl.execchain.RetryExec.execute(RetryExec.java:89)
  at org.apache.http.impl.execchain.RedirectExec.execute(RedirectExec.java:110)
  at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:185)
  at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83)
```

**分析：**

看一下报错的源代码，相关注释提示服务端主动关闭了连接导致的：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/nohttpresonseexception.png)

通过**WireShark**抓包，我们可以看到的确是服务端已经发送了`FIN`,而客户端依旧使用了这个链接进行访问，所以就收到了`RST`，造成整个连接失败。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/tcpdump.png)

遇到问题，首先打开**Debug**日遇到问题，首先打开**Debug**日志，我们可以看到这是因为尝试读数据是没读到，所以链接失败了。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/endofstream.png)

- 查阅**TCP**资料，我们可以知道一般读到**end of stream**都是因为服务端主动关闭连接造成的，此时客户端应该主动关闭连接，而不是继续复用。
- 通过网络抓包，我们可以看到的确是服务端已经发送了`FIN`,而客户端依旧使用了这个链接进行访问，所以就收到了`RST`，造成整个连接失败

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/wireshark.png)

**那么为什么会继续复用已经关闭的链接呢？**

我们继续从代码出发，通过日志，我们可以看出每次链接前都有次向连接池请求连接的过程

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/httpclient1.png)

进入连接池管理代码，我们可以看到这边有两次检测是否要关闭:

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/abstractconnpool.png" style="zoom:67%;" />

首先检测有没有配置`validateAfterInactivity`(默认为**2000ms**),如果超过了指定的时间间隔就进行`validate`

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/basicconnpool.png" style="zoom:67%;" />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/httpclient/bhttpconnectionbase.png" style="zoom:67%;" />

- `validate`就是尝试读一次(超时**1ms**)来判断有没有失效
- 如果读到**-1**(**end of stream**)或者发生**IO**异常了就认为失效了
- 如果超时了就认为还是正常(因为**httpclient**阻塞读)

所以出现链接异常会有2种情况

1. **keepalive**时间太短，因此**httpClient**以为还能用就没有检测直接使用了，从日志可以看出，根本没有`validate`过程，在**TCP**的连接上表现为**FIN**后直接发起请求
2. 刚好服务端释放请求时发起访问。如之前例子所示，在服务端还没发送**FIN**是进行了**validate**，但是刚好在正式发送请求前服务端发送了**FIN**，因此造成了**RST**。或者是如下正好在服务端关闭时发送了请求

{% note info no-icon %}

就要提一下**Keepalive**机制。**Keepalive**是**HTTP**的连接复用机制，在**HTTP1.0**时代，每个请求经过三次握手后，只会传输一次**HTTP**请求和响应报文后，就进入四次挥手关闭连接了。而**TCP**建立连接和关闭连接的代价是比较大的，导致**HTTP1.0**的通道利用率较低，时延较高。针对这个问题，退出了**Keepalive**机制，一个**TCP**连接建立后，可以在上面发送多个**HTTP**报文，只有这个**TCP**连接的空闲时间达到超时时间，才会被关闭。**HTTP1.1**默认开启**Keepalive**。这里的关闭行为可能发生在客户端和服务端，比如客户端的**Keepalive**超时时间更短，则客户端就会先关闭连接，如果服务端配置的**Keepalive**超时时间更短，则服务端就会先关闭连接。

乍看起来无论那一边关闭连接都没什么问题，但是还是有细节需要注意。比如服务端关闭连接，发送**FIN**包，在这个**FIN**包发送但是还未到达客户端期间，客户端如果继续复用这个**TCP**连接，发送**HTTP**请求报文的话，服务端会因为在四次挥手期间不接收报文而发送**RST**报文给客户端，客户端收到**RST**报文就会提示异常。

{% endnote %}

**解决办法：**

我们调整前置、`Ingress`以及网关等服务的`keep-alive-timeout`的时间，确保前置小于`Ingress`，`Ingress`小于网关、网关小于聚合。即客户端设置的`keep-alive-timeout`的时间小于服务端的`keep-alive-timeout`的时间。

---
title: Netty核心模块组件
date: 2025-08-05 20:13:54
tags:
- NIO
- Netty
categories: Netty
---

# Netty 核心模块组件

## Bytebuf（字节容器）

**网络通信最终都是通过字节流进行传输的。 `ByteBuf` 就是 Netty 提供的一个字节容器，其内部是一个字节数组。** 当我们通过 Netty 传输数据的时候，就是通过 `ByteBuf` 进行的。

我们可以将 `ByteBuf` 看作是 Netty 对 Java NIO 提供了 `ByteBuffer` 字节容器的封装和抽象。

有很多小伙伴可能就要问了 ： **为什么不直接使用 Java NIO 提供的 `ByteBuffer` 呢？**

因为 `ByteBuffer` 这个类使用起来过于复杂和繁琐。

## Bootstrap 和 ServerBootstrap（启动引导类）

`Bootstrap` 意思是引导，一个 `Netty` 应用通常由一个 `Bootstrap` 开始，主要作用是配置整个 `Netty` 程序，串联各个组件。

`Bootstrap` 类是客户端程序的启动引导类，具体使用方法如下：

```java
				EventLoopGroup group = new NioEventLoopGroup();
        try {
            //创建客户端启动引导/辅助类：Bootstrap
            Bootstrap b = new Bootstrap();
            //指定线程模型
            b.group(group).
                    ......
            // 尝试建立连接
            ChannelFuture f = b.connect(host, port).sync();
            f.channel().closeFuture().sync();
        } finally {
            // 优雅关闭相关线程组资源
            group.shutdownGracefully();
        }
```

`ServerBootstrap` 是服务端启动引导类，具体使用方法如下：

```java
        // 1.bossGroup 用于接收连接，workerGroup 用于具体的处理
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            //2.创建服务端启动引导/辅助类：ServerBootstrap
            ServerBootstrap b = new ServerBootstrap();
            //3.给引导类配置两大线程组,确定了线程模型
            b.group(bossGroup, workerGroup).
                   ......
            // 6.绑定端口
            ChannelFuture f = b.bind(port).sync();
            // 等待连接关闭
            f.channel().closeFuture().sync();
        } finally {
            //7.优雅关闭相关线程组资源
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
```

从上面的示例中，我们可以看出：

1. `Bootstrap` 通常使用 `connet()` 方法连接到远程的主机和端口，作为一个 Netty TCP 协议通信中的客户端。另外，`Bootstrap` 也可以通过 `bind()` 方法绑定本地的一个端口，作为 UDP 协议通信中的一端。
2. `ServerBootstrap`通常使用 `bind()` 方法绑定本地的端口上，然后等待客户端的连接。
3. `Bootstrap` 只需要配置一个线程组— `EventLoopGroup` ,而 `ServerBootstrap`需要配置两个线程组— `EventLoopGroup` ，一个用于接收连接，一个用于具体的 IO 处理。

常见的方法有

- `public ServerBootstrap group(EventLoopGroup parentGroup, EventLoopGroup childGroup)`，该方法用于服务器端，用来设置两个 `EventLoop`
- `public B group(EventLoopGroup group)`，该方法用于客户端，用来设置一个 `EventLoop`
- `public B channel(Class<? extends C> channelClass)`，该方法用来设置一个服务器端的通道实现
- `public <T> B option(ChannelOption<T> option, T value)`，用来给 `ServerChannel` 添加配置
- `public <T> ServerBootstrap childOption(ChannelOption<T> childOption, T value)`，用来给接收到的通道添加配置
- `public ServerBootstrap childHandler(ChannelHandler childHandler)`，该方法用来设置业务处理类（自定义的`handler`）
- `public ChannelFuture bind(int inetPort)`，该方法用于服务器端，用来设置占用的端口号
- `public ChannelFuture connect(String inetHost, int inetPort)`，该方法用于客户端，用来连接服务器端

---------

## Future、ChannelFuture

```java
public interface ChannelFuture extends Future<Void> {
    Channel channel();

    ChannelFuture addListener(GenericFutureListener<? extends Future<? super Void>> var1);
     ......

    ChannelFuture sync() throws InterruptedException;
}
```

`Netty` 是异步非阻塞的，所有的 I/O 操作都为异步的。

因此，我们不能立刻得到操作是否执行成功，但是，你可以通过 `ChannelFuture` 接口的 `addListener()` 方法注册一个 `ChannelFutureListener`，当操作执行成功或者失败时，监听就会自动触发返回结果。

```java
ChannelFuture f = b.connect(host, port).addListener(future -> {
  if (future.isSuccess()) {
    System.out.println("连接成功!");
  } else {
    System.err.println("连接失败!");
  }
}).sync();
```

并且，你还可以通过`ChannelFuture` 的 `channel()` 方法获取连接相关联的`Channel` 。

```
Channel channel = f.channel();
```

另外，我们还可以通过 `ChannelFuture` 接口的 `sync()`方法让异步的操作编程同步的。

```
//bind()是异步的，但是，你可以通过 `sync()`方法将其变为同步。
ChannelFuture f = b.bind(port).sync();
```

## Channel

`Channel` 接口是 Netty 对网络操作抽象类。

1. 通过 `Channel` 我们可以进行 I/O 操作
2. 通过 `Channel` 可获得当前网络连接的通道的状态
3. 通过 `Channel` 可获得网络连接的配置参数（例如接收缓冲区大小）
4. `Channel` 提供异步的网络 `I/O` 操作(如建立连接，读写，绑定端口)，异步调用意味着任何 `I/O` 调用都将立即返回，并且不保证在调用结束时所请求的 `I/O` 操作已完成
5. 调用立即返回一个 `ChannelFuture` 实例，通过注册监听器到 `ChannelFuture` 上，可以 `I/O` 操作成功、失败或取消时回调通知调用方
6. 支持关联 `I/O` 操作与对应的处理程序

比较常用的`Channel`接口实现类是 ：

- `NioServerSocketChannel`（服务端）
- `NioSocketChannel`（客户端）

这两个 `Channel` 可以和 BIO 编程模型中的`ServerSocket`以及`Socket`两个概念对应上。

## Selector

1. `Netty` 基于 `Selector` 对象实现 `I/O` 多路复用，通过 `Selector` 一个线程可以监听多个连接的 `Channel` 事件。
2. 当向一个 `Selector` 中注册 `Channel` 后，`Selector` 内部的机制就可以自动不断地查询（`Select`）这些注册的 `Channel` 是否有已就绪的 `I/O` 事件（例如可读，可写，网络连接完成等），这样程序就可以很简单地使用一个线程高效地管理多个 `Channel`

## ChannelHandler 及其实现类

**`ChannelHandler` 是消息的具体处理器，主要负责处理客户端/服务端接收和发送的数据。**	![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/ChannelHandler.jpg)

`ChannelHandler`本身并没有提供很多方法，因为这个接口有许多的方法需要实现，方便使用期间，可以继承它的子类：

- `ChannelInboundHandler`用于处理入站I / O事件
- `ChannelOutboundHandler`用于处理出站I / O操作

或者使用以下适配器类：

- `ChannelInboundHandlerAdapter`用于处理入站I / O事件
- `ChannelOutboundHandlerAdapter`用于处理出站I / O操作
- `ChannelDuplexHandler`用于处理入站和出站事件

当 `Channel` 被创建时，它会被自动地分配到它专属的 `ChannelPipeline`。 一个`Channel`包含一个 `ChannelPipeline`。 `ChannelPipeline` 为 `ChannelHandler` 的链，一个 pipeline 上可以有多个 `ChannelHandler`。

我们可以在 `ChannelPipeline` 上通过 `addLast()` 方法添加一个或者多个`ChannelHandler` （*一个数据或者事件可能会被多个 Handler 处理*） 。当一个 `ChannelHandler` 处理完之后就将数据交给下一个 `ChannelHandler` 。

当 `ChannelHandler` 被添加到的 `ChannelPipeline` 它得到一个 `ChannelHandlerContext`，它代表一个 `ChannelHandler` 和 `ChannelPipeline` 之间的“绑定”。 `ChannelPipeline` 通过 `ChannelHandlerContext`来间接管理 `ChannelHandler` 。

## Pipeline 和 ChannelPipeline

`ChannelPipeline` 是一个重点：

1. `ChannelPipeline` 是一个 `Handler` 的集合，它负责处理和拦截 `inbound` 或者 `outbound` 的事件和操作，相当于一个贯穿 `Netty` 的链。（也可以这样理解：`ChannelPipeline` 是保存 `ChannelHandler` 的 `List`，用于处理或拦截 `Channel` 的入站事件和出站操作）
2. `ChannelPipeline` 实现了一种高级形式的拦截过滤器模式，使用户可以完全控制事件的处理方式，以及 `Channel` 中各个的 `ChannelHandler` 如何相互交互
3. 在 `Netty` 中每个 `Channel` 都有且仅有一个 `ChannelPipeline` 与之对应，它们的组成关系如下

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/channelpipline.jpg)

一个 `Channel` 包含了一个 `ChannelPipeline`, 而 `ChannelPipeline` 中又维护了一个由 `ChannelHandlerContext` 组成的双向链表, 并且每个 `ChannelHandlerContext` 中又关联着一个 `ChannelHandler`。入站事件和出站事件在一个双向链表中，入站事件会从链表`head`往后传递到最后一个入站的`handler`，出站事件会从链表`tail`往前传递到最前一个出站的`handler`，两种类型的`handler`互不干扰。

## ChannelHandlerContext

1. 保存 `Channel` 相关的所有上下文信息，同时关联一个 `ChannelHandler` 对象
2. 即 `ChannelHandlerContext` 中包含一个具体的事件处理器 `ChannelHandler`，同时 `ChannelHandlerContext` 中也绑定了对应的 `pipeline` 和 `Channel` 的信息，方便对 `ChannelHandler` 进行调用

## ChannelOption

1. `Netty` 在创建 `Channel` 实例后，一般都需要设置 `ChannelOption` 参数。
2. `ChannelOption` 参数如下：

- **ChannelOption.SO_BACKLOG**

  对应TCP/IP协议listen函数中的backlog参数，用来初始化服务器可连接队列大小。服务端处理客户端连接请求是顺序处理的，所以同一时间只能处理一个客户端连接。多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog参数指定了队列的大小

- **ChannelOption.SO_KEEPALIVE**

  Socket参数，连接保活，默认值为False。启用该功能时，将一直保持连接活动状态

## EventLoop

这么说吧！`EventLoop`（事件循环）接口可以说是 Netty 中最核心的概念了！

《Netty 实战》这本书是这样介绍它的：

> `EventLoop` 定义了 Netty 的核心抽象，用于处理连接的生命周期中所发生的事件。

是不是很难理解？说实话，我学习 Netty 的时候看到这句话是没太能理解的。

说白了，**`EventLoop` 的主要作用实际就是责监听网络事件并调用事件处理器进行相关 I/O 操作（读写）的处理。**

### Channel 和 EventLoop 的关系

那 `Channel` 和 `EventLoop` 直接有啥联系呢？

**`Channel` 为 Netty 网络操作(读写等操作)抽象类，`EventLoop` 负责处理注册到其上的`Channel` 的 I/O 操作，两者配合进行 I/O 操作。**

## EventLoopGroup 和其实现类 NioEventLoopGroup

在基于 Netty 的 TCP Server 代码中，包含了两个 EventLoopGroup——bossGroup 和 workerGroup，EventLoopGroup 是一组 EventLoop 的抽象。

追踪 Netty 的 EventLoop 的继承链，可以发现 EventLoop 最终继承于 JUC Executor，因此 EventLoop 本质就是一个 JUC Executor，即线程，JUC Executor 的源码为：

```java
public interface Executor {
    /**
     * Executes the given command at some time in the future.
     */
    void execute(Runnable command);
}
```

Netty 为了更好地利用多核 CPU 的性能，一般会有多个 EventLoop 同时工作，每个 EventLoop 维护着一个 Selector 实例，Selector 实例监听注册其上的 Channel 的 IO 事件。

EventLoopGroup 含有一个 next 方法，它的作用是按照一定规则从 Group 中选取一个 EventLoop 处理 IO 事件。

在服务端，通常 Boss EventLoopGroup 只包含一个 Boss EventLoop（单线程），该 EventLoop 维护者一个注册了 ServerSocketChannel 的 Selector 实例。该 EventLoop 不断轮询 Selector 得到 OP_ACCEPT 事件（客户端连接事件），然后将接收到的 SocketChannel 交给 Worker EventLoopGroup，Worker EventLoopGroup 会通过 next()方法选取一个 Worker EventLoop 并将这个 SocketChannel 注册到其中的 Selector 上，由这个 Worker EventLoop 负责该 SocketChannel 上后续的 IO 事件处理。整个过程如下图所示：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/eventLoopGroup.png)

## Unpooled 类

1. `Netty` 提供一个专门用来操作缓冲区(即 `Netty` 的数据容器)的工具类
2. 常用方法如下所示

```csharp
// 通过给定的数据和字符编码返回一个ByteBuf对象（类似于NIO中的ByteBuffer但有区别）
public static ByteBuf copiedBuffer(CharSequence string, Charset charset)
```

`ByteBuf`和 `ByteBuffer`的区别？

- `ByteBuffer`是NIO的对象，**ByteBuf**是**Netty**的对象
- `ByteBuffer` 维护着**postion**,**limit**,**capacity**字段，每次读写时，需要进行转换，即让**position=0**
- `ByteBuf` 维护着**ReadIndex**,**WriteIndex**,**Capacity**,将**ByteBuf**分成三个区域
  - 已读区域 0-readIndex
  - 可读区域 readIndex-writeIndex
  - 可写区域 writeIndex-capacity

举例说明 **Unpooled** 获取 **Netty** 的数据容器 **ByteBuf** 的基本使用

```java
package netty.buf;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;

public class NettyByteBuf01 {
	
	public static void main(String[] args) {
		//创建一个ByteBuf
		//说明
		//1. 创建一个对象，该对象包含一个数组arr，是一个byte[10]
		//2. 在netty的buffer中，不需要使用flip进行反转
		//   是因为底层维护了readerIndex属性和writerIndex属性
		//3. 通过 readerIndex 和 writerIndex 和 capacity，将buffer分成了三段
		//   0 --> readerIndex：已经读取的区域
		//   readerIndex --> writerIndex：可读的区域
		//   writerIndex --> capacity：可写的区域
		ByteBuf buffer = Unpooled.buffer(10);
		
		for (int i = 0; i < 10; i++) {
			buffer.writeByte(i);
		}
		
		System.out.println("capacity=" + buffer.capacity()); //10
		//输出
		for (int i = 0; i < buffer.capacity(); i++) {
			System.out.println(buffer.readByte());
		}
	}
}
```

当写入结束后，`writerIndex=10`,array数组被赋值

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/nettyByteBuf02.png" style="zoom:67%;" />

读取时，`readerIndex`会递增

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/nettyByteBuf03.png" style="zoom:67%;" />

# Netty 应用实例-群聊系统

实例要求：

1. 编写一个 `Netty` 群聊系统，实现服务器端和客户端之间的数据简单通讯（非阻塞）
2. 实现多人群聊
3. 服务器端：可以监测用户上线，离线，并实现消息转发功能
4. 客户端：通过 `channel` 可以无阻塞发送消息给其它所有用户，同时可以接受其它用户发送的消息（有服务器转发得到）

**GroupChatServer**

```java
package com.wotzc.netty.group;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;

public class GroupChatServer {

    // 监听端口
    private int port;

    public GroupChatServer(int port) {
        this.port = port;
    }

    //编写run方法，处理客户端的请求
    public void run() throws Exception {

        //创建两个线程组
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();  // 默认8个线程

        try {
            ServerBootstrap bootstrap = new ServerBootstrap();


            bootstrap.group(bossGroup, workerGroup) // 设置两个 `EventLoopGroup`
                    .channel(NioServerSocketChannel.class) // 设置一个服务器端的通道实现
                    .option(ChannelOption.SO_BACKLOG, 128) // 设置队列大小128
                    .childOption(ChannelOption.SO_KEEPALIVE, true) // 添加用来接受的通道配置，一直保持连接
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel socketChannel) throws Exception {
                            //获取到pipeline
                            ChannelPipeline pipeline = socketChannel.pipeline();
                            //向pipeline加入解码器
                            pipeline.addLast("decoder", new StringDecoder());
                            //向pipeline加入编码器
                            pipeline.addLast("encoder", new StringEncoder());
                            //加入自己的业务处理handler
                            pipeline.addLast(new GroupChatServerHandler());
                        }

                    });

            System.out.println("netty 服务器启动");
            ChannelFuture channelFuture = bootstrap.bind(port).sync();

            //监听关闭
            channelFuture.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws Exception {
        new GroupChatServer(8000).run();
    }
}
```

**GroupChatServerHandler**

```java
package com.wotzc.netty.group;

import io.netty.channel.Channel;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.channel.group.ChannelGroup;
import io.netty.channel.group.DefaultChannelGroup;
import io.netty.util.concurrent.GlobalEventExecutor;

import java.text.SimpleDateFormat;

/**
 * 作为服务端，这个Handler能够监听各种事件，这些事件对应着不同的重载方法
 * 1. 上线
 * 2. 离线
 * 3. 给其他人发消息等
 */

//这里的泛参数设置为 String，因为现在通信使用的是String字符串
public class GroupChatServerHandler extends SimpleChannelInboundHandler<String> {


    // 定义一个channelGroup，管理所有的channel
    // GlobalEventExecutor.INSTANCE-单例-全局的事件执行器
    private static final ChannelGroup channelGroup = new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    /**
     * handlerAdded 表示连接建立，一旦连接，第一个被执行，此时通道尚未被激活
     * 将当前channel加入到channelGroup
     */
    @Override
    public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
        Channel channel = ctx.channel();
        //将该客户加入聊天的信息推送给其它在线的客户端
        // 遍历channelGroup中所有的channel，并发送消息
        channelGroup.writeAndFlush("[客户端]" + channel.remoteAddress() + " 加入聊天" + sdf.format(new java.util.Date()));
        channelGroup.add(channel);
    }

    /**
     * 断开连接，把相关信息发送给其他客户端
     */
    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
        Channel channel = ctx.channel();
        channelGroup.writeAndFlush("[客户端]" + channel.remoteAddress() + " 离开了");
        System.out.println("channelGroup size" + channelGroup.size());

    }

    /**
     * 通道连接被激活了
     */
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        System.out.println(ctx.channel().remoteAddress() + " 上线了~");
    }

    /**
     * 通道依旧存在，只是状态变为离线了
     */
    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        System.out.println(ctx.channel().remoteAddress() + " 离线了~");
    }

    //读取数据
    @Override
    protected void channelRead0(ChannelHandlerContext handlerContext, String msg) throws Exception {
        //获取到当前channel
        Channel currentChannel = handlerContext.channel();
        //这时我们遍历channelGroup, 根据不同的情况，回送不同的消息
        channelGroup.forEach(channel -> {
            if (currentChannel != channel) {
                // 不是当前的channel,转发消息
                channel.writeAndFlush(channel.remoteAddress() + " 发送了消息: " + msg + "\n");
            } else {
                //回显自己发送的消息给自己
                channel.writeAndFlush("[自己]发送了消息: " + msg + "\n");
            }
        });
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        //关闭通道
        ctx.close();
    }
}
```

**GroupChatClient**

```java
package com.wotzc.netty.group;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;

import java.util.Scanner;

public class GroupChatClient {

    //属性
    private final String host;
    private final int port;

    public GroupChatClient(String host, int port) {
        this.host = host;
        this.port = port;
    }

    public void run() throws Exception {
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap bootstrap = new Bootstrap()
                    .group(group)
                    .channel(NioSocketChannel.class)
                    .handler(new ChannelInitializer<SocketChannel>() {

                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            //得到pipeline
                            ChannelPipeline pipeline = ch.pipeline();
                            //加入相关handler
                            pipeline.addLast("decoder", new StringDecoder());
                            pipeline.addLast("encoder", new StringEncoder());
                            //加入自定义的handler
                            pipeline.addLast(new GroupChatClientHandler());
                        }
                    });
            ChannelFuture channelFuture = bootstrap.connect(host, port).sync();
            //得到channel
            Channel channel = channelFuture.channel();
            System.out.println("-------" + channel.localAddress() + "--------");
            //客户端需要输入信息，创建一个扫描器
            Scanner scanner = new Scanner(System.in);
            while (scanner.hasNextLine()) {
                String msg = scanner.nextLine();
                //通过channel 发送到服务器端
                channel.writeAndFlush(msg + "\r\n");
            }
        } finally {
            group.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws Exception {
        new GroupChatClient("127.0.0.1", 8000).run();
    }
}
```

**GroupChatClientHandler**

```java
package com.wotzc.netty.group;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;

public class GroupChatClientHandler extends SimpleChannelInboundHandler<String> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {
        System.out.println(msg);
    }
}
```

# Netty 心跳检测机制案例

实例要求：

1. 编写一个 `Netty` 心跳检测机制案例,当服务器超过 `3` 秒没有读时，就提示读空闲
2. 当服务器超过 `5` 秒没有写操作时，就提示写空闲
3. 实现当服务器超过 `7` 秒没有读或者写操作时，就提示读写空闲

**MyServer**

```java
package com.wotzc.netty.heart;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;
import io.netty.handler.timeout.IdleStateHandler;

import java.util.concurrent.TimeUnit;

public class MyServer {

    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(1);
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO)) // 给自己的通道添加Log日志处理器
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel channel) throws Exception {
                            ChannelPipeline pipeline = channel.pipeline();
                            // 1. IdleStateHandler 是 netty 提供的处理空闲状态的处理器
                            // 2. long readerIdleTime 表示多长时间没有读, 就会发送一个心跳检测包检测是否连接
                            // 3. long writerIdleTime 表示多长时间没有写, 就会发送一个心跳检测包检测是否连接
                            // 4. long allIdleTime 表示多长时间没有读写, 就会发送一个心跳检测包检测是否连接
                            // 5. 当 IdleStateEvent 触发后 , 就会传递给管道 的下一个 handler 去处理
                            //    通过调用(触发)下一个 handler 的 userEventTiggered ,
                            //    在该方法中去处理 IdleStateEvent(读 空闲，写空闲，读写空闲)
                            pipeline.addLast(new IdleStateHandler(7000, 7000, 10, TimeUnit.SECONDS));
                            //加入一个对空闲检测进一步处理的handler(自定义)
                            pipeline.addLast(new MyServerHandler());
                        }
                    });

            // 启动服务器
            ChannelFuture channelFuture = serverBootstrap.bind(8000).sync();
            channelFuture.channel().closeFuture().sync();

        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
    
}
```

# Netty 通过 WebSocket 编程实现服务器和客户端长连接

1. Http 协议是无状态的, 浏览器和服务器间的请求响应一次，下一次会重新创建连接.
2. 要求：实现基于 webSocket 的长连接的全双工的交互
3. 改变 Http 协议多次请求的约束，实现长连接了， 服务器可以发送消息给浏览器
4. 客户端浏览器和服务器端会相互感知，比如服务器关闭了，浏览器会感知，同样浏览器关闭了，服务器会感知

​		<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/netty-websocket1.png" style="zoom:67%;" />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/netty-websocket2.png" style="zoom:67%;" />

## 服务端

**WebSocketServer**

```java
package com.wotzc.netty.socket;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.http.HttpObjectAggregator;
import io.netty.handler.codec.http.HttpServerCodec;
import io.netty.handler.codec.http.websocketx.WebSocketServerProtocolHandler;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;
import io.netty.handler.stream.ChunkedWriteHandler;

public class WebSocketServer {

    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(1);
        NioEventLoopGroup workGroup = new NioEventLoopGroup();
        try{
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(bossGroup,workGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            // 因为基于http协议，所以我们使用http的编码和解码器
                            pipeline.addLast(new HttpServerCodec());
                            // 是以块方式写的，添加ChunkedWriteHandler处理器
                            // chunkedWriteHandler是处理大数据传输的，不会占用大内存或导致内存溢出问题
                            pipeline.addLast(new ChunkedWriteHandler());
                            /*
                             * 说明：
                             * 1. http数据在传输过程中是分段的，HttpObjectAggregator就是可以将多个段聚合起来
                             * 2. 这就是为什么，当浏览器发送大量数据时，就会发送多次http请求
                             */
                            pipeline.addLast(new HttpObjectAggregator(8192));
                            /*
                             * 说明：
                             * 1. 对应WebSocket，它的数据是以帧（frame）形式传递
                             * 2. 可以看到WebSocketFrame下面有六个子类
                             * 3. 浏览器请求时：ws://localhost:7000/hello 表示请求的uri
                             * 4. WebSocketServerProtocolHandler核心功能是将http协议升级为ws协议，保持长连接
                             */
                            pipeline.addLast(new WebSocketServerProtocolHandler("/hello"));
                            pipeline.addLast(new MyTextWebSocketFrameHandler());
                        }
                    });
            ChannelFuture channelFuture = serverBootstrap.bind(8000).sync();
            channelFuture.channel().closeFuture().sync();
        }finally {
            bossGroup.shutdownGracefully();
            workGroup.shutdownGracefully();
        }
    }
}
```

**MyTextWebSocketFrameHandler**

```java
package com.wotzc.netty.socket;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.channel.group.ChannelGroup;
import io.netty.channel.group.DefaultChannelGroup;
import io.netty.handler.codec.http.websocketx.TextWebSocketFrame;
import io.netty.util.concurrent.GlobalEventExecutor;

import java.time.LocalDateTime;

public class MyTextWebSocketFrameHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {

    private static final ChannelGroup channelGroup=new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, TextWebSocketFrame msg) throws Exception {
        System.out.println("服务器收到消息 " + msg.text());
        ctx.channel().writeAndFlush(new TextWebSocketFrame("服务器时间 " + LocalDateTime.now() + " " + msg.text()));
    }

    //当 web 客户端连接后， 触发方法
    @Override
    public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
        channelGroup.add(ctx.channel());
        //id 表示唯一的值，LongText 是唯一的 ShortText 不是唯一
        System.out.println (" handlerAdded called "+ctx.channel(). id(). asLongText()); 
    }

    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
        System.out.println("handlerRemoved 被调用" + ctx.channel().id().asLongText());
    }

}
```

## 客户端

**hello.html**

```html
<!DOCTYPE html>
<html lang="en">

<head>
<meta charset="UTF-8">
<title>hello</title>
<style>
	
</style>
</head>

<body>
	<form onsubmit="return false">
		<textarea name="message" style="height:300px; width: 300px"></textarea>
		
		<input type="button" value="发送消息" onclick="send(this.form.message.value)" />
		
		<textarea id="responseText" style="height:300px; width: 300px"></textarea>
		
		<input type="button" value="清空内容" onclick="document.getElementById('responseText').value=''">
	</form>
</body>
</html>

<script>
	//也可使用SockJS库

	var socket;
	//判断当前浏览器是否支持WebSocket
	if(window.WebSocket) {
		//go on
		socket = new WebSocket("ws://localhost:8000/hello");
		
		//相当于channelRead0，event收到服务器端回送的消息
		socket.onmessage = function(event) {
			var rt = document.getElementById('responseText');
			rt.value = rt.value + "\n" + event.data;
			
		}
		
		//相当于连接开启(感知到连接开启)
		socket.onopen = function(event) {
			var rt = document.getElementById('responseText');
			rt.value = "连接开启了...";
			
		}
		
		//连接关闭(感知到连接关闭)
		socket.onclose = function(event) {
			var rt = document.getElementById('responseText');
			rt.value = rt.value + "\n" + "连接关闭了...";
			
		}
	} else {
		alert("当前浏览器不支持WebSocket编程");
	}
	
	//发送消息到服务器
	function send(message) {
		if(!window.socket){ //判断socket是否创建好
			return;
			
		}
		
		if(socket.readyState == WebSocket.OPEN) {
			//通过socket发送消息
			socket.send(message);
		} else {
			alert("连接未开启...");
		}
	}
</script>
```


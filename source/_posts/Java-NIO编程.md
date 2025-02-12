---
title: Java NIO编程
date: 2025-01-14 14:31:26
tags:
- NIO
- Netty
categories: Netty
---

## Java NIO 基本介绍

1. `Java NIO` 全称 **`Java non-blocking IO`** ，是指 `JDK` 提供的新 `API`。从 `JDK1.4` 开始，`Java` 提供了一系列改进的输入/输出的新特性，被统称为 `NIO`（即 `NewIO`），是同步非阻塞的。
2. `NIO` 相关类都被放在 **`java.nio`** 包及子包下，并且对原 `java.io` 包中的很多类进行改写。
3. `NIO` 有三大核心部分: **`Channel`（通道）、`Buffer`（缓冲区）、`Selector`（选择器）** 。
4. `NIO` 是**面向缓冲区，或者面向块编程**的。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络。
5. `Java NIO` 的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。通俗理解：`NIO` 是可以做到用一个线程来处理多个操作的。假设有 `10000` 个请求过来,根据实际情况，可以分配 `50` 或者 `100` 个线程来处理。不像之前的阻塞 `IO` 那样，非得分配 `10000` 个。
6. `HTTP 2.0` 使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比 `HTTP 1.1` 大了好几个数量级。

## NIO 和 BIO 的比较

1. `BIO` 以流的方式处理数据，而 `NIO` 以块的方式处理数据，块 `I/O` 的效率比流 `I/O` 高很多。
2. `BIO` 是阻塞的，`NIO` 则是非阻塞的。
3. `BIO` 基于字节流和字符流进行操作，而 `NIO` 基于 `Channel`（通道）和 `Buffer`（缓冲区）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。`Selector`（选择器）用于监听多个通道的事件（比如：连接请求，数据到达等），因此使用单个线程就可以监听多个客户端通道。

## NIO 三大核心原理示意图

一张图描述 `NIO` 的 `Selector`、`Channel` 和 `Buffer` 的关系。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/chapter03_01.png" style="zoom:67%;" />

**一个线程对应一个Selector**，**一个Selector对应多个channel**（连接）。程序切换到哪个channel是由**事件**决定的，Selector会根据不同的事件，在各个通道上切换。每个channel都会对应一个Buffer，数据的读取写入是通过Buffer完成的。Buffer就是一个内存块，底层是一个数组。BIO中要么是输入流，要么是输出流，不能双向，但是 NIO 的**Buffer是可以读也可以写的**。

**Java NIO**系统的核心在于：通道(**Channel**)和缓冲区(**Buffer**)。通道表示打开到 IO 设备(例如：文件、 套接字)的连接。若需要使用 NIO 系统，需要获取用于连接 IO 设备的通道以及用于容纳数据的缓冲区。然后操作缓冲区，对数据进行处理。简而言之，**Channel 负责传输，Buffer负责存取数据**。

## NIO 三大核心

### 缓冲区（Buffer）

**基本介绍：**

**Buffer**（缓冲区）是一个可以读取数据的内存块，可以理解成一个**容器对象（含数组）**，该对象提供了**一组方法**，可以轻松的使用内存块。缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。**channel**提供从文件、网络读取数据的渠道，但是读取和写入数据必须都经由**buffer**。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/chapter03_02.png" style="zoom:67%;" />

#### Buffer 类及其子类

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/chapter03_03.png" style="zoom:67%;" />

Buffer 有 2 个核心方法和 4 个核心属性，下面我们一起来看看。

核心方法，内容如下：

- **put()**：存入数据到缓冲区中
- **get()**：从缓冲区中的读取数据

`Buffer` 类定义了所有的缓冲区都具有的四个属性来提供关于其所包含的数据元素的信息：

- **capacity**：容量，即可以容纳的最大数据量。在缓冲区被创建时确定，并且不能更改。
- **limit**：表示缓冲区中可以操作数据的大小（limit后数据不能进行读写）。缓冲区的limit不能为负，并且不能大于其容量。写入模式，limit等于buffer的容量。读取模式下，limit等于写入的数据量。
- **position**：下一个要读取或写入的数据的索引。缓冲区的位置不能为负，并且不能大于其limit。
- **mark**：标记，是一个索引，通过 Buffer 中的mark()方法指定 Buffer 中一个特定的position，之后可以通过调用 reset() 方法恢复到这个position。

以分配一个大小为 10 个字节容量的缓冲区，向里面写入`abcde`并读取数据为例，Buffer 处理过程如下图：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/nio-buffer.png" style="zoom:67%;" />

需要注意的是：

- `0 <= mark <= position <= limit <= capacity`
- `limit`后的数据不能进行读写

**基本方法：**

**JDK1.4** 时引入的**api**

- `int capacity()`：返回 **Buffer** 的 **capacity** 大小；
- `int position()`：返回缓冲区的当前位置 **position**；
- `Buffer position(int n)`：将设置缓冲区的当前位置为 `n`，并返回修改后的 **Buffer** 对象；
- `int limit()`：返回 **Buffer** 的界限(**limit**)的位置；
- `Buffer limit(int n)`：将设置缓冲区界限为 `n`，并返回一个具有新的 **limit** 的缓冲区对象；
- `Buffer mark()`：对缓冲区设置标记；
- `Buffer reset()`：将位置 **position** 转到以前设置的 **mark** 所在的位置；
- `Buffer clear()`：清空缓冲区并返回对缓冲区的引用；
- `Buffer flip()`：为将缓冲区的界限设置为当前位置，并将当前位置充值为 **0**；
- `public final Buffer rewind()`：重绕此缓冲区
- `public final int remaining()`：返回当前位置与限制之间的元素数
- `boolean hasRemaining()`：判断当前位置和**limit**之间是否还有元素；
- `boolean isReadOnly()`：缓冲区是否为只读；

**JDK1.6** 时引入的**api**

- `boolean hasArray()`：缓冲区是否有可以访问的底层实现数组；
- `Object array()`：返回缓冲区的底层实现数组；
- `public abstract int arrayOffset()`：返回此缓冲区的底层实现数组中第一个缓冲区元素的偏移量
- `public abstract boolean isDirect()`：告知此缓冲区是否为直接缓冲区

缓冲区读写操作通过`get()`和`put()`方法来进行：

- `get()`：读取单个字节；
- `get(byte[] dst)`：批量读取多个字节到**dst**中；
- `get(int index)`：读取指定索引位置的字节(不会移动 **position**)；
- `put(byte b)`：将给定单个字节写入缓冲区的当前位置；
- `put(byte[] src)`：将 **src** 中的字节写入缓冲区的当前位置；
- put(int index, byte b)：将指定字节写入缓冲区的索引位置(不会移动 **position**)

**使用案例：** Buffer的子类中最常用的是**ByteBuffer**类，**ByteBuffer**常用方法使用案例如下：

```java
/**
 * Buffers所有子类的管理方式几乎一致，通过 allocate() 获取缓冲区
 * 获取Buffer的方式有两种：
 *   非直接缓冲区：通过 allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中
 *   直接缓冲区：通过 allocateDirect() 方法分配直接缓冲区，将缓冲区建立在物理内存中。可以提高读写效率，
 *   但是直接缓冲区的创建和销毁会有更大的开销
 */
public class BufferCase {
    public static void main(String[] args) {
        String str = "abcde";

        // 分配缓冲区
        //1. 分配一个指定大小的缓冲区
        ByteBuffer buf = ByteBuffer.allocate(1024);
        // 此时 position = 0； limit = capacity = 1024
        System.out.println("-----------------allocate()----------------");
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());

        //2. 利用 put() 存入数据到缓冲区中
        buf.put(str.getBytes());
        // 此时 position = 5； limit = capacity = 1024
        System.out.println("-----------------put()----------------");
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());

        //3. 切换读取数据模式
        buf.flip();
        // 此时 position = 0； limit = 5； capacity = 1024
        System.out.println("-----------------flip()----------------");
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());

        //4. 利用 get() 读取缓冲区中的数据
        byte[] dst = new byte[buf.limit()];
        buf.get(dst);
        // 此时 position = 5； limit = 5； capacity = 1024
        System.out.println("-----------------get()----------------");
        System.out.println(new String(dst, 0, dst.length));
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());

        //5. rewind() : 可重复读
        buf.rewind();
        // 此时 position = 0； limit = 5； capacity = 1024
        System.out.println("-----------------rewind()----------------");
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());

        //6. clear() : 清空缓冲区. 但是缓冲区中的数据依然存在，但是处于“被遗忘”状态
        buf.clear();
        // 此时 position = 0； limit = capacity = 1024
        System.out.println("-----------------clear()----------------");
        System.out.println(buf.position());
        System.out.println(buf.limit());
        System.out.println(buf.capacity());
        // 原有的数据仍然存在，可以读出
        System.out.println((char)buf.get());
    }
}
```

### 通道（Channel）

**Channel**（通道）由java.nio.channels包定义。通道表示与某个实体（例如硬件设备、文件、网络套接字或程序组件）的开放连接，该实体能够执行一个或多个不同的 I/O 操作，例如读取或写入。**Channel**本身不能直接访问数据，**Channel**只能与 **Buffer** 进行交互。

1）**Channel**类似于传统的流，但有些区别：通道可以同时进行读写，而流只能读或者只能写；通道可以实现异步读写数据；通道可以从缓冲读数据，也可以写数据到缓冲。

2）**BIO** 中的 **stream** 是单向的，例如 **FileInputStream** 对象只能进行读取数据的操作，而 **NIO** 中的通道(**Channel**)是双向的，可以读操作，也可以写操作。

3）**Channel** 在**NIO**中是一个接口 `public interface Channel extends Closeable{}`，常用的**Channel**实现类有：

- **FileChannel**：用于读取、写入、映射和操作文件的通道。
- **DatagramChannel**：通过 **UDP** 读写网络中的数据。
- **SocketChannel**：通过 **TCP** 读写网络中的数据。
- **ServerSocketChannel**：可以监听新进来的 **TCP** 连接，对每一个新进来的连接都会创建一个 **SocketChannel**。 

> 注意：ServerSocketChannel 类似 ServerSocket , SocketChannel 类似 Socket

`FileChannel` 用于文件的数据读写，`DatagramChannel` 用于 `UDP` 的数据读写，`ServerSocketChannel` 和 `SocketChannel` 用于 `TCP` 的数据读写。

`FileChannel` 主要用来对本地文件进行 `IO` 操作，常见的方法有

- `public int read(ByteBuffer dst)`，从通道读取数据并放到缓冲区中
- `public int write(ByteBuffer src)`，把缓冲区的数据写到通道中
- `public long transferFrom(ReadableByteChannel src, long position, long count)`，从目标通道中复制数据到当前通道
- `public long transferTo(long position, long count, WritableByteChannel target)`，把数据从当前通道复制给目标通道

**使用案例：**

案例一：使用前面学习后的 `ByteBuffer`（缓冲）和 `FileChannel`（通道），将 "hello，world！" 写入到 `file01.txt` 中

```java
/**
 * 使用Buffer和FileChannel，将 ”hello，world！“ 写入到file.txt中
 */
public class FileChannelCase {
    public static void main(String[] args) throws IOException {
        String str = "hello，world！";

        // 创建一个文件输出流
        FileOutputStream fileOutputStream = new FileOutputStream("d:\file.txt");

        // 通过 fileOutputStream 获得对应的 FileChannel
        FileChannel channel = fileOutputStream.getChannel();

        // 创建一个缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(1024);

        // 将数据写入缓冲区
        buffer.put(str.getBytes());

        // 转化Buffer为读取模式
        buffer.flip();

        // 将Buffer中的数据写入到channel
        channel.write(buffer);

        // 关闭文件流，通道会随之关闭
        fileOutputStream.close();
    }
}
```

案例二：使用 `FileChannel`（通道）和方法 `read、write`，完成文件的拷贝，拷贝一个文本文件 `1.txt`，放在项目下即可

```java
package com.wotzc.nio;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class NIOFileChannel03 {

    public static void main(String[] args) throws Exception {

        FileInputStream fileInputStream = new FileInputStream("1.txt");
        FileChannel fileChannel01 = fileInputStream.getChannel();
        FileOutputStream fileOutputStream = new FileOutputStream("2.txt");
        FileChannel fileChannel02 = fileOutputStream.getChannel();

        ByteBuffer byteBuffer = ByteBuffer.allocate(512);
        
        while (true) { //循环读取

            //这里有一个重要的操作，一定不要忘了
            /*
            public final Buffer clear() {
                position = 0;
                limit = capacity;
                mark = -1;
                return this;
            }
            */
            byteBuffer.clear(); //清空 buffer
            int read = fileChannel01.read(byteBuffer);
            System.out.println("read = " + read);
            if (read == -1) { //表示读完
                break;
            }

            //将 buffer 中的数据写入到 fileChannel02--2.txt
            byteBuffer.flip();
            fileChannel02.write(byteBuffer);
        }

        //关闭相关的流
        fileInputStream.close();
        fileOutputStream.close();
    }
}
```

### Selector（选择器）

**Java** 的 **NIO**，用非阻塞的 IO 方式。可以用一个线程，处理多个的客户端连接，就会使用到 **Selector(选择器)**。

**Selector 能够检测多个注册的通道上是否有事件发生(注意:多个 Channel 以事件的方式可以注册到同一个Selector)**，如果有事件发生，便获取事件然后针对每个事件进行相应的处理。这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求。

只有在 连接/通道 真正有读写事件发生时，才会进行读写，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程。避免了多线程之间的上下文切换导致的开销。

Selector类是一个抽象类，有如下相关方法：

- `Selector open()`：得到一个选择器对象；
- `int select(long timeout)`：监控所有注册的通道，当其中有IO操作可以进行时，将对应的**SelectionKey**加入内部集合并返回。参数用来设置超时时间（可以不设置）。
- `Set<SeletionKey> selectedKeys()`：从内部集合中得到所有的**SeletionKey**。

**Selector** 相关方法说明

- `selector.select();` //  调用它会一直阻塞，直到获取注册到的**Selector**中的**channel**至少有一个**channel**发生它所关心的事件才返回，返回的是发生事件的**channel**的**SelectionKey**。
- `selector.select(1000);` // 阻塞 1000 毫秒，在 1000 毫秒后返回
- `selector.wakeup();` // 唤醒 selector
- `selector.selectNow();` // 不阻塞，立马返还

####  NIO 非阻塞网络编程原理分析图

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/chapter03_22.png" style="zoom:67%;" />

NIO网络通信基本流程：

1）当客户端连接时，会通过 **ServerSocketChannel** （**ServerSocketChannel**也需要注册到**selector**上）得到 **SocketChannel**；

2）**Selector** 进行监听 ，通过 `select()` 方法, 返回有事件发生的通道的个数；

3）**socketChannel**调用 `register(Selector sel, int ops)`方法，注册到**Selector**上，一个**selector**上可以注册多个**SocketChannel**；

4）**socketChannel**注册成功后会返回一个 **SelectionKey**，用于和该**Selector** 关联，多个**socketChannel**注册成功后就会有一个 **SelectionKey** 集合；

5）**Selector**使用`select()`方法进行监听，会返回有事件发生的通道的个数，进一步可以得到对应的**SeletionKey**（有事件发生的）。

6）通过**SeletionKey**的`channel()`方法反向获得**SocketChannel**，通过**channel**通道完成对应的业务处理。

#### SelectionKey

**SelectionKey**表示**Selector**和网络通道的注册关系，共四种：

- `int OP_ACCEPT`：有新的网络连接可以 `accept`，值为 `16`
- `int OP_CONNECT`：代表连接已经建立，值为 `8`
- `int OP_READ`：代表读操作，值为 `1`
- `int OP_WRITE`：代表写操作，值为 `4`

源码中：

```java
public static final int OP_READ = 1 << 0;
public static final int OP_WRITE = 1 << 2;
public static final int OP_CONNECT = 1 << 3;
public static final int OP_ACCEPT = 1 << 4;
```

**SelectionKey**相关方法

```java
public abstract class SelectionKey {
	public abstract Selector selector();//得到与之关联的 Selector 对象
	public abstract SelectableChannel channel();//得到与之关联的通道
	public final Object attachment();//得到与之关联的共享数据
	public abstract SelectionKey interestOps(int ops);//设置或改变监听事件
	public final boolean isAcceptable();//是否可以 accept
	public final boolean isReadable();//是否可以读
	public final boolean isWritable();//是否可以写
}
```

#### ServerSocketChanne

**ServerSocketChannel**在服务端监听新的客户端**Socket**连接。相关方法：

```java
public abstract class ServerSocketChannel extends AbstractSelectableChannel implements NetworkChannel {
	// 得到一个 ServerSocketChannel 通道
	public static ServerSocketChannel open()
	// 设置服务器端端口号
	public final ServerSocketChannel bind(SocketAddress local)
	// 设置阻塞或非阻塞模式，取值 false 表示采用非阻塞模式
	public final SelectableChannel configureBlocking(boolean block)
	// 接受一个连接，返回代表这个连接的通道对象
	public SocketChannel accept()
	// 注册一个选择器并设置监听事件
	public final SelectionKey register(Selector sel, int ops)
}
```

#### SocketChannel

**SocketChannel**，网络 **IO** 通道，具体负责进行读写操作。**NIO** 把缓冲区的数据写入通道，或者把通道里的数据读到缓冲区。

```java
public abstract class SocketChannel extends AbstractSelectableChannel implements ByteChannel, ScatteringByteChannel, GatheringByteChannel, NetworkChannel {
	// 得到一个 SocketChannel 通道
	public static SocketChannel open();
	// 设置阻塞或非阻塞模式，取值 false 表示采用非阻塞模式
	public final SelectableChannel configureBlocking(boolean block);
	// 连接服务器
	public boolean connect(SocketAddress remote);
	// 如果上面的方法连接失败，接下来就要通过该方法完成连接操作
	public boolean finishConnect();
	// 往通道里写数据
	public int write(ByteBuffer src);
	// 从通道里读数据
	public int read(ByteBuffer dst);
	// 注册一个选择器并设置监听事件，最后一个参数可以设置共享数据
	public final SelectionKey register(Selector sel, int ops, Object att);
	// 关闭通道
	public final void close();
}
```

## NIO 网络编程应用实例 - 群聊系统

1. 编写一个 `NIO` 群聊系统，实现服务器端和客户端之间的数据简单通讯（非阻塞）
2. 实现多人群聊
3. 服务器端：可以监测用户上线，离线，并实现消息转发功能
4. 客户端：通过 `Channel` 可以无阻塞发送消息给其它所有用户，同时可以接受其它用户发送的消息（有服务器转发得到）

**思路**

1. 先编写服务器端

   - 服务器启动并监听6667
   - 服务器接受客户端信息，并实现转发[处理上线和离线]

2. 编写客户端

   - 连接服务器
   - 发送消息
   - 接受服务器消息


**服务端：**

```java
package com.wotzc.nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;

public class NIOServer {

    //定义属性
    private Selector selector;
    private ServerSocketChannel serverSocketChannel;
    private static final int PORT = 6667;

    public NIOServer() {
        try {
            // 得到选择器
            selector = Selector.open();
            // ServerSocketChannel
            serverSocketChannel = ServerSocketChannel.open();
            // 绑定端口
            serverSocketChannel.socket().bind(new InetSocketAddress(PORT));
            //设置非阻塞模式
            serverSocketChannel.configureBlocking(false);
            //将该 serverSocketChannel 注册到 selector
            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        NIOServer server = new NIOServer();
        server.listen();
    }


    private void listen() {

        try {
            while (true) {
                int count = selector.select();
                if (count > 0) {
                    //有事件处理
                    Iterator<SelectionKey> selectionKeys = selector.selectedKeys().iterator();
                    if (selectionKeys.hasNext()) {
                        // 取出selectionkey
                        SelectionKey selectionKey = selectionKeys.next();
                        // 监听到accept
                        if (selectionKey.isAcceptable()) {
                            SocketChannel socketChannel = serverSocketChannel.accept();
                            socketChannel.configureBlocking(false);
                            // 将该socketChannel注册到selector
                            socketChannel.register(selector, SelectionKey.OP_READ);
                            // 提示有客户端上线
                            System.out.println(socketChannel.getRemoteAddress() + "上线 ");
                        }
                        if (selectionKey.isReadable()) {
                            //通道发送read事件，即通道是可读的状态
                            readData(selectionKey);
                        }
                        // 删除当前的key，防止重复处理
                        selectionKeys.remove();
                    }
                } else {
                    System.out.println("等待....");
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 读取客户端的信息
    private void readData(SelectionKey selectionKey) {
        // 取出channel
        SocketChannel channel = (SocketChannel) selectionKey.channel();
        // 创建buffer
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        try {
            int conut = channel.read(byteBuffer);
            // count != -1 表示没有读完
            if (conut != -1) {
                // 把缓存区的数据转成字符串
                String message = new String(byteBuffer.array());
                //输出该消息
                System.out.println("form客户端:" + message);
                // 向其它的客户端转发消息(去掉自己),专门写一个方法来处理
                sendInfoToOtherClients(message, channel);
            }
        } catch (IOException e) {
            try {
                System.out.println(channel.getRemoteAddress() + "离线了...");
                //取消注册
                selectionKey.cancel();
                //关闭通道
                channel.close();
            } catch (IOException e2) {
                e2.printStackTrace();
            }
        }
    }

    // 转发消息给其它客户端(通道)
    private void sendInfoToOtherClients(String message, SocketChannel channel) throws IOException {
        System.out.println("服务器转发消息中...");
        // 遍历所有注册到selector上的SocketChannel，并排除自身
        for (SelectionKey key : selector.keys()) {
            //通过 key 取出对应的 SocketChannel
            Channel targetChannel = key.channel();
            if (targetChannel instanceof SocketChannel && targetChannel != channel) {
                // 类型转换
                SocketChannel socketChannel = (SocketChannel) targetChannel;
                // 将message转存到buffer
                ByteBuffer byteBuffer = ByteBuffer.wrap(message.getBytes());
                // 将buffer的数据写入通道
                socketChannel.write(byteBuffer);
            }
        }
    }

}
```

**客户端：**

```java
package com.wotzc.nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Scanner;

public class NIOClient {

    private Selector selector;
    public static final String HOST = "127.0.0.1";
    public static final int PORT = 6667;
    private String username;
    private SocketChannel socketChannel;


    public NIOClient() throws IOException {
        // 得到选择器
        selector = Selector.open();
        // 连接服务器
        socketChannel = SocketChannel.open(new InetSocketAddress(HOST, PORT));
        // 设置非阻塞
        socketChannel.configureBlocking(false);
        // 将channel注册到selector
        socketChannel.register(selector, SelectionKey.OP_READ);
        // 得到username
        username = socketChannel.getLocalAddress().toString();
        System.out.println(username + " is ok...");
    }

    public static void main(String[] args) throws IOException {
        // 启动客户端
        NIOClient nioClient = new NIOClient();
        // 启动一个线程,每个3 秒，读取从服务器发送消息
        new Thread(() -> {
            while (true) {
                try {
                    nioClient.readMessage();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        // 发送数据给服务器端
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String s = scanner.next();
            nioClient.sendMessage(s);
        }

    }

    void readMessage() throws IOException {
        int count = selector.select();
        if (count > 0) { // 有可以用的通道
            Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
            if (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                if (key.isReadable()) {
                    // 通过key得到channel
                    SocketChannel channel = (SocketChannel) key.channel();
                    // 新建buffer
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    // 读取数据到buffer中
                    channel.read(buffer);
                    //把读到的缓冲区的数据转成字符串
                    String message = new String(buffer.array());
                    System.out.println(message);
                }
                iterator.remove();
            }
        }
    }

    void sendMessage(String message) {
        message = username + " 说：" + message;
        try {
            socketChannel.write(ByteBuffer.wrap(message.getBytes()));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

----

## NIO 与零拷贝

### 零拷贝基本介绍

零拷⻉（Zero-copy）技术指在计算机执⾏操作时，CPU 不需要先将数据从⼀个内存区域复制到另⼀个内存区域，从⽽可以减少上下⽂切换以及 CPU 的拷⻉时间。它的作⽤是在数据从⽹络设备到⽤户程序空间传递的过程中，减少数据拷⻉次数，减少系统调⽤，实现 CPU 的零参与，彻底消除 CPU 在这⽅⾯的负载。

实现零拷⻉⽤到的最主要技术是 DMA 数据传输技术和内存区域映射技术。

零拷⻉机制可以减少数据在内核缓冲区和⽤户进程缓冲区之间反复的 I/O 拷⻉操作。零拷⻉机制可以减少⽤户进程地址空间和内核地址空间之间因为上下⽂切换⽽带来的 CPU 开销。在 Java 程序中，常⽤的零拷⻉有 mmap（内存映射）和 sendFile。

### 基本概念介绍

- **用户态：** 用户程序运行的环境，也是用户与操作系统交互的界面。在用户态中，用户程序可以访问系统资源，如文件、网络、内存等，但是不能直接访问硬件设备。用户态程序通过系统调用来请求内核提供服务，如文件读写、进程管理等。
- **内核态：** 操作系统的核心部分，负责管理系统的硬件资源和提供基本的系统服务。在内核态中，操作系统可以直接访问硬件设备，并控制它们的运行。内核态具有较高的执行权限和特权级别，可以执行一些操作系统的核心功能，如进程调度、内存管理、设备驱动等。
- **上下文切换：** 用户态和内核态之间通过系统调用来进行通信和交互。用户程序请求操作系统提供服务时，会通过系统调用进入内核态执行相应的操作，内核态执行完操作后再返回到用户态。这种分离的设计可以保护系统的稳定性和安全性，同时也方便了系统的管理和维护。
- **管道**：Linux的管道是一种将一个命令的输出作为另一个命令的输入的机制以实现数据流的传递和处理。
- **DMA**：（Direct Memory Access）又称直接内存存取，也就是通过硬件直接访问主内存，不需要通过 cpu，有点像协处理器，目前大多数的硬件都支持 DMA，例如网卡、显卡、声卡、磁盘等等。

### ⽤户空间和内核空间

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/kernel-space.png" style="zoom:67%;" />

### 传统IO方式

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/traditional-io-streams.png)

**DMA**：`direct memory access` 直接内存拷贝（不使用 `CPU`）

传统 I/O 操作的数据读写流程，整个过程涉及 2 次 CPU 拷⻉、2 次 DMA 拷⻉总共 4 次拷⻉，以及 4 次上下⽂切换。

### mmap 优化

`mmap` 通过内存映射，将文件映射到内核缓冲区，同时，用户空间可以共享内核空间的数据。这样，在进行网络传输时，就可以减少内核空间到用户空间的拷贝次数。如下图

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/mmap-stream.png)

 　基于 `mmap` + `write` 系统调⽤的零拷⻉⽅式，整个拷⻉过程会发⽣ 4 次上下⽂切换，1 次 CPU 拷⻉和 2次 DMA 拷⻉，⽤户程序读写数据的流程如下：

1. ⽤户进程通过 `mmap()` 函数向内核（**kernel**）发起系统调⽤，上下⽂从⽤户态（**user space**）切换为内核态（**kernel space**）。
2. 将⽤户进程的内核空间的读缓冲区（**read buffer**）与⽤户空间的缓存区（**user buffer**）进⾏内存地址映射。
3. **CPU**利⽤**DMA**控制器将数据从主存或硬盘拷⻉到内核空间（**kernel space**）的读缓冲区（**readbuffer**）。
4. 上下⽂从内核态（**kernel space**）切换回⽤户态（**user space**），**mmap** 系统调⽤执⾏返回。
5. ⽤户进程通过 `write()` 函数向内核（**kernel**）发起系统调⽤，上下⽂从⽤户态（**user space**）切换为内核态（**kernel space**）。
6. `CPU`将读缓冲区（**read buffer**）中的数据拷⻉到的⽹络缓冲区（**socket buffer**）。
7. **CPU**利⽤**DMA**控制器将数据从⽹络缓冲区（**socket buffer**）拷⻉到⽹卡进⾏数据传输。
8. 上下⽂从内核态（**kernel space**）切换回⽤户态（**user space**），`write` 系统调⽤执⾏返回。

　　mmap 主要的⽤处是提⾼ I/O 性能，特别是针对⼤⽂件。对于⼩⽂件，内存映射⽂件反⽽会导致碎⽚空间的浪费，因为内存映射总是要对⻬⻚边界，最⼩单位是 4 KB，⼀个 5 KB 的⽂件将会映射占⽤ 8KB 内存，也就会浪费 3 KB 内存。

　　*mmap 的拷⻉虽然减少了 1 次拷⻉，提升了效率，但也存在⼀些隐藏的问题。当 mmap ⼀个⽂件**时，如果这个⽂件被另⼀个进程所截获，那么 write 系统调⽤会因为访问⾮法地址被 SIGBUS 信号终**⽌，SIGBUS 默认会杀死进程并产⽣⼀个 coredump，服务器可能因此被终⽌。*

### sendFile 优化

`Linux2.1` 版本提供了 `sendFile` 函数，其基本原理如下：数据根本不经过用户态，直接从内核缓冲区进入到 `SocketBuffer`，同时，由于和用户态完全无关，就减少了一次上下文切换

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/sendfile-stream.png)

提示：零拷贝从操作系统角度，是没有 `cpu` 拷贝

`Linux在2.4` 版本中，做了一些修改，避免了从内核缓冲区拷贝到 `Socketbuffer` 的操作，直接拷贝到协议栈，从而再一次减少了数据拷贝。具体如下图和小结：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/sendfile-stream1.png)

这里其实有一次 `cpu` 拷贝 `kernel buffer` -> `socket buffer` 但是，拷贝的信息很少，比如 `lenght`、`offset` 消耗低，可以忽略

### 深入理解

1. 零拷⻉是从操作系统⻆度来说的，因为内核缓冲区之前，没有数据的重复（只有 **kernel buffer** 有⼀份数据）。
2. 零拷⻉不仅仅带来了更少的数据复制，还能带来其他的性能优势，例如：更少的上下⽂切换，更少的 **CPU** 缓存伪共享以及⽆ **CPU** 校验和计算。

### mmap 和 sendFile 的区别

- `mmap` 适合⼩数据量读写，`sendFile` 适合⼤⽂件传输
- `mmap` 需要 4 次上下⽂切换、3 次数据拷⻉；`sendFile` 需要 3 次上下⽂切换、最少 2 次数据拷⻉。
- `sendFile` 可以利⽤ **DMA** ⽅式，减少 **CPU** 拷⻉，`mmap` 则不能（必须从内核拷⻉到 `socket` 缓冲区）。
- 在这个选择上：**RocketMQ** 在消费消息时，使⽤了 `mmap`。**kafka** 使⽤了 `sendFIle`。

### NIO 零拷贝案例

案例要求：

1. 使用传统的 `IO` 方法传递一个大文件
2. 使用 `NIO` 零拷贝方式传递（`transferTo`）一个大文件
3. 看看两种传递方式耗时时间分别是多少

**服务器**

```java
package com.wotzc.nio;

import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

//服务器
public class NewIOServer {

    public static void main(String[] args) throws Exception {
        InetSocketAddress address = new InetSocketAddress(7001);
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        ServerSocket serverSocket = serverSocketChannel.socket();
        serverSocket.bind(address);

        //创建buffer
        ByteBuffer byteBuffer = ByteBuffer.allocate(4096);

        while (true) {
            SocketChannel socketChannel = serverSocketChannel.accept();
            int readcount = 0;
            while (-1 != readcount) {
                try {
                    readcount = socketChannel.read(byteBuffer);
                } catch (Exception ex) {
                    // ex.printStackTrace();
                    break;
                }
                //
                byteBuffer.rewind(); //倒带 position = 0 mark 作废
            }
        }
    }
}
```

**传统IO方式**

```java
package com.wotzc.nio;

import java.io.File;
import java.io.RandomAccessFile;
import java.net.InetSocketAddress;
import java.nio.channels.SocketChannel;

public class TraditionalIOClient {

    public static void main(String[] args) throws Exception {
        SocketChannel socketChannel = SocketChannel.open();
        socketChannel.connect(new InetSocketAddress("localhost", 7001));
        String filename = "/Users/mac7/IdeaProjects/NettyTutorial/src/main/resources/flame-piper.2016.1080p.bluray.x264[PRiME].mkv";

        File file = new File(filename);
        RandomAccessFile raf = new RandomAccessFile(file, "rw");
        byte[] arr = new byte[(int) file.length()];
        raf.read(arr);
        //准备发送
        long startTime = System.currentTimeMillis();
        socketChannel.socket().getOutputStream().write(arr);
        System.out.println("发送的总的字节数 = " + arr.length + " 耗时: " + (System.currentTimeMillis() - startTime));

    }

}
```

**Out:**

```
发送的总的字节数 = 521072679 耗时: 542
```

**NIO 零拷贝方式**

```java
package com.wotzc.nio;

import java.io.FileInputStream;
import java.net.InetSocketAddress;
import java.nio.channels.FileChannel;
import java.nio.channels.SocketChannel;

public class NewIOClient {

    public static void main(String[] args) throws Exception {
        SocketChannel socketChannel = SocketChannel.open();
        socketChannel.connect(new InetSocketAddress("localhost", 7001));
        String filename = "/Users/mac7/IdeaProjects/NettyTutorial/src/main/resources/flame-piper.2016.1080p.bluray.x264[PRiME].mkv";
        //得到一个文件channel
        FileChannel fileChannel = new FileInputStream(filename).getChannel();
        //准备发送
        long startTime = System.currentTimeMillis();
        //在 linux 下一个 transferTo 方法就可以完成传输
        //在 windows 下一次调用 transferTo 只能发送 8m, 就需要分段传输文件,而且要主要
        //传输时的位置=》课后思考...
        //transferTo 底层使用到零拷贝
        long transferCount = fileChannel.transferTo(0, fileChannel.size(), socketChannel);
        System.out.println("发送的总的字节数 = " + transferCount + " 耗时: " + (System.currentTimeMillis() - startTime));

        //关闭
        fileChannel.close();
    }
}
```

**Out:**

```
发送的总的字节数 = 521072679 耗时: 245
```

## Java AIO 基本介绍

1. `JDK7` 引入了 `AsynchronousI/O`，即 `AIO`。在进行 `I/O` 编程中，常用到两种模式：`Reactor` 和 `Proactor`。`Java` 的 `NIO` 就是 `Reactor`，当有事件触发时，服务器端得到通知，进行相应的处理
2. `AIO` 即 `NIO2.0`，叫做异步不阻塞的 `IO`。`AIO` 引入异步通道的概念，采用了 `Proactor` 模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用
3. 目前 `AIO` 还没有广泛应用，`Netty` 也是基于 `NIO`，而不是 `AIO`，因此我们就不详解 `AIO` 了，有兴趣的同学可以参考[《Java新一代网络编程模型AIO原理及Linux系统AIO介绍》](http://www.52im.net/thread-306-1-1.html)

## BIO、NIO、AIO 对比表

|          | BIO      | NIO                    | AIO        |
| -------- | -------- | ---------------------- | ---------- |
| IO模型   | 同步阻塞 | 同步非阻塞（多路复用） | 异步非阻塞 |
| 编程难度 | 简单     | 复杂                   | 复杂       |
| 可靠性   | 差       | 好                     | 好         |
| 吞吐量   | 低       | 高                     | 高         |

**举例说明**

1. 同步阻塞：到理发店理发，就一直等理发师，直到轮到自己理发。
2. 同步非阻塞：到理发店理发，发现前面有其它人理发，给理发师说下，先干其他事情，一会过来看是否轮到自己.
3. 异步非阻塞：给理发师打电话，让理发师上门服务，自己干其它事情，理发师自己来家给你理发

---
title: Java BIO 编程
date: 2025-01-13 11:06:57
tags:
- BIO
- Netty
categories: Netty
---

## 什么是IO模型？

I/O 模型：就是用什么样的通道或者说是通信模式和架构进行数据的传输和接收，很大程度上决定了程序通信的性能。

在Java中，主要有三种IO模型，分别是同步阻塞IO (**BIO-Blocking I/O**)、同步非阻塞IO (**NIO-non-blocking I/O**)和异步非阻塞IO (**AIO-Asynchronous I/O**)。

先来理解这三者之间的区别：

- **同步阻塞**：服务器在连接客户端时，如果当前没有客户端请求连接，服务器就需要一直等待，阻塞在这里。同理，服务器在接收客户端数据的时候，如果客户端一直没有发送过来，服务器也需要一直等待。
- **同步非阻塞**：服务器在连接客户端时，如果当前没有客户端请求连接，服务器可以先做其它事情，等一会再来看是否有连接请求。同理，服务器在接收客户端数据的时候，如果客户端一直没有发送过来，服务器可以先处理其它客户端的业务，但仍需要主动检查客户端有没有发送数据。
- **异步非阻塞**：服务器在连接客户端时，如果当前没有客户端请求连接，服务器可以先做其它事情，当客户端发送了IO请求，os先完成（或监听器）就会通知服务器去进行处理。同理，服务器在接收数据时，也无需等待，当有客户端发送了数据，os（或监听器）会通知服务器去接收数据。

**BIO** 为同步阻塞，一个连接一个线程，资源要求高，适用于连接少且固定的架构。

**NIO** 是同步非阻塞，有 **Channel**、**Buffer**、**Selector** 三大核心，通过多路复用技术，一个线程可处理多个操作，适用于连接多且短的架构。

**AIO** 是异步非阻塞，采用 **Proactor** 模式，适用于连接多且长的架构。

## Java BIO基本介绍

1. **Java BIO**就是传统的**Java IO**编程，其相关的类和接口在**java.io**包下。
2. **BIO**（**Blocking I/O**）：同步阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理。如果这个连接不做任何事情会造成不必要的线程开销，可以通过线程池机制改善。
3. **BIO**方式适用于数据数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，**JDK1.4**以前的唯一选择，程序简单易理解。

## Java BIO 工作机制

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2025/netty/chapter02_03.png" style="zoom:67%;" />

### BIO编程简单流程

1. 服务器启动一个**ServerSocket**。
2. 客户端启动一个**Socket**对服务器进行通信，默认情况下，服务器端需要对每一个客户端建立一个线程与之通信。
3. 客户端发出请求后，先咨询服务器是否有线程相应，如果没有则会等待，或者被拒绝。
4. 如果有响应，客户端线程会等待请求结束后，再继续执行。

## Java BIO应用实例

### 实例说明

1. 使用**BIO**模型编写一个服务器端，监听**6666**端口，当有客户端连接时，就启动一个线程与之通讯。
2. 要求使用线程池机制改善，可以连接多个客户端。
3. 服务端可以接受客户端发送的数据。

### 代码实现

下面是服务端代码

```java
package com.wotzc.bio;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class BIOServer {

    public static void main(String[] args) throws IOException {
        // 创建线程池
        ExecutorService executorService = Executors.newCachedThreadPool();
        // 创建ServerSocket并且监听6666端口
        ServerSocket serverSocket = new ServerSocket(6666);
        while (true) {
            // 监听---一直等待客户端连接
            Socket socket = serverSocket.accept();
            // 连接来了之后，启用一个线程去执行里面的方法
            executorService.execute(() -> {
                try {
                    // 获取客户端发送过来的输入流
                    InputStream inputStream = socket.getInputStream();
                    byte[] bytes = new byte[1024];
                    int read = inputStream.read(bytes);
                    // 读取发送过来的信息并打印
                    if (read != -1) {
                        System.out.println(new String(bytes, 0, read));
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    // 断开通讯
                    try {
                        socket.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
    }
}
```

下面是客户端代码

```java
package com.wotzc.bio;

import java.io.IOException;
import java.io.OutputStream;
import java.net.Socket;

public class BIOClient {

    public static void main(String[] args) throws IOException {
        // 创建一个socket，并绑定本地ip及6666端口
        Socket socket = new Socket("127.0.0.1", 6666);
        // 获取output流
        OutputStream outputStream = socket.getOutputStream();
        // 向输出流中写入
        outputStream.write("Hello World!".getBytes());
        outputStream.flush();
        // 关闭连接
        socket.close();
    }

}
```

通过以上代码能够看到：在服务端的控制台中能有信息打印出来，且能得出结论：如果客户端一直没有请求发送，则服务端一直在等待；如果发送过来的数据是空的话，就会引起线程的消耗。

上述服务端可以通过线程池机制改善(实现多个客户连接服务器)。

实例说明：

1. 使用 `BIO` 模型编写一个服务器端，监听 `6666` 端口，当有客户端连接时，就启动一个线程与之通讯。
2. 要求使用线程池机制改善，可以连接多个客户端。
3. 服务器端可以接收客户端发送的数据（`telnet` 方式即可）。
4. 代码演示：

```java
package com.wotzc.bio;

import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class BIOServer {

    public static void main(String[] args) throws Exception {
        //线程池机制
        //思路
        //1. 创建一个线程池
        //2. 如果有客户端连接，就创建一个线程，与之通讯(单独写一个方法)
        ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
        //创建ServerSocket
        ServerSocket serverSocket = new ServerSocket(6666);
        System.out.println("服务器启动了");
        while (true) {
            System.out.println("线程信息id = " + Thread.currentThread().getId() + "名字 = " + Thread.currentThread().getName());
            //监听，等待客户端连接
            System.out.println("等待连接....");
            final Socket socket = serverSocket.accept();
            System.out.println("连接到一个客户端");
            //就创建一个线程，与之通讯(单独写一个方法)
            newCachedThreadPool.execute(new Runnable() {
                public void run() {//我们重写
                    //可以和客户端通讯
                    handler(socket);
                }
            });
        }
    }

    //编写一个handler方法，和客户端通讯
    public static void handler(Socket socket) {
        try {
            System.out.println("线程信息id = " + Thread.currentThread().getId() + "名字 = " + Thread.currentThread().getName());
            byte[] bytes = new byte[1024];
            //通过socket获取输入流
            InputStream inputStream = socket.getInputStream();
            //循环的读取客户端发送的数据
            while (true) {
                System.out.println("线程信息id = " + Thread.currentThread().getId() + "名字 = " + Thread.currentThread().getName());
                System.out.println("read....");
                int read = inputStream.read(bytes);
                if (read != -1) {
                    System.out.println(new String(bytes, 0, read));//输出客户端发送的数据
                } else {
                    break;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println("关闭和client的连接");
            try {
                socket.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

}
```

**out**:

```
服务器启动了
线程信息id = 1名字 = main
等待连接....
连接到一个客户端
线程信息id = 1名字 = main
等待连接....
线程信息id = 12名字 = pool-1-thread-1
线程信息id = 12名字 = pool-1-thread-1
read....
Hello World!
线程信息id = 12名字 = pool-1-thread-1
read....
关闭和client的连接
```

## Java BIO问题分析

1. 每个请求都需要创建独立的线程，与对应的客户端进行数据**读**，业务处理，然后再数据**写**。
2. 当并发数较大时，需要创建大量的线程来处理连接，系统资源占用较大。
3. 连接建立后，如果当前线程暂时没有数据可读，则线程就阻塞在**读**操作上，造成线程资源浪费。

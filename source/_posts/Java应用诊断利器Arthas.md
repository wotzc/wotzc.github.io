---
title: Arthas基础教程
date: 2024-01-03 16:18:54
tags: 
- Arthas
- Java诊断工具
- 线上问题排查
- JVM
- 反编译
categories: Java诊断工具
---

# 简介

Arthas 是一款线上监控诊断产品，通过全局视角实时查看应用 load、内存、gc、线程的状态信息，并能在不修改应用代码的情况下，对业务问题进行诊断，包括查看方法调用的出入参、异常，监测方法执行耗时，类加载信息等，大大提升线上问题排查效率。

# 背景

通常，本地开发环境无法访问生产环境。如果在生产环境中遇到问题，则无法使用 IDE 远程调试。更糟糕的是，在生产环境中调试是不可接受的，因为它会暂停所有线程，导致服务暂停。

开发人员可以尝试在测试环境或者预发环境中复现生产环境中的问题。但是，某些问题无法在不同的环境中轻松复现，甚至在重新启动后就消失了。

如果您正在考虑在代码中添加一些日志以帮助解决问题，您将必须经历以下阶段：测试、预发，然后生产。这种方法效率低下，更糟糕的是，该问题可能无法解决，因为一旦 JVM 重新启动，它可能无法复现，如上文所述。

Arthas 旨在解决这些问题。开发人员可以在线解决生产问题。无需 JVM 重启，无需代码更改。 Arthas 作为观察者永远不会暂停正在运行的线程。

# Arthas（阿尔萨斯）能为你做什么？

`Arthas` 是 Alibaba 开源的 Java 诊断工具，深受开发者喜爱。

当你遇到以下类似问题而束手无策时，`Arthas`可以帮助你解决：

1. 这个类从哪个 jar 包加载的？为什么会报各种类相关的 Exception？
2. 我改的代码为什么没有执行到？难道是我没 commit？分支搞错了？
3. 遇到问题无法在线上 debug，难道只能通过加日志再重新发布吗？
4. 线上遇到某个用户的数据处理有问题，但线上同样无法 debug，线下无法重现！
5. 是否有一个全局视角来查看系统的运行状况？
6. 有什么办法可以监控到 JVM 的实时运行状态？
7. 怎么快速定位应用的热点，生成火焰图？
8. 怎样直接从 JVM 内查找某个类的实例？

`Arthas` 支持 JDK 6+，支持 Linux/Mac/Windows，采用命令行交互模式，同时提供丰富的 `Tab` 自动补全功能，进一步方便进行问题的定位和诊断。

# 安装启动Arthas

在命令行下面执行（使用和目标进程一致的用户启动，否则可能 attach 失败）：

```bash
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

如果下载速度比较慢，可以使用 aliyun 的镜像：`java -jar arthas-boot.jar --repo-mirror aliyun --use-http`

选择应用 java 进程：

```bash
$ $ java -jar arthas-boot.jar
* [1]: 35542
  [2]: 71560 math-game.jar
```

`math-game`进程是第 2 个，则输入 2，再输入`回车/enter`。Arthas 会 attach 到目标进程上，并输出日志：

```bash
[INFO] Try to attach process 71560
[INFO] Attach process 71560 success.
[INFO] arthas-client connect 127.0.0.1 3658
  ,---.  ,------. ,--------.,--.  ,--.  ,---.   ,---.
 /  O  \ |  .--. ''--.  .--'|  '--'  | /  O  \ '   .-'
|  .-.  ||  '--'.'   |  |   |  .--.  ||  .-.  |`.  `-.
|  | |  ||  |\  \    |  |   |  |  |  ||  | |  |.-'    |
`--' `--'`--' '--'   `--'   `--'  `--'`--' `--'`-----'


wiki: https://arthas.aliyun.com/doc
version: 3.0.5.20181127201536
pid: 71560
time: 2018-11-28 19:16:24

```

# 表达式核心变量

无论是匹配表达式也好、观察表达式也罢，他们核心判断变量都是围绕着一个 Arthas 中的通用通知对象 `Advice` 进行。

它的简略代码结构如下

```java
public class Advice {

    private final ClassLoader loader;
    private final Class<?> clazz;
    private final ArthasMethod method;
    private final Object target;
    private final Object[] params;
    private final Object returnObj;
    private final Throwable throwExp;
    private final boolean isBefore;
    private final boolean isThrow;
    private final boolean isReturn;

    // getter/setter
}
```

这里列一个表格来说明不同变量的含义

|      变量名 | 变量解释                                                     |
| ----------: | :----------------------------------------------------------- |
|    `loader` | 本次调用类所在的 `ClassLoader`                               |
|     `clazz` | 本次调用类的 `Class` 引用                                    |
|    `method` | 本次调用方法反射引用                                         |
|    `target` | 本次调用类的实例                                             |
|    `params` | 本次调用参数列表，这是一个数组，如果方法是无参方法则为空数组 |
| `returnObj` | 本次调用返回的对象。当且仅当 `isReturn==true` 成立时候有效，表明方法调用是以正常返回的方式结束。如果当前方法无返回值 `void`，则值为 null |
|  `throwExp` | 本次调用抛出的异常。当且仅当 `isThrow==true` 成立时有效，表明方法调用是以抛出异常的方式结束。 |
|  `isBefore` | 辅助判断标记，当前的通知节点有可能是在方法一开始就通知，此时 `isBefore==true` 成立，同时 `isThrow==false` 和 `isReturn==false`，因为在方法刚开始时，还无法确定方法调用将会如何结束。 |
|   `isThrow` | 辅助判断标记，当前的方法调用以抛异常的形式结束。             |
|  `isReturn` | 辅助判断标记，当前的方法调用以正常返回的形式结束。           |

所有变量都可以在表达式中直接使用，如果在表达式中编写了不符合 OGNL 脚本语法或者引入了不在表格中的变量，则退出命令的执行；用户可以根据当前的异常信息修正`条件表达式`或`观察表达式`。

- 特殊用法请参考：[https://github.com/alibaba/arthas/issues/71在新窗口打开](https://github.com/alibaba/arthas/issues/71)
- OGNL 表达式官网：https://commons.apache.org/proper/commons-ognl/language-guide.html

# 命令列表

## jvm 相关

- [`dashboard`](https://arthas.aliyun.com/doc/dashboard.html) - 当前系统的实时数据面板
- [`getstatic`](https://arthas.aliyun.com/doc/getstatic.html) - 查看类的静态属性
- [`heapdump`](https://arthas.aliyun.com/doc/heapdump.html) - dump java heap, 类似 jmap 命令的 heap dump 功能
- [`jvm`](https://arthas.aliyun.com/doc/jvm.html) - 查看当前 JVM 的信息
- [`logger`](https://arthas.aliyun.com/doc/logger.html) - 查看和修改 logger
- [`mbean`](https://arthas.aliyun.com/doc/mbean.html) - 查看 Mbean 的信息
- [`memory`](https://arthas.aliyun.com/doc/memory.html) - 查看 JVM 的内存信息
- [`ognl`](https://arthas.aliyun.com/doc/ognl.html) - 执行 ognl 表达式
- [`perfcounter`](https://arthas.aliyun.com/doc/perfcounter.html) - 查看当前 JVM 的 Perf Counter 信息
- [`sysenv`](https://arthas.aliyun.com/doc/sysenv.html) - 查看 JVM 的环境变量
- [`sysprop`](https://arthas.aliyun.com/doc/sysprop.html) - 查看和修改 JVM 的系统属性
- [`thread`](https://arthas.aliyun.com/doc/thread.html) - 查看当前 JVM 的线程堆栈信息
- [`vmoption`](https://arthas.aliyun.com/doc/vmoption.html) - 查看和修改 JVM 里诊断相关的 option
- [`vmtool`](https://arthas.aliyun.com/doc/vmtool.html) - 从 jvm 里查询对象，执行 forceGc

## class/classloader 相关

- [`classloader`](https://arthas.aliyun.com/doc/classloader.html) - 查看 classloader 的继承树，urls，类加载信息，使用 classloader 去 getResource
- [`dump`](https://arthas.aliyun.com/doc/dump.html) - dump 已加载类的 byte code 到特定目录
- [`jad`](https://arthas.aliyun.com/doc/jad.html) - 反编译指定已加载类的源码
- [`mc`](https://arthas.aliyun.com/doc/mc.html) - 内存编译器，内存编译`.java`文件为`.class`文件
- [`redefine`](https://arthas.aliyun.com/doc/redefine.html) - 加载外部的`.class`文件，redefine 到 JVM 里
- [`retransform`](https://arthas.aliyun.com/doc/retransform.html) - 加载外部的`.class`文件，retransform 到 JVM 里
- [`sc`](https://arthas.aliyun.com/doc/sc.html) - 查看 JVM 已加载的类信息
- [`sm`](https://arthas.aliyun.com/doc/sm.html) - 查看已加载类的方法信息

## monitor/watch/trace 相关

{% note warning no-icon 注意 %}

请注意，这些命令，都通过字节码增强技术来实现的，会在指定类的方法中插入一些切面来实现数据统计和观测，因此在线上、预发使用时，请尽量明确需要观测的类、方法以及条件，诊断结束要执行 `stop` 或将增强过的类执行 `reset` 命令。

{% endnote %}

- [`monitor`](https://arthas.aliyun.com/doc/monitor.html) - 方法执行监控
- [`stack`](https://arthas.aliyun.com/doc/stack.html) - 输出当前方法被调用的调用路径
- [`trace`](https://arthas.aliyun.com/doc/trace.html) - 方法内部调用路径，并输出方法路径上的每个节点上耗时
- [`tt`](https://arthas.aliyun.com/doc/tt.html) - 方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测
- [`watch`](https://arthas.aliyun.com/doc/watch.html) - 方法执行数据观测

## profiler/火焰图

- [`profiler`](https://arthas.aliyun.com/doc/profiler.html) - 使用[async-profiler在新窗口打开](https://github.com/jvm-profiling-tools/async-profiler)对应用采样，生成火焰图
- [`jfr`](https://arthas.aliyun.com/doc/jfr.html) - 动态开启关闭 JFR 记录

## 鉴权

- [`auth`](https://arthas.aliyun.com/doc/auth.html) - 鉴权

## options

- [`options`](https://arthas.aliyun.com/doc/options.html) - 查看或设置 Arthas 全局开关

## 管道

Arthas 支持使用管道对上述命令的结果进行进一步的处理，如`sm java.lang.String * | grep 'index'`

- [`grep`](https://arthas.aliyun.com/doc/grep.html) - 搜索满足条件的结果
- plaintext - 将命令的结果去除 ANSI 颜色
- wc - 按行统计输出结果

## 后台异步任务

当线上出现偶发的问题，比如需要 watch 某个条件，而这个条件一天可能才会出现一次时，异步后台任务就派上用场了，详情请参考[这里](https://arthas.aliyun.com/doc/async.html)

- 使用 `>` 将结果重写向到日志文件，使用 `&` 指定命令是后台运行，session 断开不影响任务执行（生命周期默认为 1 天）
- `jobs` - 列出所有 job
- `kill` - 强制终止任务
- `fg` - 将暂停的任务拉到前台执行
- `bg` - 将暂停的任务放到后台执行

## 基础命令

- [`base64`](https://arthas.aliyun.com/doc/base64.html) - base64 编码转换，和 linux 里的 base64 命令类似
- [`cat`](https://arthas.aliyun.com/doc/cat.html) - 打印文件内容，和 linux 里的 cat 命令类似
- [`cls`](https://arthas.aliyun.com/doc/cls.html) - 清空当前屏幕区域
- [`echo`](https://arthas.aliyun.com/doc/echo.html) - 打印参数，和 linux 里的 echo 命令类似
- [`grep`](https://arthas.aliyun.com/doc/grep.html) - 匹配查找，和 linux 里的 grep 命令类似
- [`help`](https://arthas.aliyun.com/doc/help.html) - 查看命令帮助信息
- [`history`](https://arthas.aliyun.com/doc/history.html) - 打印命令历史
- [`keymap`](https://arthas.aliyun.com/doc/keymap.html) - Arthas 快捷键列表及自定义快捷键
- [`pwd`](https://arthas.aliyun.com/doc/pwd.html) - 返回当前的工作目录，和 linux 命令类似
- [`quit`](https://arthas.aliyun.com/doc/quit.html) - 退出当前 Arthas 客户端，其他 Arthas 客户端不受影响
- [`reset`](https://arthas.aliyun.com/doc/reset.html) - 重置增强类，将被 Arthas 增强过的类全部还原，Arthas 服务端关闭时会重置所有增强过的类
- [`session`](https://arthas.aliyun.com/doc/session.html) - 查看当前会话的信息
- [`stop`](https://arthas.aliyun.com/doc/stop.html) - 关闭 Arthas 服务端，所有 Arthas 客户端全部退出
- [`tee`](https://arthas.aliyun.com/doc/tee.html) - 复制标准输入到标准输出和指定的文件，和 linux 里的 tee 命令类似
- [`version`](https://arthas.aliyun.com/doc/version.html) - 输出当前目标 Java 进程所加载的 Arthas 版本号

--------

# Arthas 基础教程

## Dashboard

`dashboard` 命令可以查看当前系统的实时数据面板，`dashboard` 命令可以查看当前系统的实时数据面板。

```bash
$ dashboard
ID   NAME                           GROUP           PRIORITY   STATE     %CPU      DELTA_TIME TIME      INTERRUPTE DAEMON
-1   C2 CompilerThread0             -               -1         -         1.55      0.077      0:8.684   false      true
53   Timer-for-arthas-dashboard-07b system          5          RUNNABLE  0.08      0.004      0:0.004   false      true
22   scheduling-1                   main            5          TIMED_WAI 0.06      0.003      0:0.287   false      false
-1   C1 CompilerThread0             -               -1         -         0.06      0.003      0:2.171   false      true
-1   VM Periodic Task Thread        -               -1         -         0.03      0.001      0:0.092   false      true
49   arthas-NettyHttpTelnetBootstra system          5          RUNNABLE  0.02      0.001      0:0.156   false      true
16   Catalina-utility-1             main            1          TIMED_WAI 0.0       0.000      0:0.029   false      false
-1   G1 Young RemSet Sampling       -               -1         -         0.0       0.000      0:0.019   false      true
17   Catalina-utility-2             main            1          WAITING   0.0       0.000      0:0.025   false      false
34   http-nio-8080-ClientPoller     main            5          RUNNABLE  0.0       0.000      0:0.016   false      true
23   http-nio-8080-BlockPoller      main            5          RUNNABLE  0.0       0.000      0:0.011   false      true
-1   VM Thread                      -               -1         -         0.0       0.000      0:0.032   false      true
-1   Service Thread                 -               -1         -         0.0       0.000      0:0.006   false      true
-1   GC Thread#5                    -               -1         -         0.0       0.000      0:0.043   false      true
Memory                     used     total    max      usage    GC
heap                       36M      70M      4096M    0.90%    gc.g1_young_generation.count   12
g1_eden_space              6M       18M      -1       33.33%                                  86
g1_old_gen                 30M      50M      4096M    0.74%    gc.g1_old_generation.count     0
g1_survivor_space          491K     2048K    -1       24.01%   gc.g1_old_generation.time(ms)  0
nonheap                    66M      69M      -1       96.56%
codeheap_'non-nmethods'    1M       2M       5M       22.39%
metaspace                  46M      47M      -1       98.01%
Runtime
os.name                                                        Mac OS X
os.version                                                     10.15.4
java.version                                                   15
java.home                                                      /Library/Java/JavaVirtualMachines/jdk-15.jdk/Contents/Home
systemload.average                                             10.68
processors                                                     8
uptime                                                         272s 
```

当运行在 Ali-tomcat 时，会显示当前 tomcat 的实时信息，如 HTTP 请求的 qps, rt, 错误数, 线程池信息等等。

### 数据说明

- `ID`: Java 级别的线程 ID，注意这个 ID 不能跟 jstack 中的 nativeID 一一对应。
- `NAME`: 线程名
- `GROUP`: 线程组名
- `PRIORITY`: 线程优先级，1~10 之间的数字，越大表示优先级越高
- `STATE`: 线程的状态
- `CPU%`: 线程的 cpu 使用率。比如采样间隔 1000ms，某个线程的增量 cpu 时间为 100ms，则 cpu 使用率=100/1000=10%
- `DELTA_TIME`: 上次采样之后线程运行增量 CPU 时间，数据格式为`秒`
- `TIME`: 线程运行总 CPU 时间，数据格式为`分:秒`
- `INTERRUPTED`: 线程当前的中断位状态
- `DAEMON`: 是否是 daemon 线程

### JVM 内部线程

Java 8 之后支持获取 JVM 内部线程 CPU 时间，这些线程只有名称和 CPU 时间，没有 ID 及状态等信息（显示 ID 为 -1）。 通过内部线程可以观测到 JVM 活动，如 GC、JIT 编译等占用 CPU 情况，方便了解 JVM 整体运行状况。

- 当 JVM 堆 (heap)/元数据 (metaspace) 空间不足或 OOM 时，可以看到 GC 线程的 CPU 占用率明显高于其他的线程。
- 当执行`trace/watch/tt/redefine` 等命令后，可以看到 JIT 线程活动变得更频繁。因为 JVM 热更新 class 字节码时清除了此 class 相关的 JIT 编译结果，需要重新编译。

JVM 内部线程包括下面几种：

- JIT 编译线程：如 `C1 CompilerThread0` , `C2 CompilerThread0`
- GC 线程：如`GC Thread0` , `G1 Young RemSet Sampling`
- 其它内部线程：如`VM Periodic Task Thread` , `VM Thread` , `Service Thread`

### 参数说明

| 参数名称 | 参数说明                                 |
| -------: | :--------------------------------------- |
|     [i:] | 刷新实时数据的时间间隔 (ms)，默认 5000ms |
|     [n:] | 刷新实时数据的次数                       |

#### 使用参考:

每隔5000毫秒进行刷新一次，总共刷新3次

```bash
dashboard -i 5000 -n 3
```

### 截图展示

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/arthas/dashboard.png)

------

## help

{% note info no-icon %}

提示

[help 指令]的等同于[指令 -help]，都是查看具体指令的使用说明。

{% endnote %}

### 参数说明

| 参数名称 | 参数说明                                   |
| -------: | :----------------------------------------- |
| 不接参数 | 查询当前 arthas 版本支持的指令以及指令描述 |
|  [name:] | 查询具体指令的使用说明                     |

### 使用参考

```text
$ help
 NAME         DESCRIPTION
 help         Display Arthas Help
 auth         Authenticates the current session
 keymap       Display all the available keymap for the specified connection.
 sc           Search all the classes loaded by JVM
 sm           Search the method of classes loaded by JVM
 classloader  Show classloader info
 jad          Decompile class
 getstatic    Show the static field of a class
 monitor      Monitor method execution statistics, e.g. total/success/failure count, average rt, fail rate, etc.
 stack        Display the stack trace for the specified class and method
 thread       Display thread info, thread stack
 trace        Trace the execution time of specified method invocation.
 watch        Display the input/output parameter, return object, and thrown exception of specified method invocation
 tt           Time Tunnel
 jvm          Display the target JVM information
 perfcounter  Display the perf counter information.
 ognl         Execute ognl expression.
 mc           Memory compiler, compiles java files into bytecode and class files in memory.
 redefine     Redefine classes. @see Instrumentation#redefineClasses(ClassDefinition...)
 retransform  Retransform classes. @see Instrumentation#retransformClasses(Class...)
 dashboard    Overview of target jvm's thread, memory, gc, vm, tomcat info.
 dump         Dump class byte array from JVM
 heapdump     Heap dump
 options      View and change various Arthas options
 cls          Clear the screen
 reset        Reset all the enhanced classes
 version      Display Arthas version
 session      Display current session information
 sysprop      Display, and change the system properties.
 sysenv       Display the system env.
 vmoption     Display, and update the vm diagnostic options.
 logger       Print logger info, and update the logger level
 history      Display command history
 cat          Concatenate and print files
 base64       Encode and decode using Base64 representation
 echo         write arguments to the standard output
 pwd          Return working directory name
 mbean        Display the mbean information
 grep         grep command for pipes.
 tee          tee command for pipes.
 profiler     Async Profiler. https://github.com/jvm-profiling-tools/async-profiler
 stop         Stop/Shutdown Arthas server and exit the console.
```



```text
 $ help dashboard
  USAGE:
   dashboard [-h] [-i <value>] [-n <value>]

 SUMMARY:
   Overview of target jvm's thread, memory, gc, vm, tomcat info.

 EXAMPLES:
   dashboard
   dashboard -n 10
   dashboard -i 2000

 WIKI:
   https://arthas.aliyun.com/doc/dashboard

 OPTIONS:
 -h, --help                              this help
 -i, --interval <value>                  The interval (in ms) between two executions, default is 5000 ms.
 -n, --number-of-execution <value>       The number of times this command will be executed.
```

## Thread

`thread 1` 命令会打印线程 ID 1 的栈。

Arthas 支持管道，可以用 `thread 1 | grep 'main('` 查找到`main class` 。

可以看到`main class` 是`demo.MathGame` ：

```shell
$ thread 1 | grep 'main('
    at demo.MathGame.main(MathGame.java:17)
```

{% note info no-icon %}

提示

查看当前线程信息，查看线程的堆栈。

{% endnote %}

### 参数说明

|      参数名称 | 参数说明                                                |
| ------------: | :------------------------------------------------------ |
|          *id* | 线程 id                                                 |
|          [n:] | 指定最忙的前 N 个线程并打印堆栈                         |
|           [b] | 找出当前阻塞其他线程的线程                              |
| [i `<value>`] | 指定 cpu 使用率统计的采样间隔，单位为毫秒，默认值为 200 |
|       [--all] | 显示所有匹配的线程                                      |

### 使用参考

#### 支持一键展示当前最忙的前 N 个线程并打印堆栈：

```sh
"Reference Handler" Id=2 cpuUsage=0.0% deltaTime=0ms time=0ms WAITING on java.lang.ref.Reference$Lock@2718d45c
    at java.lang.Object.wait(Native Method)
    -  waiting on java.lang.ref.Reference$Lock@2718d45c
    at java.lang.Object.wait(Unknown Source)
    at java.lang.ref.Reference.tryHandlePending(Unknown Source)
    at java.lang.ref.Reference$ReferenceHandler.run(Unknown Source)


"Finalizer" Id=3 cpuUsage=0.0% deltaTime=0ms time=0ms WAITING on java.lang.ref.ReferenceQueue$Lock@6ce75d0d
    at java.lang.Object.wait(Native Method)
    -  waiting on java.lang.ref.ReferenceQueue$Lock@6ce75d0d
    at java.lang.ref.ReferenceQueue.remove(Unknown Source)
    at java.lang.ref.ReferenceQueue.remove(Unknown Source)
    at java.lang.ref.Finalizer$FinalizerThread.run(Unknown Source)


"Signal Dispatcher" Id=4 cpuUsage=0.0% deltaTime=0ms time=0ms RUNNABLE
```

- 没有线程 ID，包含`[Internal]`表示为 JVM 内部线程，参考dashboard命令的介绍。
- `cpuUsage`为采样间隔时间内线程的 CPU 使用率，与dashboard命令的数据一致。
- `deltaTime`为采样间隔时间内线程的增量 CPU 时间，小于 1ms 时被取整显示为 0ms。
- `time` 线程运行总 CPU 时间。

注意：线程栈为第二采样结束时获取，不能表明采样间隔时间内该线程都是在处理相同的任务。建议间隔时间不要太长，可能间隔时间越大越不准确。 可以根据具体情况尝试指定不同的间隔时间，观察输出结果。

#### 当没有参数时，显示第一页线程的信息

默认按照 CPU 增量时间降序排列，只显示第一页数据。

#### thread --all, 显示所有匹配的线程

显示所有匹配线程信息，有时需要获取全部 JVM 的线程数据进行分析。

#### thread id, 显示指定线程的运行堆栈

```sh
$ thread 1
"main" Id=1 TIMED_WAITING
    at java.lang.Thread.sleep(Native Method)
    at java.lang.Thread.sleep(Unknown Source)
    at java.util.concurrent.TimeUnit.sleep(Unknown Source)
    at demo.MathGame.main(MathGame.java:17)
```

#### thread -b, 找出当前阻塞其他线程的线程

有时候我们发现应用卡住了， 通常是由于某个线程拿住了某个锁， 并且其他线程都在等待这把锁造成的。 为了排查这类问题， arthas 提供了`thread -b`， 一键找出那个罪魁祸首。

```bash
$ thread -b
"http-bio-8080-exec-4" Id=27 TIMED_WAITING
    at java.lang.Thread.sleep(Native Method)
    at test.arthas.TestThreadBlocking.doGet(TestThreadBlocking.java:22)
    -  locked java.lang.Object@725be470 <---- but blocks 4 other threads!
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:624)
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:731)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:303)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:208)
    at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:241)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:208)
    at test.filter.TestDurexFilter.doFilter(TestDurexFilter.java:46)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:241)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:208)
    at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:220)
    at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:122)
    at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:505)
    at com.taobao.tomcat.valves.ContextLoadFilterValve$FilterChainAdapter.doFilter(ContextLoadFilterValve.java:191)
    at com.taobao.eagleeye.EagleEyeFilter.doFilter(EagleEyeFilter.java:81)
    at com.taobao.tomcat.valves.ContextLoadFilterValve.invoke(ContextLoadFilterValve.java:150)
    at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:170)
    at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:103)
    at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:116)
    at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:429)
    at org.apache.coyote.http11.AbstractHttp11Processor.process(AbstractHttp11Processor.java:1085)
    at org.apache.coyote.AbstractProtocol$AbstractConnectionHandler.process(AbstractProtocol.java:625)
    at org.apache.tomcat.util.net.JIoEndpoint$SocketProcessor.run(JIoEndpoint.java:318)
    -  locked org.apache.tomcat.util.net.SocketWrapper@7127ee12
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    at java.lang.Thread.run(Thread.java:745)

    Number of locked synchronizers = 1
    - java.util.concurrent.ThreadPoolExecutor$Worker@31a6493e
```

{% note warn no-icon %}

注意

注意， 目前只支持找出 synchronized 关键字阻塞住的线程， 如果是`java.util.concurrent.Lock`， 目前还不支持。

{% endnote %}

#### thread -i, 指定采样时间间隔

- `thread -i 1000` : 统计最近 1000ms 内的线程 CPU 时间。
- `thread -n 3 -i 1000` : 列出 1000ms 内最忙的 3 个线程栈

#### thread --state ，查看指定状态的线程

### thread --state ，查看指定状态的线程

```bash
[arthas@28114]$ thread --state WAITING
Threads Total: 16, NEW: 0, RUNNABLE: 9, BLOCKED: 0, WAITING: 3, TIMED_WAITING: 4, TERMINATED: 0
ID   NAME                           GROUP           PRIORITY   STATE     %CPU      DELTA_TIME TIME      INTERRUPTE DAEMON
3    Finalizer                      system          8          WAITING   0.0       0.000      0:0.000   false      true
20   arthas-UserStat                system          9          WAITING   0.0       0.000      0:0.001   false      true
14   arthas-timer                   system          9          WAITING   0.0       0.000      0:0.000   false      true
```

## Sc

可以通过 `sc` 命令 来查找 JVM 里已加载的类：

“Search-Class” 的简写，这个命令能搜索出所有已经加载到 JVM 中的 Class 信息，这个命令支持的参数有 `[d]`、`[E]`、`[f]` 和 `[x:]`。

### 参数说明

|              参数名称 | 参数说明                                                     |
| --------------------: | :----------------------------------------------------------- |
|       *class-pattern* | 类名表达式匹配                                               |
|      *method-pattern* | 方法名表达式匹配                                             |
|                 `[d]` | 输出当前类的详细信息，包括这个类所加载的原始文件来源、类的声明、加载的 ClassLoader 等详细信息。 如果一个类被多个 ClassLoader 所加载，则会出现多次 |
|                 `[E]` | 开启正则表达式匹配，默认为通配符匹配                         |
|                 `[f]` | 输出当前类的成员变量信息（需要配合参数-d 一起使用）          |
|                `[x:]` | 指定输出静态变量时属性的遍历深度，默认为 0，即直接使用 `toString` 输出 |
|                `[c:]` | 指定 class 的 ClassLoader 的 hashcode                        |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name                   |
|                `[n:]` | 具有详细信息的匹配类的最大数量（默认为 100）                 |
|          `[cs <arg>]` | 指定 class 的 ClassLoader#toString() 返回值。长格式`[classLoaderStr <arg>]` |

{% note primary no-icon %}

**提示**

class-pattern 支持全限定名，如 com.taobao.test.AAA，也支持 com/taobao/test/AAA 这样的格式，这样，我们从异常堆栈里面把类名拷贝过来的时候，不需要在手动把/替换为.啦。
{% endnote %}

{% note primary no-icon %}

**提示**

sc 默认开启了子类匹配功能，也就是说所有当前类的子类也会被搜索出来，想要精确的匹配，请打开options disable-sub-class true开关

{% endnote %}

### 使用参考

- 模糊搜索

  ```bash
  $ sc demo.*
  demo.MathGame
  Affect(row-cnt:1) cost in 55 ms.
  ```

- 打印类的详细信息

  ```bash
  $ sc -d demo.MathGame
  class-info        demo.MathGame
  code-source       /private/tmp/math-game.jar
  name              demo.MathGame
  isInterface       false
  isAnnotation      false
  isEnum            false
  isAnonymousClass  false
  isArray           false
  isLocalClass      false
  isMemberClass     false
  isPrimitive       false
  isSynthetic       false
  simple-name       MathGame
  modifier          public
  annotation
  interfaces
  super-class       +-java.lang.Object
  class-loader      +-sun.misc.Launcher$AppClassLoader@3d4eac69
                      +-sun.misc.Launcher$ExtClassLoader@66350f69
  classLoaderHash   3d4eac69
  
  Affect(row-cnt:1) cost in 875 ms.
  ```

- 打印出类的 Field 信息

  ```bash
  $ sc -d -f demo.MathGame
  class-info        demo.MathGame
  code-source       /private/tmp/math-game.jar
  name              demo.MathGame
  isInterface       false
  isAnnotation      false
  isEnum            false
  isAnonymousClass  false
  isArray           false
  isLocalClass      false
  isMemberClass     false
  isPrimitive       false
  isSynthetic       false
  simple-name       MathGame
  modifier          public
  annotation
  interfaces
  super-class       +-java.lang.Object
  class-loader      +-sun.misc.Launcher$AppClassLoader@3d4eac69
                      +-sun.misc.Launcher$ExtClassLoader@66350f69
  classLoaderHash   3d4eac69
  fields            modifierprivate,static
                    type    java.util.Random
                    name    random
                    value   java.util.Random@522b4
                            08a
  
                    modifierprivate
                    type    int
                    name    illegalArgumentCount
  
  
  Affect(row-cnt:1) cost in 19 ms.
  ```

- 通过 ClassLoader#toString 查找类（前提：有一个 toString()返回值是`apo`的类加载器，加载的类中包含`demo.MathGame`, `demo.MyBar`,` demo.MyFoo`3 个类）

  ```bash
  $ sc -cs apo *demo*
  demo.MathGame
  demo.MyBar
  demo.MyFoo
  Affect(row-cnt:3) cost in 56 ms.
  ```

## Jad

可以通过 `jad` 命令来反编译代码：

{% note info no-icon %}

**提示**

反编译指定已加载类的源码

{% endnote %}

`jad` 命令将 JVM 中实际运行的 class 的 byte code 反编译成 java 代码，便于你理解业务逻辑；如需批量下载指定包的目录的 class 字节码可以参考 [dump](https://arthas.aliyun.com/doc/dump.html)。

- 在 Arthas Console 上，反编译出来的源码是带语法高亮的，阅读更方便
- 当然，反编译出来的 java 代码可能会存在语法错误，但不影响你进行阅读理解

### 参数说明

|              参数名称 | 参数说明                                   |
| --------------------: | :----------------------------------------- |
|       *class-pattern* | 类名表达式匹配                             |
|                `[c:]` | 类所属 ClassLoader 的 hashcode             |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name |
|                   [E] | 开启正则表达式匹配，默认为通配符匹配       |

### 使用参考

#### 反编译`java.lang.String`

```java
$ jad java.lang.String

ClassLoader:

Location:


        /*
         * Decompiled with CFR.
         */
        package java.lang;

        import java.io.ObjectStreamField;
        import java.io.Serializable;
...
        public final class String
        implements Serializable,
        Comparable<String>,
        CharSequence {
            private final char[] value;
            private int hash;
            private static final long serialVersionUID = -6849794470754667710L;
            private static final ObjectStreamField[] serialPersistentFields = new ObjectStreamField[0];
            public static final Comparator<String> CASE_INSENSITIVE_ORDER = new CaseInsensitiveComparator();
...
            public String(byte[] byArray, int n, int n2, Charset charset) {
/*460*/         if (charset == null) {
                    throw new NullPointerException("charset");
                }
/*462*/         String.checkBounds(byArray, n, n2);
/*463*/         this.value = StringCoding.decode(charset, byArray, n, n2);
            }
...

```

#### 反编译时只显示源代码

默认情况下，反编译结果里会带有`ClassLoader`信息，通过`--source-only`选项，可以只打印源代码。方便和[mc](https://arthas.aliyun.com/doc/mc.html)/[retransform](https://arthas.aliyun.com/doc/retransform.html)命令结合使用。

```java
$ jad --source-only demo.MathGame
/*
 * Decompiled with CFR 0_132.
 */
package demo;

import java.io.PrintStream;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Random;
import java.util.concurrent.TimeUnit;

public class MathGame {
    private static Random random = new Random();
    public int illegalArgumentCount = 0;
...

```

#### 反编译指定的函数

```java
$ jad demo.MathGame main

ClassLoader:
+-sun.misc.Launcher$AppClassLoader@232204a1
  +-sun.misc.Launcher$ExtClassLoader@7f31245a

Location:
/private/tmp/math-game.jar

       public static void main(String[] args) throws InterruptedException {
           MathGame game = new MathGame();
           while (true) {
/*16*/         game.run();
/*17*/         TimeUnit.SECONDS.sleep(1L);
           }
       }

```

#### 反编译时不显示行号

`--lineNumber` 参数默认值为 true，显示指定为 false 则不打印行号。

```java
$ jad demo.MathGame main --lineNumber false

ClassLoader:
+-sun.misc.Launcher$AppClassLoader@232204a1
  +-sun.misc.Launcher$ExtClassLoader@7f31245a

Location:
/private/tmp/math-game.jar

public static void main(String[] args) throws InterruptedException {
    MathGame game = new MathGame();
    while (true) {
        game.run();
        TimeUnit.SECONDS.sleep(1L);
    }
}

```

#### 反编译时指定 ClassLoader

{% note info no-icon %}

**提示**

当有多个 `ClassLoader` 都加载了这个类时，`jad` 命令会输出对应 `ClassLoader` 实例的 `hashcode`，然后你只需要重新执行 `jad` 命令，并使用参数 `-c <hashcode>` 就可以反编译指定 ClassLoader 加载的那个类了；

{% endnote %}

```java
$ jad org.apache.log4j.Logger

Found more than one class for: org.apache.log4j.Logger, Please use jad -c hashcode org.apache.log4j.Logger
HASHCODE  CLASSLOADER
69dcaba4  +-monitor's ModuleClassLoader
6e51ad67  +-java.net.URLClassLoader@6e51ad67
            +-sun.misc.Launcher$AppClassLoader@6951a712
            +-sun.misc.Launcher$ExtClassLoader@6fafc4c2
2bdd9114  +-pandora-qos-service's ModuleClassLoader
4c0df5f8  +-pandora-framework's ModuleClassLoader

Affect(row-cnt:0) cost in 38 ms.
$ jad org.apache.log4j.Logger -c 69dcaba4

ClassLoader:
+-monitor's ModuleClassLoader

Location:
/Users/admin/app/log4j-1.2.14.jar

package org.apache.log4j;

import org.apache.log4j.spi.*;

public class Logger extends Category
{
    private static final String FQCN;

    protected Logger(String name)
    {
        super(name);
    }

...

Affect(row-cnt:1) cost in 190 ms.
```

对于只有唯一实例的 ClassLoader 还可以通过`--classLoaderClass`指定 class name，使用起来更加方便：

`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>`是动态变化的。

--------

## Watch

通过 `watch` 命令可以查看函数的参数/返回值/异常信息。

{% note info no-icon %}

提示

函数执行数据观测

{% endnote %}

让你能方便的观察到指定函数的调用情况。能观察到的范围为：`返回值`、`抛出异常`、`入参`，通过编写 OGNL 表达式进行对应变量的查看。

### 参数说明

watch 的参数比较多，主要是因为它能在 4 个不同的场景观察对象

|            参数名称 | 参数说明                                                     |
| ------------------: | :----------------------------------------------------------- |
|     *class-pattern* | 类名表达式匹配                                               |
|    *method-pattern* | 函数名表达式匹配                                             |
|           *express* | 观察表达式，默认值：`{params, target, returnObj}`            |
| *condition-express* | 条件表达式                                                   |
|                 [b] | 在**函数调用之前**观察                                       |
|                 [e] | 在**函数异常之后**观察                                       |
|                 [s] | 在**函数返回之后**观察                                       |
|                 [f] | 在**函数结束之后**(正常返回和异常返回)观察                   |
|                 [E] | 开启正则表达式匹配，默认为通配符匹配                         |
|                [x:] | 指定输出结果的属性遍历深度，默认为 1，最大值是 4             |
|         `[m <arg>]` | 指定 Class 最大匹配数量，默认值为 50。长格式为`[maxMatch <arg>]`。 |

这里重点要说明的是观察表达式，观察表达式的构成主要由 ognl 表达式组成，所以你可以这样写`"{params,returnObj}"`，只要是一个合法的 ognl 表达式，都能被正常支持。

观察的维度也比较多，主要体现在参数 `advice` 的数据结构上。`Advice` 参数最主要是封装了通知节点的所有信息。

- 特殊用法请参考：[https://github.com/alibaba/arthas/issues/71在新窗口打开](https://github.com/alibaba/arthas/issues/71)
- OGNL 表达式官网：[https://commons.apache.org/proper/commons-ognl/language-guide.html在新窗口打开](https://commons.apache.org/proper/commons-ognl/language-guide.html)

**特别说明**：

- watch 命令定义了 4 个观察事件点，即 `-b` 函数调用前，`-e` 函数异常后，`-s` 函数返回后，`-f` 函数结束后
- 4 个观察事件点 `-b`、`-e`、`-s` 默认关闭，`-f` 默认打开，当指定观察点被打开后，在相应事件点会对观察表达式进行求值并输出
- 这里要注意`函数入参`和`函数出参`的区别，有可能在中间被修改导致前后不一致，除了 `-b` 事件点 `params` 代表函数入参外，其余事件都代表函数出参
- 当使用 `-b` 时，由于观察事件点是在函数调用前，此时返回值或异常均不存在
- 在 watch 命令的结果里，会打印出`location`信息。`location`有三种可能值：`AtEnter`，`AtExit`，`AtExceptionExit`。对应函数入口，函数正常 return，函数抛出异常。

### 使用参考

#### 观察函数调用返回时的参数、this 对象和返回值

{% note info no-icon %}

提示

观察表达式，默认值是 {params, target, returnObj}

{% endnote %}

```sh
$ watch demo.MathGame primeFactors -x 2
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 32 ms, listenerId: 5
method=demo.MathGame.primeFactors location=AtExceptionExit
ts=2021-08-31 15:22:57; [cost=0.220625ms] result=@ArrayList[
    @Object[][
        @Integer[-179173],
    ],
    @MathGame[
        random=@Random[java.util.Random@31cefde0],
        illegalArgumentCount=@Integer[44],
    ],
    null,
]
method=demo.MathGame.primeFactors location=AtExit
ts=2021-08-31 15:22:58; [cost=1.020982ms] result=@ArrayList[
    @Object[][
        @Integer[1],
    ],
    @MathGame[
        random=@Random[java.util.Random@31cefde0],
        illegalArgumentCount=@Integer[44],
    ],
    @ArrayList[
        @Integer[2],
        @Integer[2],
        @Integer[26947],
    ],
]
```

- 上面的结果里，说明函数被执行了两次，第一次结果是`location=AtExceptionExit`，说明函数抛出异常了，因此`returnObj`是 null
- 在第二次结果里是`location=AtExit`，说明函数正常返回，因此可以看到`returnObj`结果是一个 ArrayList

#### 指定 Class最大匹配数量

```sh
$ watch demo.MathGame primeFactors -m 1
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 302 ms, listenerId: 3
method=demo.MathGame.primeFactors location=AtExceptionExit
ts=2022-12-25 19:58:41; [cost=0.222419ms] result=@ArrayList[
    @Object[][isEmpty=false;size=1],
    @MathGame[demo.MathGame@3bf400],
    null,
]
method=demo.MathGame.primeFactors location=AtExceptionExit
ts=2022-12-25 19:58:51; [cost=0.046928ms] result=@ArrayList[
    @Object[][isEmpty=false;size=1],
    @MathGame[demo.MathGame@3bf400],
    null,
]
```

#### 观察函数调用入口的参数和返回值

```sh
$ watch demo.MathGame primeFactors "{params,returnObj}" -x 2 -b
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 50 ms.
ts=2018-12-03 19:23:23; [cost=0.0353ms] result=@ArrayList[
    @Object[][
        @Integer[-1077465243],
    ],
    null,
]
```

- 对比前一个例子，返回值为空（事件点为函数执行前，因此获取不到返回值）

## Vmtool

通过 `vmtool` 命令，可以搜索内存对象。

### 获取 class 实例

```sh
$ vmtool --action getInstances --className java.lang.String --limit 10
@String[][
    @String[com/taobao/arthas/core/shell/session/Session],
    @String[com.taobao.arthas.core.shell.session.Session],
    @String[com/taobao/arthas/core/shell/session/Session],
    @String[com/taobao/arthas/core/shell/session/Session],
    @String[com/taobao/arthas/core/shell/session/Session.class],
    @String[com/taobao/arthas/core/shell/session/Session.class],
    @String[com/taobao/arthas/core/shell/session/Session.class],
    @String[com/],
    @String[java/util/concurrent/ConcurrentHashMap$ValueIterator],
    @String[java/util/concurrent/locks/LockSupport],
]
```

{% note info no-icon %}

提示

通过 --limit 参数，可以限制返回值数量，避免获取超大数据时对 JVM 造成压力。默认值是 10。

{% endnote %}

#### 调用实例方法

```sh
[arthas@13416]$ vmtool --action  getInstances  --className demo.MathGame --express 'instances[0].primeFactors(3)' -x 3
@ArrayList[
    @Integer[3],
]
```

## 退出 Arthas

用 `exit` 或者 `quit` 命令可以退出 Arthas。

退出 Arthas 之后，还可以再次用 `java -jar arthas-boot.jar` 来连接。

## 彻底退出 Arthas

`exit/quit` 命令只是退出当前 session，arthas server 还在目标进程中运行。

想完全退出 Arthas，可以执行 `stop` 命令。

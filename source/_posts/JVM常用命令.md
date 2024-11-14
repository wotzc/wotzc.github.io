---
title: Java 监控和诊断工具：jps、jstat、jcmd、jmap和jstack
date: 2024-11-12 10:59:28
tags:
- JVM
categories: JVM
---

**Java** 应用程序通常在复杂且动态的环境中运行，因此监控其性能并有效诊断问题至关重要。幸运的是，**Java** 开发工具包 (**JDK**) 附带了一套强大的工具来实现此目的。在本指南中，我们将探索四个基本工具：**jps**、**jstat**、**jcmd**、**jmap**和**jstack**。我们将讨论它们的功能、示例用法以及如何有效地解释它们的输出。

# jps

`jps(Java Process Status)` 工具列出本地计算机上的 **Java** 虚拟机 (**JVM**) 进程。它提供进程 ID (**PID**) 和正在执行的主类或 **JAR** 文件等信息。

Usage:

```bash
jps [options]
```

Sample Usage:

```bash
$ jps -l
12345 com.example.MainApp
```

理解输出：

- 第一列表示 **PID**
- 第二列显示主类的完全限定类名或 **JAR** 文件名

# jstat

`jstat (JVM Statistics Monitoring Tool)` 是一个命令行工具，提供有关 **JVM** 内部统计信息，例如垃圾收集、类加载、编译器活动等。

Usage:

```bash
jstat [options] <vmid> [<interval> [<count>]]
```

Sample Usage:

```bash
$ jstat -gcutil 12345 1000 10
```

`-gcutil` 提供垃圾收集统计数据，每秒统计1次，共统计两次

```bash
(base) mac7@TZC-Mac ~ % jstat -options
-class
-compiler
-gc
-gccapacity
-gccause
-gcmetacapacity
-gcnew
-gcnewcapacity
-gcold
-gcoldcapacity
-gcutil
-printcompilation
```

`option` 选项详解

- `-class`  用于查看类加载情况的统计 
- `-compiler` 用于查看**HotSpot**中即时编译器编译情况的统计 
- `-gc` 用于查看**JVM**中堆的垃圾收集情况的统计 
- `-gccapacity` 用于查看新生代、老生代及持久代的存储容量情况 
- `-gccause` 显示[垃圾回收]的相关信息（同`-gcutil`）,同时显示最后一次仅当前正在发生的垃圾收集的原因 
- `-gcmetacapacity` 用于查看新生代垃圾收集的情况 
- `-gcnew` 用于查看新生代存储容量的情况 
- `-gcnewcapacity` 用于查看老生代及持久代垃圾收集的情况 
- `-gcold`  用于查看老生代及持久代垃圾收集的情况 
- `-gcoldcapacity`  用于查看老生代的容量 
- `-gcutil` 显示垃圾收集信息 
- `-printcompilation` 输出**JIT**编译的方法信息

`interval` 参数详解

- 指定输出统计数据的周期，单位毫秒

`count` 参数详解

- 一共输出多少次数据

Usage:

`-class`命令，每秒统一次**classloader**信息，一共输出 2 次

```bash
[root@localhost ~]$ jstat -class 21074 1000 2
Loaded  Bytes  Unloaded  Bytes     Time   
  3198  6281.9        0     0.0       0.99
  3198  6281.9        0     0.0       0.99
```

装载了3198个类，大小6281.9个字节，卸载了0个类，大小0个字节，装载和卸载耗费时间总时间0.99秒

`-compiler`命令，显示**JVM**实时编译的数量

```bash
[root@localhost ~]$ jstat -compiler 21074
Compiled Failed Invalid   Time   FailedType FailedMethod
    3920      2       0    10.55          1 com/mysql/jdbc/AbandonedConnectionCleanupThread run
```

编译任务执行了3920个，失败了2个，失效了0个，编译耗费10.55秒，最后一个编译失败任务的类型为1 ，最后一个编译失败任务所在的方法

`-gc`命令，显示**GC**堆相关信息

```bash
[root@localhost ~]$ jstat -gc 21074
 S0C    	S1C    	S0U    S1U   EC        EU       OC         OU       MC     	MU    	CCSC   CCSU   	YGC   YGCT    FGC    FGCT     GCT   
12800.0 	12288.0 288.0  0.0   3120640.0 360814.7 1048576.0  17050.0  20608.0 20235.5 2432.0 2280.5 	26    0.129   0      0.000    0.129
```

字段解释：

- S0C 年轻代中第一个S区容量（千字节）

- S1C 年轻代中第二个S区容量（千字节）
- S0U 年轻代中第一个S区已使用的空间（千字节）
- S1U 年轻代中第二个S区已使用的空间（千字节）
- EC 年轻代中E区容量（千字节）
- EU 年轻代中E区使用空间（千字节）
- OC old代的容量（千字节）
- OU old代已使用空间（千字节）
- MC metaspace元空间容量（千字节）
- MU metaspace元空间已使用容量（千字节）
- CCSC：当前压缩类空间的容量 (千字节)
- CCSU：当前压缩类空间目前已使用空间 (千字节)
- YGC 应用程序启动到采样时年轻代中GC次数
- YGCT 应用程序启动到采样时年轻代中GC所用时间
- FGC 应用程序启动到采样老年代GC次数（秒）
- FGCT 应用程序启动到采样老年代GC所用时间（秒）
- GCT 应用程序从启动到采样GC所用总时间（秒）

`-gcmetacapacity`命令，查看`metaspace`中对象的信息

```bash
[root@localhost ~]$ jstat -gcmetacapacity 21074
   MCMN       MCMX        MC       CCSMN      CCSMX       CCSC     YGC   FGC    FGCT     GCT   
     0.0  1067008.0    20608.0        0.0  1048576.0     2432.0    31     0    0.000    0.134
```

字段解释：

- MCMN 最小元数据容量
- MCMX 最大元数据容量
- MC 当前元数据空间大小
- CCSMN 最小压缩类空间大小
- CCSMX 最大压缩类空间大小
- CCSC 当前压缩类空间大小
- YGC 从应用程序启动到采样YGC次数
- FGC 从应用程序启动到采样FULL GC次数
- FGCT 从应用程序到采样FULL GC所用时间
- GCT 从应用程序到采样GC总时间

`-gcnew`命令，年轻代对象信息

```bash
[root@localhost ~]$ jstat -gcnew 21074
 S0C    S1C    S0U   S1U   TT MTT	DSS      EC       EU     	YGC     YGCT  
8704.0 9728.0  0.0  288.0  1  15	8704.0 3125760.0 3067072.3  31    0.134
```

字段解释：

- S0C 年轻代中第一个S区容量

- S1C 年轻代中第二个S区容量
- S0U 年轻代中第一个S区已使用容量
- S1U 年轻代中第二个S区已使用容量
- TT 持有次数限制
- MTT 最大持有次数限制
- DSS 期望的S区大小
- EC 年轻代中E区大小
- EU 年轻代中E区已使用大小
- YGC 从应用程序启动到采样YGC次数
- YGCT 从应用程序启动到采样YGC所用时间

`-gcnewcapacity`命令，年轻代对象信息以及占用量

```bash
[root@localhost ~]$ jstat -gcnewcapacity 21074
  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
 3145728.0  3145728.0  3145728.0 1048576.0   8704.0 1048576.0   8192.0  3144704.0  3128832.0    32     0
```

字段解释：

- NGCMN 年轻代初始化大小

- NGCMX 年轻代最大容量
- NGC 年轻代中当前容量
- S0CMX S0最大的容量
- S0C 当前S0大小
- S1CM S1最大容量
- S1C 当前S1大小
- ECMX E区最大容量
- EC E区当前大小
- YGC 从应用程序启动到采样YGC次数
- FGC 从应用程序启动到采样FGC次数

`-gcold`命令，老年代对象信息

```bash
[root@localhost ~]$ jstat -gcold 21074
   MC       MU      CCSC     CCSU       OC          OU       YGC    FGC    FGCT     GCT   
 20608.0  20235.5   2432.0   2280.5   1048576.0     17234.0     32     0    0.000    0.135
```

字段解释：

- MC metaspace 元空间最大容量

- MU metaspace 元空间当前大小
- CCSC 压缩类空间大小
- CCSU 压缩类空间当前使用大小
- OC 老年代容量
- OU 老年代已使用大小
- YGC 从应用程序启动到采样YGC次数
- FGC 从应用程序启动到采样FGC次数
- FGCT 从应用程序启动到采样FGC耗费总时间
- GCT 从应用程序启动到采样GC耗费总时间

`-gcoldcapacity`命令，老年代对象占用情况

```bash
[root@localhost ~]$ jstat -gcoldcapacity 21074
   OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT   
  1048576.0   1048576.0   1048576.0   1048576.0    32     0    0.000    0.135
```

字段解释：

- OGCMN 老年代初始化大小
- OGCMX 老年代最大容量
- OGC 老年代当前使用大小
- OC 老年代容量

`-gcutil`命令，统计**GC**情况

```bash
[root@localhost ~]$ jstat -gcutil 21074
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  2.21   0.00  54.97   1.64  98.19  93.77     32    0.135     0    0.000    0.135
```

字段解释：

- S0 S0区占用比
- S1 S1区占用比
- E 伊甸园区占用比
- O 老年代占用比
- M 元空间占用比
- CCS 压缩类占用比
- YGC YGC发生的次数
- YGCT 所有YGC的总共耗时
- FGC FGC发生的次数
- FGCT 所有FGC的共耗时
- GCT 所有GC的总耗时

`-gccause`命令，显示**GC**导致的原因

```bash
[root@localhost ~]$ jstat -gccause 21074
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT    LGCC                 GCC                 
  2.21   0.00  68.95   1.64  98.19  93.77     32    0.135     0    0.000    0.135 Allocation Failure   No GC
```

大部分字段同`-gcutil`命令

字段解释：

- LGCC 上一次GC原因
- GCC 当前GC原因

`-printcompilation`命令，VM执行的信息

```bash
[root@localhost ~]$ jstat -printcompilation 21074
Compiled  Size  Type Method
    3964    311    1 com/alibaba/druid/sql/visitor/SchemaStatVisitor visit
```

字段解释：

- compiled 编译任务的数目
- size 方法生成的字节码大小
- type 编译类型
- method 类名和方法名

---------

# jcmd

`jcmd` - 向正在运行的 Java 虚拟机 (`JVM`) 发送诊断命令请求

`jcmd` [*pid* | *main-class* ] *command* ... | `PerfCounter.print` | `-f` *filename*

`jcmd` [`-l` ]

`jcmd -h`

查看帮助

```bash
$ jcmd -h
Usage: jcmd <pid | main class> <command ...|PerfCounter.print|-f file>
   or: jcmd -l
   or: jcmd -h

  command must be a valid jcmd command for the selected jvm.
  Use the command "help" to see which commands are available.
  If the pid is 0, commands will be sent to all Java processes.
  The main class argument will be used to match (either partially
  or fully) the class used to start Java.
  If no options are given, lists Java processes (same as -p).

  PerfCounter.print display the counters exposed by this process
  -f  read and execute commands from the file
  -l  list JVM processes on the local machine
  -h  this help
```

查看正在运行的 **Java** 进程**ID**、名称和 **main** 函数参数

```bash
$ jcmd
7200 sun.tools.jcmd.JCmd
10614 com.install4j.runtime.launcher.MacLauncher
88348 org.gradle.launcher.daemon.bootstrap.GradleDaemon 2.14
```

注意，**7200** 进程是`jcmd`本身。执行完`jcmd`后，该进程已经结束了。

查看某个进程支持的命令

在`jcmd` 后加上进程 **ID**，然后加上 **help** 。

```bash
$ jcmd 10614 help
10614:
The following commands are available:
JFR.configure
JFR.stop
JFR.start
JFR.dump
JFR.check
VM.log
VM.native_memory
VM.check_commercial_features
VM.unlock_commercial_features
ManagementAgent.status
ManagementAgent.stop
ManagementAgent.start_local
ManagementAgent.start
Compiler.directives_clear
Compiler.directives_remove
Compiler.directives_add
Compiler.directives_print
VM.print_touched_methods
Compiler.codecache
Compiler.codelist
Compiler.queue
VM.classloader_stats
Thread.print
JVMTI.data_dump
JVMTI.agent_load
VM.stringtable
VM.symboltable
VM.class_hierarchy
GC.class_stats
GC.class_histogram
GC.heap_dump
GC.finalizer_info
GC.heap_info
GC.run_finalization
GC.run
VM.info
VM.uptime
VM.dynlibs
VM.set_flag
VM.flags
VM.system_properties
VM.command_line
VM.version
help

For more information about a specific command use 'help <command>'.
```

`jcmd` 后也可以跟上进程名：

```bash
$ jcmd MacLauncher help
```

输出结果和 `jcmd 10614 help` 相同。

查看某个进程的 JVM 版本

```bash
$ jcmd 10614 VM.version
10614:
Java HotSpot(TM) 64-Bit Server VM version 9.0.1+11
JDK 9.0.1
```

查看 JVM 进程信息

```bash
$ jcmd 10614 VM.info
```

建议进程进行垃圾回收

```bash
$ jcmd 10614 GC.run
```

获取类的统计信息

```bash
$ jcmd 10614 GC.class_histogram | more
```

可以看到类名、对象数量、占用空间等。

获取启动参数

```bash
$ jcmd 10614 VM.flags
```

获取进程到现在运行了多长时间

```bash
$ jcmd 10614 VM.uptime
```

查看线程信息

```bash
$ jcmd 10614 Thread.print
```

获取性能相关数据

```bash
$ jcmd 10614 PerfCounter.print
```

导出堆快照到当前目录

```bash
$ jcmd 10614 GC.heap_dump $PWD/heap.dump
```

堆快照可以使用 [VisualVM](https://visualvm.github.io/) 等工具打开分析。

----

# jmap

`jmap (Memory Map for Java)` 为给定的 Java 进程生成与内存相关的信息，包括堆`dump`和内存使用情况统计信息。

Usage:

```bash
jmap [option] <pid>
```

查看一下`jmap`命令的帮助信息

```bash
[root@localhost ~]$ jmap -help
Usage:
    jmap [option] <pid>
        (to connect to running process)
    jmap [option] <executable <core>
        (to connect to a core file)
    jmap [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    <none>               to print same info as Solaris pmap
    -heap                to print java heap summary
    -histo[:live]        to print histogram of java object heap; if the "live"
                         suboption is specified, only count live objects
    -clstats             to print class loader statistics
    -finalizerinfo       to print information on objects awaiting finalization
    -dump:<dump-options> to dump java heap in hprof binary format
                         dump-options:
                           live         dump only live objects; if not specified,
                                        all objects in the heap are dumped.
                           format=b     binary format
                           file=<file>  dump heap to <file>
                         Example: jmap -dump:live,format=b,file=heap.bin <pid>
    -F                   force. Use with -dump:<dump-options> <pid> or -histo
                         to force a heap dump or histogram when <pid> does not
                         respond. The "live" suboption is not supported
                         in this mode.
    -h | -help           to print this help message
    -J<flag>             to pass <flag> directly to the runtime system
```

参数：

- `option`： 选项参数
- `pid`： 需要打印配置信息的进程**ID**
- `executable`： 产生核心**dump**的Java可执行文件
- `core`： 需要打印配置信息的核心文件
- `server-id` 可选的唯一**id**，如果相同的远程主机上运行了多台调试服务器，用此选项参数标识服务器
- `remote server IP or hostname` 远程调试服务器的IP地址或主机名

这些参数里面一般使用option和pid即可

- `no option`： 查看进程的内存映像信息，类似Solaris pmap命令
- `heap`： 显示Java堆详细信息
- `histo[:live]`： 显示堆中对象的统计信息
- `clstats`：打印类加载器信息
- `finalizerinfo`： 显示在`F-Queue`队列等待`Finalizer`线程执行`finalizer`方法的对象
- `dump`:：生成堆转储快照
- `F`： 当`-dump`没有响应时，使用`-dump`或者`-histo`参数。在这个模式下，`live`子参数无效
- `help`：打印帮助信息
- `J`：指定传递给运行`jmap`的`JVM`的参数

**示例**

1. `jmap -heap pid`命令，打印堆内存详细信息

打印一个堆的摘要信息，包括使用的**GC**算法、堆配置信息和各内存区域内存使用信息

```bash
[root@localhost ~]$ jmap -heap 21074
Attaching to process ID 21074, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.131-b11

using thread-local object allocation.
Parallel GC with 4 thread(s)	

Heap Configuration:										##堆配置情况，也就是JVM参数配置的结果
   MinHeapFreeRatio         = 0							##最小堆使用比例
   MaxHeapFreeRatio         = 100						##最大堆可用比例
   MaxHeapSize              = 4294967296 (4096.0MB)		##最大堆空间大小
   NewSize                  = 3221225472 (3072.0MB)		##新生代分配大小
   MaxNewSize               = 3221225472 (3072.0MB)		##最大可新生代分配大小
   OldSize                  = 1073741824 (1024.0MB)		##老年代大小
   NewRatio                 = 2							##新生代比例
   SurvivorRatio            = 8							##新生代与suvivor的比例
   MetaspaceSize            = 21807104 (20.796875MB)	##元空间大小
   CompressedClassSpaceSize = 1073741824 (1024.0MB)		##压缩类空间大小
   MaxMetaspaceSize         = 17592186044415 MB			##最大元空间大小
   G1HeapRegionSize         = 0 (0.0MB)					##G1的region大小

Heap Usage:												##堆使用情况
PS Young Generation										##新生代（伊甸Eden区 + 幸存者survior（from + to）区）
Eden Space:												##伊甸区
   capacity = 3206021120 (3057.5MB)						##伊甸区容量
   used     = 1334298032 (1272.4857635498047MB)			##已经使用大小
   free     = 1871723088 (1785.0142364501953MB)			##剩余容量
   41.61850412264283% used								##使用比例
From Space:												##survior1区
   capacity = 7340032 (7.0MB)							##survior1区容量
   used     = 229376 (0.21875MB)						##surviror1区已使用情况
   free     = 7110656 (6.78125MB)						##surviror1区剩余容量
   3.125% used											##survior1区使用比例
To Space:												##survior2 区
   capacity = 6815744 (6.5MB)							##survior2区容量
   used     = 0 (0.0MB)									##survior2区已使用情况
   free     = 6815744 (6.5MB)							##survior2区剩余容量
   0.0% used											##survior2区使用比例
PS Old Generation										##老年代使用情况
   capacity = 1073741824 (1024.0MB)						##老年代容量
   used     = 17754144 (16.931671142578125MB)			##老年代已使用容量
   free     = 1055987680 (1007.0683288574219MB)			##老年代剩余容量
   1.653483510017395% used								##老年代使用比例

7111 interned Strings occupying 559016 bytes.			##系统中使用的字符串总大小

```

2. `jmap -histo:live pid`命令，显示堆中对象的统计信息

其中包括每个Java类、对象数量、内存大小(单位：字节)、完全限定的类名。打印的虚拟机内部的类名称将会带有一个`*`前缀。如果指定了live子选项，则只计算活动的对象。

```bash
[root@localhost ~]$ jmap -histo:live 21074
#编号	#实例个数		#实例总大小 #全限定类名
num     #instances         #bytes  class name
----------------------------------------------
   1:        262144        6291456  org.apache.logging.log4j.core.async.AsyncLoggerConfigDisruptor$Log4jEventWrapper
   2:         16536        2830928  [C
   3:          2622        1197536  [Ljava.lang.Object;
   4:          1547        1122888  [I
   5:           896         675144  [B
   6:         16462         395088  java.lang.String
   7:          3483         393304  java.lang.Class
   8:         20149         322384  java.lang.Integer
   9:          8820         282240  io.mycat.statistic.HeartbeatRecorder$Record
  10:          8954         214896  java.util.concurrent.ConcurrentLinkedQueue$Node
  11:          2007         211264  [Ljava.util.HashMap$Node;
  12:          5444         174208  java.util.HashMap$Node
  13:          4183         133856  java.util.concurrent.ConcurrentHashMap$Node
  14:          1493         119440  io.mycat.route.RouteResultsetNode
  15:          1959          94032  java.util.HashMap
  16:             2          80560  [Ljava.lang.Integer;
  17:            93          67352  [Ljava.util.concurrent.ConcurrentHashMap$Node;
  18:          3713          59408  java.lang.Object
  ......
```

类名解释如下

- `B` `byte`
- `C` `char`
- `D` `double`
- `F` `float`
- `I` `int`
- `J` `long`
- `Z` `boolean`
- [ 数组，如`[I`表示`int[]`
- `[L`+类名 其他对象

3. `jmap -dump:format=b,file=heapdump.hprof pid`命令，`dump`当前内存快照

以`hprof`二进制格式转储`Java`堆到指定`filename`的文件中。`live`子选项是可选的。如果指定了`live`子选项，堆中只有活动的对象会被转储。想要浏览heap dump，你可以使用jhat（Java堆分析工具）或者MAT等工具读取生成的文件。

-----------

# jstack

`jstack`（Java Virtual Machine Stack Trace）是JDK提供的一个可以生成Java虚拟机当前时刻的线程快照信息的命令行工具。线程快照一般被称为**threaddump**或者**javacore**文件，是当前Java虚拟机中每个线程正在执行的Java线程、虚拟机内部线程和可选的本地方法堆栈帧的集合。对于每个方法栈帧，将会显示完整的类名、方法名、字节码索引(**bytecode index**，**BCI**)和行号。生成的线程快照可以用于定位线程出现长时间停顿的原因，比如：线程间死锁、死循环、请求外部资源被长时间挂起等等。

> jstack prints Java stack traces of Java threads **for** a given Java process or core file or a remote debug server. 

- `jstack`命令用于生成虚拟机当前时刻的线程快照。
- 线程快照是当前虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因， 如线程间死锁、死循环、请求外部资源导致的长时间等待等问题。
- 线程出现停顿的时候通过`jstack`来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做什么事情，或者等待什么资源。
- 如果**java**程序崩溃生成**core**文件，`jstack`工具可以用来获得**core**文件的**java stack**和**native stack**的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。
- 另外，`jstack`工具还可以附属到正在运行的**java**程序中，看到当时运行的**java**程序的**java stack**和**native stack**的信息, 如果现在运行的java程序呈现hung的状态，`jstack`是非常有用的。

命令语法：

```bash
jstack [options] pid
```

命令参数说明：

- `option`：`jstack`命令的可选参数。如果没有指定这个参数，`jstack`命令会显示**Java**虚拟机当前时刻的线程快照信息：

```bash
D:\Desktop>jstack 9348
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.281-b09 mixed mode):

"DestroyJavaVM" #13 prio=5 os_prio=0 tid=0x0000018da5881000 nid=0x34c8 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"mythread2" #12 prio=5 os_prio=0 tid=0x0000018dc2547000 nid=0x2580 waiting on condition [0x000000468cdff000]
   java.lang.Thread.State: WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x000000076b8dbf28> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
        at java.util.concurrent.locks.LockSupport.park(Unknown Source)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(Unknown Source)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(Unknown Source)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(Unknown Source)
        at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(Unknown Source)
        at java.util.concurrent.locks.ReentrantLock.lock(Unknown Source)
        at DeathLock.lambda$deathLock$1(DeathLock.java:24)
        at DeathLock$$Lambda$2/1044036744.run(Unknown Source)
        at java.lang.Thread.run(Unknown Source)
```

查看帮助信息

可以通过执行`jstack` 或者 `jstack -h`来查看帮助信息：

```bash
$ jstack
Usage:
    jstack [-l] <pid>
        (to connect to running process)
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process)
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file)
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server)

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -m  to print both java and native frames (mixed mode)
    -l  long listing. Prints additional information about locks
    -h or -help to print this help message
```

**option参数说明如下：**

```bash
-F 当’jstack [-l] pid’没有相应的时候强制打印栈信息
-l 长列表. 打印关于锁的附加信息,例如属于java.util.concurrent的ownable synchronizers列表.
-m 打印java和native c/c++框架的所有栈信息.
-h | -help 打印帮助信息
```

> **pid 需要被打印配置信息的java进程id,可以用jps查询**

**线程状态简介**

**jstack**用于生成线程快照的，我们分析线程的情况，需要复习一下线程状态

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/jvm/threadstatus.jpg" style="zoom:67%;" />

**Java语言定义了6种线程池状态：**

- `New`：创建后尚未启动的线程处于这种状态，不会出现在**Dump**中。
- `RUNNABLE`：包括**Running**和**Ready**。线程开启`start（）`方法，会进入该状态，在虚拟机内执行的。
- `Waiting`：无限的等待另一个线程的特定操作。
- `Timed Waiting`：有时限的等待另一个线程的特定操作。
- 阻塞（`Blocked`）：在程序等待进入同步区域的时候，线程将进入这种状态，在等待监视器锁。
- 结束（`Terminated`）：已终止线程的线程状态，线程已经结束执行。

**Dump文件的线程状态一般其实就以下3种：**

- `RUNNABLE`，线程处于执行中
- `BLOCKED`，线程被阻塞
- `WAITING`，线程正在等待

**Dump 文件分析关注重点**

- `runnable`，线程处于执行中
- `deadlock`，死锁（重点关注）
- `blocked`，线程被阻塞 （重点关注）
- `Parked`，停止
- `locked`，对象加锁
- `waiting`，线程正在等待
- `waiting to lock` 等待上锁
- `Object.wait()`，对象等待中
- `waiting for monitor entry` 等待获取监视器（重点关注）
- `Waiting on condition`，等待资源（重点关注），最常见的情况是线程在等待网络的读写

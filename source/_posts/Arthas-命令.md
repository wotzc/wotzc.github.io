---
title: Arthas 命令
date: 2024-01-08 10:01:57
tags:
- Arthas
- Java诊断工具
- 线上问题排查
- JVM
- 反编译
categories: Java诊断工具
---

# 命令列表

## jvm 相关

- [dashboard](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Dashboard-%E5%91%BD%E4%BB%A4) - 当前系统的实时数据面板
- [getstatic](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Getstatic-%E5%91%BD%E4%BB%A4) - 查看类的静态属性
- [heapdump](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#dump-%E5%91%BD%E4%BB%A4) - dump java heap, 类似 jmap 命令的 heap dump 功能
- [jvm](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Jvm-%E5%91%BD%E4%BB%A4) - 查看当前 JVM 的信息
- [logger](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Logger-%E5%91%BD%E4%BB%A4) - 查看和修改 logger
- [mbean](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Mbean-%E5%91%BD%E4%BB%A4) - 查看 Mbean 的信息
- [memory](http://localhost:4000/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#memory-%E5%91%BD%E4%BB%A4) - 查看 JVM 的内存信息
- [ognl](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#ognl-%E5%91%BD%E4%BB%A4) - 执行 ognl 表达式
- [perfcounter](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Perfcounter-%E5%91%BD%E4%BB%A4) - 查看当前 JVM 的 Perf Counter 信息
- [sysenv](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Sysenv-%E5%91%BD%E4%BB%A4) - 查看 JVM 的环境变量
- [sysprop](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#sysprop-%E5%91%BD%E4%BB%A4) - 查看和修改 JVM 的系统属性
- [thread](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#thread-%E5%91%BD%E4%BB%A4) - 查看当前 JVM 的线程堆栈信息
- [vmoption](http://localhost:4000/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Vmoption-%E5%91%BD%E4%BB%A4) - 查看和修改 JVM 里诊断相关的 option
- [vmtool](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Vmoption-%E5%91%BD%E4%BB%A4) - 从 jvm 里查询对象，执行 forceGc

## class/classloader 相关

- [classloader](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Classloader-%E5%91%BD%E4%BB%A4) - 查看 classloader 的继承树，urls，类加载信息，使用 classloader 去 getResource
- [dump](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#dump-%E5%91%BD%E4%BB%A4) - dump 已加载类的 byte code 到特定目录
- [jad](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Jad-%E5%91%BD%E4%BB%A4) - 反编译指定已加载类的源码
- [mc](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#mc) - 内存编译器，内存编译`.java`文件为`.class`文件
- [redefine](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#redefine) - 加载外部的`.class`文件，redefine 到 JVM 里
- [retransform](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#retransform-%E5%91%BD%E4%BB%A4) - 加载外部的`.class`文件，retransform 到 JVM 里
- [sc](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Sc-%E5%91%BD%E4%BB%A4) - 查看 JVM 已加载的类信息
- [sm](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Sm-%E5%91%BD%E4%BB%A4) - 查看已加载类的方法信息

## monitor/watch/trace 相关

注意

请注意，这些命令，都通过字节码增强技术来实现的，会在指定类的方法中插入一些切面来实现数据统计和观测，因此在线上、预发使用时，请尽量明确需要观测的类、方法以及条件，诊断结束要执行 `stop` 或将增强过的类执行 `reset` 命令。

- [monitor](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Monitor-%E5%91%BD%E4%BB%A4) - 方法执行监控
- [stack](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Stack-%E5%91%BD%E4%BB%A4) - 输出当前方法被调用的调用路径
- [trace](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#trace-%E5%91%BD%E4%BB%A4) - 方法内部调用路径，并输出方法路径上的每个节点上耗时
- [tt](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#tt-%E5%91%BD%E4%BB%A4) - 方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测
- [watch](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#watch-%E5%91%BD%E4%BB%A4) - 方法执行数据观测

## profiler/火焰图

- [profiler](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Profiler-%E5%91%BD%E4%BB%A4) - 使用[async-profiler在新窗口打开](https://github.com/jvm-profiling-tools/async-profiler)对应用采样，生成火焰图
- [jfr](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#jfr-%E5%91%BD%E4%BB%A4) - 动态开启关闭 JFR 记录

## 鉴权

- [auth](https://arthas.aliyun.com/doc/auth.html) - 鉴权

## options

- [options](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Options-%E5%91%BD%E4%BB%A4) - 查看或设置 Arthas 全局开关

## 管道

Arthas 支持使用管道对上述命令的结果进行进一步的处理，如`sm java.lang.String * | grep 'index'`

- [grep](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Grep-%E5%91%BD%E4%BB%A4) - 搜索满足条件的结果
- plaintext - 将命令的结果去除 ANSI 颜色
- wc - 按行统计输出结果

## 后台异步任务

当线上出现偶发的问题，比如需要 watch 某个条件，而这个条件一天可能才会出现一次时，异步后台任务就派上用场了，详情请参考[这里](https://arthas.aliyun.com/doc/async.html)

- 使用 `>` 将结果重写向到日志文件，使用 `&` 指定命令是后台运行，session 断开不影响任务执行（生命周期默认为 1 天）
- jobs - 列出所有 job
- kill - 强制终止任务
- fg - 将暂停的任务拉到前台执行
- bg - 将暂停的任务放到后台执行

## 基础命令

- [base64](https://arthas.aliyun.com/doc/base64.html) - base64 编码转换，和 linux 里的 base64 命令类似
- [cat](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Cat-%E5%91%BD%E4%BB%A4) - 打印文件内容，和 linux 里的 cat 命令类似
- [cls](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Cls-%E5%91%BD%E4%BB%A4) - 清空当前屏幕区域
- [echo](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#echo-%E5%91%BD%E4%BB%A4) - 打印参数，和 linux 里的 echo 命令类似
- [grep](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Grep-%E5%91%BD%E4%BB%A4) - 匹配查找，和 linux 里的 grep 命令类似
- [help](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Help-%E5%91%BD%E4%BB%A4) - 查看命令帮助信息
- [history](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#history-%E5%91%BD%E4%BB%A4) - 打印命令历史
- [keymap](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Keymap-%E5%91%BD%E4%BB%A4) - Arthas 快捷键列表及自定义快捷键
- [pwd](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Pwd-%E5%91%BD%E4%BB%A4) - 返回当前的工作目录，和 linux 命令类似
- [quit](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Quit-stop-%E5%91%BD%E4%BB%A4) - 退出当前 Arthas 客户端，其他 Arthas 客户端不受影响
- [reset](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Reset-%E5%91%BD%E4%BB%A4) - 重置增强类，将被 Arthas 增强过的类全部还原，Arthas 服务端关闭时会重置所有增强过的类
- [session](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#session-%E5%91%BD%E4%BB%A4) - 查看当前会话的信息
- [stop](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#Quit-stop-%E5%91%BD%E4%BB%A4) - 关闭 Arthas 服务端，所有 Arthas 客户端全部退出
- [tee](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#tee-%E5%91%BD%E4%BB%A4) - 复制标准输入到标准输出和指定的文件，和 linux 里的 tee 命令类似
- [version](https://tianzhencai.xyz/2024/01/08/Arthas-%E5%91%BD%E4%BB%A4/#version-%E5%91%BD%E4%BB%A4) - 输出当前目标 Java 进程所加载的 Arthas 版本号

---------------

# Cat 命令

{% note info no-icon %}

提示

打印文件内容，和 linux 里的 cat 命令类似。

{% endnote %}

使用参考：

```
$ cat /tmp/a.txt
```

# Classloader 命令

## 列出所有 ClassLoader

```
classloader -l
```

## 统计 ClassLoader 实际使用 URL 和未使用的 URL

```
classloader --url-stat
```

> 注意：基于 JVM 目前已加载的所有类统计，不代表 Unused URLs 可以从应用中删掉。因为可能将来需要从 Unused URLs 里加载类，或者需要加载 resources

## 列出 ClassLoader 里加载的所有类

列出上面的`org.apache.jasper.servlet.JasperLoader` 加载的类：

```
classloader -a --classLoaderClass org.apache.jasper.servlet.JasperLoader | grep hello
```

## 查看类的 classloader 层次

```
sc -d org.apache.jsp.jsp.hello_jsp
```

## 查看 ClassLoader 树

```
classloader -t
```

## 查看 URLClassLoader 实际的 urls

比如上面查看到的 spring LaunchedURLClassLoader 为 `org.springframework.boot.loader.LaunchedURLClassLoader` ，可以通过 `-c <hashcode>` 参数来指定 classloader，还有一种方法可以通过使用 `--classLoaderClass` 指定类名，从而查看 URLClassLoader 实际的 urls：

```
classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader
```

## 查找 ClassLoader 里的资源文件

查找指定的资源文件： `classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r logback-spring.xml`

也可以尝试查找类的 class 文件：

```
classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r java/lang/String.class
```

# Cls 命令

通过 `cls` 命令 可以清空当前屏幕区域。

```
cls
```

---------

# Grep 命令

{% note info no-icon %}

提示

类似传统的 grep 命令。

{% endnote %}

## 使用参考

```text
 USAGE:
   grep [-A <value>] [-B <value>] [-C <value>] [-h] [-i] [-v] [-n] [-m <value>] [-e] [--trim-end] pattern

 SUMMARY:
   grep command for pipes.

 EXAMPLES:
  sysprop | grep java
  sysprop | grep java -n
  sysenv | grep -v JAVA
  sysenv | grep -e "(?i)(JAVA|sun)" -m 3  -C 2
  sysenv | grep JAVA -A2 -B3
  thread | grep -m 10 -e  "TIMED_WAITING|WAITING"

 WIKI:
   https://arthas.aliyun.com/doc/grep

 OPTIONS:
 -A, --after-context <value>                                                    Print NUM lines of trailing context)
 -B, --before-context <value>                                                   Print NUM lines of leading context)
 -C, --context <value>                                                          Print NUM lines of output context)
 -h, --help                                                                     this help
 -i, --ignore-case                                                              Perform case insensitive matching.  By default, grep is case sensitive.
 -v, --invert-match                                                             Select non-matching lines
 -n, --line-number                                                              Print line number with output lines
 -m, --max-count <value>                                                        stop after NUM selected lines)
 -e, --regex                                                                    Enable regular expression to match
     --trim-end                                                                 Remove whitespaces at the end of the line
 <pattern>                                                                      Pattern
```

### 匹配展示符合范本样式的项

```
[arthas@13372]$ sysprop | grep java
 java.specification.version                        1.8
 java.class.path                                   math-game.jar
 java.vm.vendor                                    Oracle Corporation
 java.vendor.url                                   http://java.oracle.com/
 java.vm.specification.version                     1.8
 sun.java.launcher                                 SUN_STANDARD
 sun.java.command                                  math-game.jar
 java.specification.vendor                         Oracle Corporation
 java.home                                         D:\developInstall\JDK\jdk1.8.0_161\jre
 java.vm.specification.vendor                      Oracle Corporation
 java.specification.name                           Java Platform API Specification
 java.awt.graphicsenv                              sun.awt.Win32GraphicsEnvironment
 java.runtime.version                              1.8.0_161-b12
 java.endorsed.dirs                                D:\developInstall\JDK\jdk1.8.0_161\jre\lib\endorsed
 java.runtime.name                                 Java(TM) SE Runtime Environment
 java.vm.name                                      Java HotSpot(TM) 64-Bit Server VM
 java.vendor.url.bug                               http://bugreport.sun.com/bugreport/
 java.io.tmpdir                                    C:\Users\PT-TIA~1\AppData\Local\Temp\
 java.version                                      1.8.0_161
 java.vm.specification.name                        Java Virtual Machine Specification
 java.awt.printerjob                               sun.awt.windows.WPrinterJob
 java.library.path                                 D:\developInstall\JDK\jdk1.8.0_161\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;C:\Python27\;C:\Python27\Scripts;D:\app\pt-tianzhencai\product\11.1.0\db_1\bin;C:\Program Files (x86)\Comm
                                                   on Files\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Program Files\TortoiseSVN\bin;C:\Progr
 java.vm.info                                      mixed mode
 java.vendor                                       Oracle Corporation
 java.vm.version                                   25.161-b12
 java.ext.dirs                                     D:\developInstall\JDK\jdk1.8.0_161\jre\lib\ext;C:\Windows\Sun\Java\lib\ext
 java.class.version                                52.0
```

### `-n` 命令显示行号：

```
sysprop | grep java -n
```

### `-v` 展示非匹配

```
sysenv | grep -v JAVA
```

### `-e` 使用正则表达式匹配，`-m` 设定最大展示条数，

```
sysenv | grep -e "(?i)(JAVA|sun)" -m 3 -C 2
thread | grep -m 10 -e "TIMED_WAITING|WAITING"
```

### 除了显示符合范本样式的那一行之外，`-A` 指定显示该行之后的内容，`-B` 指定显示该行之前的内容。

```
sysenv | grep JAVA -A2 -B3
```

# Dashboard 命令

dashboard 命令可以查看当前系统的实时数据面板。

当运行在 Ali-tomcat 时，会显示当前 tomcat 的实时信息，如 HTTP 请求的 qps, rt, 错误数, 线程池信息等等。

## 参数说明

| 参数名称 | 参数说明                                 |
| -------: | :--------------------------------------- |
|     [i:] | 刷新实时数据的时间间隔 (ms)，默认 5000ms |
|     [n:] | 刷新实时数据的次数                       |

## 使用参考

```text
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

## 数据说明

- ID: Java 级别的线程 ID，注意这个 ID 不能跟 jstack 中的 nativeID 一一对应。
- NAME: 线程名
- GROUP: 线程组名
- PRIORITY: 线程优先级, 1~10 之间的数字，越大表示优先级越高
- STATE: 线程的状态
- CPU%: 线程的 cpu 使用率。比如采样间隔 1000ms，某个线程的增量 cpu 时间为 100ms，则 cpu 使用率=100/1000=10%
- DELTA_TIME: 上次采样之后线程运行增量 CPU 时间，数据格式为`秒`
- TIME: 线程运行总 CPU 时间，数据格式为`分:秒`
- INTERRUPTED: 线程当前的中断位状态
- DAEMON: 是否是 daemon 线程

### JVM 内部线程

Java 8 之后支持获取 JVM 内部线程 CPU 时间，这些线程只有名称和 CPU 时间，没有 ID 及状态等信息（显示 ID 为-1）。 通过内部线程可以观测到 JVM 活动，如 GC、JIT 编译等占用 CPU 情况，方便了解 JVM 整体运行状况。

- 当 JVM 堆(heap)/元数据(metaspace)空间不足或 OOM 时，可以看到 GC 线程的 CPU 占用率明显高于其他的线程。
- 当执行`trace/watch/tt/redefine`等命令后，可以看到 JIT 线程活动变得更频繁。因为 JVM 热更新 class 字节码时清除了此 class 相关的 JIT 编译结果，需要重新编译。

JVM 内部线程包括下面几种：

- JIT 编译线程: 如 `C1 CompilerThread0`, `C2 CompilerThread0`
- GC 线程: 如`GC Thread0`, `G1 Young RemSet Sampling`
- 其它内部线程: 如`VM Periodic Task Thread`, `VM Thread`, `Service Thread`

---------

# dump 命令

{% note info no-icon %}

提示

dump 已加载类的 bytecode 到特定目录

{% endnote %}

dump 命令将 JVM 中实际运行的 class 的 byte code dump 到指定目录，适用场景批量下载指定包目录的 class 字节码；如需反编译单一类、实时查看类信息，可参考 [jad](https://arthas.aliyun.com/doc/jad.html)。

## 参数说明

|              参数名称 | 参数说明                                   |
| --------------------: | :----------------------------------------- |
|       *class-pattern* | 类名表达式匹配                             |
|                `[c:]` | 类所属 ClassLoader 的 hashcode             |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name |
|                `[d:]` | 设置类文件的目标目录                       |
|                   [E] | 开启正则表达式匹配，默认为通配符匹配       |

## 使用参考

```bash
$ dump java.lang.String
 HASHCODE  CLASSLOADER  LOCATION
 null                   /Users/admin/logs/arthas/classdump/java/lang/String.class
Affect(row-cnt:1) cost in 119 ms.
```

```bash
$ dump demo.*
 HASHCODE  CLASSLOADER                                    LOCATION
 3d4eac69  +-sun.misc.Launcher$AppClassLoader@3d4eac69    /Users/admin/logs/arthas/classdump/sun.misc.Launcher$AppClassLoader-3d4eac69/demo/MathGame.class
             +-sun.misc.Launcher$ExtClassLoader@66350f69
Affect(row-cnt:1) cost in 39 ms.
```

```bash
$ dump -d /tmp/output java.lang.String
 HASHCODE  CLASSLOADER  LOCATION
 null                   /tmp/output/java/lang/String.class
Affect(row-cnt:1) cost in 138 ms.
```

- 指定 classLoader

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。

如果你使用`-c`，你需要手动输入 hashcode：`-c <hashcode>`

```bash
$ dump -c 3d4eac69 demo.*
```

对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass`指定 class name，使用起来更加方便：

```bash
$ dump --classLoaderClass sun.misc.Launcher$AppClassLoader demo.*
 HASHCODE  CLASSLOADER                                    LOCATION
 3d4eac69  +-sun.misc.Launcher$AppClassLoader@3d4eac69    /Users/admin/logs/arthas/classdump/sun.misc.Launcher$AppClassLoader-3d4eac69/demo/MathGame.class
             +-sun.misc.Launcher$ExtClassLoader@66350f69
Affect(row-cnt:1) cost in 39 ms.
```

- 注：这里 classLoaderClass 在 java 8 是 sun.misc.Launcher$AppClassLoader，而 java 11 的 classloader 是 jdk.internal.loader.ClassLoaders$AppClassLoader，killercoda 目前环境是 java11。

`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>`是动态变化的。

--------

# echo 命令

{% note info no-icon %}

提示

打印参数，和 linux 里的 echo 命令类似。

{% endnote %}

## 使用参考

```bash
$ echo 'hello'
```

# Getstatic 命令

- 推荐直接使用[ognl](https://arthas.aliyun.com/doc/arthas-tutorials.html?language=cn&id=ognl)命令，更加灵活。

通过 `getstatic` 命令可以方便的查看类的静态属性。使用方法为`getstatic class_name field_name`

```bash
$ getstatic demo.MathGame random
field: random
@Random[
    serialVersionUID=@Long[3905348978240129619],
    seed=@AtomicLong[120955813885284],
    multiplier=@Long[25214903917],
    addend=@Long[11],
    mask=@Long[281474976710655],
    DOUBLE_UNIT=@Double[1.1102230246251565E-16],
    BadBound=@String[bound must be positive],
    BadRange=@String[bound must be greater than origin],
    BadSize=@String[size must be non-negative],
    seedUniquifier=@AtomicLong[-3282039941672302964],
    nextNextGaussian=@Double[0.0],
    haveNextNextGaussian=@Boolean[false],
    serialPersistentFields=@ObjectStreamField[][isEmpty=false;size=3],
    unsafe=@Unsafe[sun.misc.Unsafe@2eaa1027],
    seedOffset=@Long[24],
]
```

- 指定 classLoader

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，使用`sc -d <ClassName>`提取对应 ClassLoader 的 hashcode。

如果你使用`-c`，你需要手动输入 hashcode：`-c <hashcode>`

```bash
$ getstatic -c 3d4eac69 demo.MathGame random
```

对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass`指定 class name，使用起来更加方便：

```
getstatic --classLoaderClass sun.misc.Launcher$AppClassLoader demo.MathGame random
```

- 注: 这里 classLoaderClass 在 java 8 是 sun.misc.Launcher$AppClassLoader，而java 11的classloader是jdk.internal.loader.ClassLoaders$AppClassLoader，killercoda 目前环境是 java11。

`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>`是动态变化的。

-----

# Help 命令

查看命令帮助信息，可以查看当前 arthas 版本支持的指令，或者查看具体指令的使用说明。

{% note info no-icon %}

提示

[help 指令]的等同于[指令 -help]，都是查看具体指令的使用说明。

{% endnote %}

## 参数说明

| 参数名称 | 参数说明                                   |
| -------: | :----------------------------------------- |
| 不接参数 | 查询当前 arthas 版本支持的指令以及指令描述 |
|  [name:] | 查询具体指令的使用说明                     |

## 使用参考

```
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

# history 命令

打印命令历史。

{% note info no-icon %}

提示

历史指令会通过一个名叫 history 的文件持久化，所以 history 指令可以查看当前 arthas 服务器的所有历史命令，而不仅只是当前次会话使用过的命令。

{% endnote %}

## 参数说明

| 参数名称 | 参数说明                |
| -------: | :---------------------- |
|     [c:] | 清空历史指令            |
|     [n:] | 显示最近执行的 n 条指令 |

## 使用参考

```text
#查看最近执行的3条指令
$ history 3
  269  thread
  270  cls
  271  history 3
```

```text
 #清空指令
 $ history -c
 $ history 3
  1  history 3
```

# Jad 命令

`jad` 命令 将 JVM 中实际运行的 class 的 byte code 反编译成 java 代码，便于你理解业务逻辑；

- 在 Arthas Console 上，反编译出来的源码是带语法高亮的，阅读更方便
- 当然，反编译出来的 java 代码可能会存在语法错误，但不影响你进行阅读理解

## 参数说明

|              参数名称 | 参数说明                                   |
| --------------------: | :----------------------------------------- |
|       *class-pattern* | 类名表达式匹配                             |
|                `[c:]` | 类所属 ClassLoader 的 hashcode             |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name |
|                   [E] | 开启正则表达式匹配，默认为通配符匹配       |

## 使用参考

### 反编译`java.lang.String`

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

### 反编译时只显示源代码

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

### 反编译指定的函数

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

### 反编译时不显示行号

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

### 反编译时指定 ClassLoader

{% note info no-icon %}

提示

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

# jfr 命令

{% note info no-icon %}

提示

Java Flight Recorder (JFR) 是一种用于收集有关正在运行的 Java 应用程序的诊断和分析数据的工具。它集成到 Java 虚拟机 (JVM) 中，几乎不会造成性能开销，因此即使在负载较重的生产环境中也可以使用。

{% endnote %}

`jfr` 命令支持在程序动态运行过程中开启和关闭 JFR 记录。 记录收集有关 event 的数据。事件在特定时间点发生在 JVM 或 Java 应用程序中。每个事件都有一个名称、一个时间戳和一个可选的有效负载。负载是与事件相关的数据，例如 CPU 使用率、事件前后的 Java 堆大小、锁持有者的线程 ID 等。

```
jfr 命令基本运行结构是 jfr cmd [actionArg]
```

> 注意： JDK8 的 8u262 版本之后才支持 jfr

## 参数说明

|      参数名称 | 参数说明                                                     |
| ------------: | :----------------------------------------------------------- |
|         *cmd* | 要执行的操作 支持的命令【start，status，dump，stop】         |
|   *actionArg* | 属性名模式                                                   |
|          [n:] | 记录名称                                                     |
|          [r:] | 记录 id 值                                                   |
| [dumponexit:] | 程序退出时，是否要 dump 出 .jfr 文件，默认为 false           |
|          [d:] | 延迟多久后启动 JFR 记录，支持带单位配置，eg: 60s, 2m, 5h, 3d. 不带单位就是秒，默认无延迟 |
|   [duration:] | JFR 记录持续时间，支持单位配置，不带单位就是秒，默认一直记录 |
|          [s:] | 采集 Event 的详细配置文件，默认是 default.jfc 位于 `$JAVA_HOME/lib/jfr/default.jfc` |
|          [f:] | 将输出转储到指定路径                                         |
|     [maxage:] | 缓冲区数据最大文件记录保存时间，支持单位配置，不带单位就是秒，默认是不限制 |
|    [maxsize:] | 缓冲区的最大文件大小，支持单位配置， 不带单位是字节，m 或者 M 代表 MB，g 或者 G 代表 GB。 |
|      [state:] | jfr 记录状态                                                 |

## 启动 JFR 记录

```text
$ jfr start
Started recording 1. No limit specified, using maxsize=250MB as default.
```

{% note info no-icon %}

提示

默认情况下，开启的是默认参数的 jfr 记录

{% endnote %}

启动 jfr 记录，指定记录名，记录持续时间，记录文件保存路径。

```text
$ jfr start -n myRecording --duration 60s -f /tmp/myRecording.jfr
Started recording 2. The result will be written to:
/tmp/myRecording.jfr
```

## 查看 JFR 记录状态

默认是查看所有 JFR 记录信息

```bash
$ jfr status
Recording: recording=1 name=Recording-1 (running)
Recording: recording=2 name=myRecording duration=PT1M (closed)
```

查看指定记录 id 的记录信息

```bash
$ jfr status -r 1
Recording: recording=1 name=Recording-1 (running)
```

查看指定状态的记录信息

```bash
$ jfr status --state closed
Recording: recording=2 name=myRecording duration=PT1M (closed)
```

## dump jfr 记录

`jfr dump` 会输出从开始到运行该命令这段时间内的记录到 JFR 文件，且不会停止 `jfr` 的记录
指定记录输出路径

```bash
$ jfr dump -r 1 -f /tmp/myRecording1.jfr
Dump recording 1, The result will be written to:
/tmp/myRecording1.jfr
```

不指定文件输出路径，默认是保存到`arthas-output`目录下

```bash
$ jfr dump -r 1
Dump recording 1, The result will be written to:
/tmp/test/arthas-output/20220819-200915.jfr
```

## 停止 jfr 记录

不指定记录输出路径，默认是保存到`arthas-output`目录下

```bash
$ jfr stop -r 1
Stop recording 1, The result will be written to:
/tmp/test/arthas-output/20220819-202049.jfr
```

> 注意一条记录只能停止一次。

也可以指定记录输出路径。

## 通过浏览器查看 arthas-output 下面 JFR 记录的结果

默认情况下，arthas 使用 8563 端口，则可以打开： http://localhost:8563/arthas-output/在新窗口打开  查看到`arthas-output`目录下面的 JFR 记录结果：

![img](https://arthas.aliyun.com/images/arthas-output-recording.png)

生成的结果可以用支持 jfr 格式的工具来查看。比如：

- JDK Mission Control ： https://github.com/openjdk/jmc

-----------

# Jvm 命令

{% note info no-icon %}

提示

查看当前 JVM 信息

{% endnote %}

## 使用参考

```text
$ jvm
RUNTIME
--------------------------------------------------------------------------------------------------------------
 MACHINE-NAME                   37@ff267334bb65
 JVM-START-TIME                 2020-07-23 07:50:36
 MANAGEMENT-SPEC-VERSION        1.2
 SPEC-NAME                      Java Virtual Machine Specification
 SPEC-VENDOR                    Oracle Corporation
 SPEC-VERSION                   1.8
 VM-NAME                        Java HotSpot(TM) 64-Bit Server VM
 VM-VENDOR                      Oracle Corporation
 VM-VERSION                     25.201-b09
 INPUT-ARGUMENTS                []
 CLASS-PATH                     demo-arthas-spring-boot.jar
 BOOT-CLASS-PATH                /usr/lib/jvm/java-8-oracle/jre/lib/resources.jar:/usr/lib/jvm/java-8-oracle/j
                                re/lib/rt.jar:/usr/lib/jvm/java-8-oracle/jre/lib/sunrsasign.jar:/usr/lib/jvm/
                                java-8-oracle/jre/lib/jsse.jar:/usr/lib/jvm/java-8-oracle/jre/lib/jce.jar:/us
                                r/lib/jvm/java-8-oracle/jre/lib/charsets.jar:/usr/lib/jvm/java-8-oracle/jre/l
                                ib/jfr.jar:/usr/lib/jvm/java-8-oracle/jre/classes
 LIBRARY-PATH                   /usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib

--------------------------------------------------------------------------------------------------------------
 CLASS-LOADING
--------------------------------------------------------------------------------------------------------------
 LOADED-CLASS-COUNT             7529
 TOTAL-LOADED-CLASS-COUNT       7529
 UNLOADED-CLASS-COUNT           0
 IS-VERBOSE                     false

--------------------------------------------------------------------------------------------------------------
 COMPILATION
--------------------------------------------------------------------------------------------------------------
 NAME                           HotSpot 64-Bit Tiered Compilers
 TOTAL-COMPILE-TIME             14921(ms)

--------------------------------------------------------------------------------------------------------------
 GARBAGE-COLLECTORS
--------------------------------------------------------------------------------------------------------------
 PS Scavenge                            name : PS Scavenge
 [count/time (ms)]                      collectionCount : 7
                                        collectionTime : 68

 PS MarkSweep                           name : PS MarkSweep
 [count/time (ms)]                      collectionCount : 1
                                        collectionTime : 47

--------------------------------------------------------------------------------------------------------------
 MEMORY-MANAGERS
--------------------------------------------------------------------------------------------------------------
 CodeCacheManager               Code Cache

 Metaspace Manager              Metaspace
                                Compressed Class Space

 Copy                           Eden Space
                                Survivor Space

 MarkSweepCompact               Eden Space
                                Survivor Space
                                Tenured Gen


--------------------------------------------------------------------------------------------------------------
 MEMORY
--------------------------------------------------------------------------------------------------------------
 HEAP-MEMORY-USAGE                      init : 268435456(256.0 MiB)
 [memory in bytes]                      used : 18039504(17.2 MiB)
                                        committed : 181403648(173.0 MiB)
                                        max : 3817865216(3.6 GiB)

 NO-HEAP-MEMORY-USAGE                   init : 2555904(2.4 MiB)
 [memory in bytes]                      used : 33926216(32.4 MiB)
                                        committed : 35176448(33.5 MiB)
                                        max : -1(-1 B)

--------------------------------------------------------------------------------------------------------------
 OPERATING-SYSTEM
--------------------------------------------------------------------------------------------------------------
 OS                             Linux
 ARCH                           amd64
 PROCESSORS-COUNT               3
 LOAD-AVERAGE                   29.53
 VERSION                        4.15.0-52-generic

--------------------------------------------------------------------------------------------------------------
 THREAD
--------------------------------------------------------------------------------------------------------------
 COUNT                          30
 DAEMON-COUNT                   24
 PEAK-COUNT                     31
 STARTED-COUNT                  36
 DEADLOCK-COUNT                 0

--------------------------------------------------------------------------------------------------------------
 FILE-DESCRIPTOR
--------------------------------------------------------------------------------------------------------------
 MAX-FILE-DESCRIPTOR-COUNT      1048576
 OPEN-FILE-DESCRIPTOR-COUNT     100
Affect(row-cnt:0) cost in 88 ms.
```

## THREAD 相关

- COUNT: JVM 当前活跃的线程数
- DAEMON-COUNT: JVM 当前活跃的守护线程数
- PEAK-COUNT: 从 JVM 启动开始曾经活着的最大线程数
- STARTED-COUNT: 从 JVM 启动开始总共启动过的线程次数
- DEADLOCK-COUNT: JVM 当前死锁的线程数

## 文件描述符相关

- MAX-FILE-DESCRIPTOR-COUNT：JVM 进程最大可以打开的文件描述符数
- OPEN-FILE-DESCRIPTOR-COUNT：JVM 当前打开的文件描述符数

----

# Keymap 命令

`keymap`命令输出当前的快捷键映射表：

默认的快捷键如下：

| 快捷键        | 快捷键说明       | 命令名称             | 命令说明                         |
| ------------- | ---------------- | -------------------- | -------------------------------- |
| `"\C-a"`      | ctrl + a         | beginning-of-line    | 跳到行首                         |
| `"\C-e"`      | ctrl + e         | end-of-line          | 跳到行尾                         |
| `"\C-f"`      | ctrl + f         | forward-word         | 向前移动一个单词                 |
| `"\C-b"`      | ctrl + b         | backward-word        | 向后移动一个单词                 |
| `"\e[D"`      | 键盘左方向键     | backward-char        | 光标向前移动一个字符             |
| `"\e[C"`      | 键盘右方向键     | forward-char         | 光标向后移动一个字符             |
| `"\e[B"`      | 键盘下方向键     | next-history         | 下翻显示下一个命令               |
| `"\e[A"`      | 键盘上方向键     | previous-history     | 上翻显示上一个命令               |
| `"\C-h"`      | ctrl + h         | backward-delete-char | 向后删除一个字符                 |
| `"\C-?"`      | ctrl + shift + / | backward-delete-char | 向后删除一个字符                 |
| `"\C-u"`      | ctrl + u         | undo                 | 撤销上一个命令，相当于清空当前行 |
| `"\C-d"`      | ctrl + d         | delete-char          | 删除当前光标所在字符             |
| `"\C-k"`      | ctrl + k         | kill-line            | 删除当前光标到行尾的所有字符     |
| `"\C-i"`      | ctrl + i         | complete             | 自动补全，相当于敲`TAB`          |
| `"\C-j"`      | ctrl + j         | accept-line          | 结束当前行，相当于敲回车         |
| `"\C-m"`      | ctrl + m         | accept-line          | 结束当前行，相当于敲回车         |
| `"\C-w"`      |                  | backward-delete-word |                                  |
| `"\C-x\e[3~"` |                  | backward-kill-line   |                                  |
| `"\e\C-?"`    |                  | backward-kill-word   |                                  |

- 任何时候 `tab` 键，会根据当前的输入给出提示
- 命令后敲 `-` 或 `--` ，然后按 `tab` 键，可以展示出此命令具体的选项

## 自定义快捷键

在当前用户目录下新建`$USER_HOME/.arthas/conf/inputrc`文件，加入自定义配置。

假设我是 vim 的重度用户，我要把`ctrl+h`设置为光标向前一个字符，则设置如下，首先拷贝默认配置

```text
"\C-a": beginning-of-line
"\C-e": end-of-line
"\C-f": forward-word
"\C-b": backward-word
"\e[D": backward-char
"\e[C": forward-char
"\e[B": next-history
"\e[A": previous-history
"\C-h": backward-delete-char
"\C-?": backward-delete-char
"\C-u": undo
"\C-d": delete-char
"\C-k": kill-line
"\C-i": complete
"\C-j": accept-line
"\C-m": accept-line
"\C-w": backward-delete-word
"\C-x\e[3~": backward-kill-line
"\e\C-?": backward-kill-word
```

然后把`"\C-h": backward-delete-char`换成`"\C-h": backward-char`，然后重新连接即可。

## 后台异步命令相关快捷键

- ctrl + c: 终止当前命令
- ctrl + z: 挂起当前命令，后续可以 bg/fg 重新支持此命令，或 kill 掉
- ctrl + a: 回到行首
- ctrl + e: 回到行尾

# Logger 命令

{% note info no-icon %}

提示

查看 logger 信息，更新 logger level

{% endnote %}

## 使用参考

### 查看所有 logger 信息

以下面的`logback.xml`为例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="APPLICATION" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>app.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
            <maxFileSize>100MB</maxFileSize>
            <maxHistory>60</maxHistory>
            <totalSizeCap>2GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="APPLICATION" />
    </appender>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n
            </pattern>
            <charset>utf8</charset>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="ASYNC" />
    </root>
</configuration>
```

使用`logger`命令打印的结果是：

```bash
[arthas@2062]$ logger
 name                                   ROOT
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  INFO
 effectiveLevel                         INFO
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar
 appenders                              name            CONSOLE
                                        class           ch.qos.logback.core.ConsoleAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        target          System.out
                                        name            APPLICATION
                                        class           ch.qos.logback.core.rolling.RollingFileAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        file            app.log
                                        name            ASYNC
                                        class           ch.qos.logback.classic.AsyncAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        appenderRef     [APPLICATION]
```

从`appenders`的信息里，可以看到

- `CONSOLE` logger 的 target 是`System.out`
- `APPLICATION` logger 是`RollingFileAppender`，它的 file 是`app.log`
- `ASYNC`它的`appenderRef`是`APPLICATION`，即异步输出到文件里

### 查看指定名字的 logger 信息

```bash
[arthas@2062]$ logger -n org.springframework.web
 name                                   org.springframework.web
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  null
 effectiveLevel                         INFO
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar
```

### 查看指定 classloader 的 logger 信息

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。

如果你使用`-c`，你需要手动输入 hashcode：`-c <hashcode>`

```bash
[arthas@2062]$ logger -c 2a139a55
 name                                   ROOT
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  DEBUG
 effectiveLevel                         DEBUG
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar
 appenders                              name            CONSOLE
                                        class           ch.qos.logback.core.ConsoleAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        target          System.out
                                        name            APPLICATION
                                        class           ch.qos.logback.core.rolling.RollingFileAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        file            app.log
                                        name            ASYNC
                                        class           ch.qos.logback.classic.AsyncAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        appenderRef     [APPLICATION]
```

对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass`指定 class name，使用起来更加方便：

```
logger --classLoaderClass sun.misc.Launcher$AppClassLoader
```

- 注: 这里 classLoaderClass 在 java 8 是 sun.misc.Launcher$AppClassLoader，而java 11的classloader是jdk.internal.loader.ClassLoaders$AppClassLoader。

`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>`是动态变化的。

### 更新 logger level

```bash
[arthas@2062]$ logger --name ROOT --level debug
update logger level success.
```

### 指定 classloader 更新 logger level

默认情况下，logger 命令会在 SystemClassloader 下执行，如果应用是传统的`war`应用，或者 spring boot fat jar 启动的应用，那么需要指定 classloader。

可以先用 `sc -d yourClassName` 来查看具体的 classloader hashcode，然后在更新 level 时指定 classloader：

```bash
[arthas@2062]$ logger -c 2a139a55 --name ROOT --level debug
```

### 查看没有 appender 的 logger 的信息

默认情况下，`logger`命令只打印有 appender 的 logger 的信息。如果想查看没有`appender`的 logger 的信息，可以加上参数`--include-no-appender`。

注意，通常输出结果会很长。

```bash
[arthas@2062]$ logger --include-no-appender
 name                                   ROOT
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  DEBUG
 effectiveLevel                         DEBUG
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar
 appenders                              name            CONSOLE
                                        class           ch.qos.logback.core.ConsoleAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        target          System.out
                                        name            APPLICATION
                                        class           ch.qos.logback.core.rolling.RollingFileAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        file            app.log
                                        name            ASYNC
                                        class           ch.qos.logback.classic.AsyncAppender
                                        classLoader     sun.misc.Launcher$AppClassLoader@2a139a55
                                        classLoaderHash 2a139a55
                                        appenderRef     [APPLICATION]

 name                                   com
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  null
 effectiveLevel                         DEBUG
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar

 name                                   com.alibaba
 class                                  ch.qos.logback.classic.Logger
 classLoader                            sun.misc.Launcher$AppClassLoader@2a139a55
 classLoaderHash                        2a139a55
 level                                  null
 effectiveLevel                         DEBUG
 additivity                             true
 codeSource                             file:/Users/hengyunabc/.m2/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar
...
```

--------

# Mbean 命令

{% note info no-icon %}

提示

查看 Mbean 的信息

{% endnote %}

这个命令可以便捷的查看或监控 Mbean 的属性信息。

## 参数说明







--------

# mc-redefine 命令

## mc

> Memory Compiler/内存编译器，编译`.java` 文件生成`.class` 。

## 使用参考

```bash
mc /tmp/Test.java
```

可以通过`-c`参数指定 classloader：

```bash
mc -c 327a647b /tmp/Test.java
```

也可以通过`--classLoaderClass`参数指定 ClassLoader：

```bash
$ mc --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader /tmp/UserController.java -d /tmp
Memory compiler output:
/tmp/com/example/demo/arthas/user/UserController.class
Affect(row-cnt:1) cost in 346 ms
```

可以通过`-d`命令指定输出目录：

```bash
mc -d /tmp/output /tmp/ClassA.java /tmp/ClassB.java
```

编译生成`.class`文件之后，可以结合[retransform](https://arthas.aliyun.com/doc/retransform.html)命令实现热更新代码。

{% note warn no-icon %}

注意

注意，mc 命令有可能失败。如果编译失败可以在本地编译好 .class 文件，再上传到服务器。具体参考[retransform](https://arthas.aliyun.com/doc/retransform.html)命令说明。

{% endnote %}

## redefine

{% note info no-icon %}

提示

推荐使用 retransform 命令

{% endnote %}

- redefine 的 class 不能修改、添加、删除类的 field 和 method，包括方法参数、方法名称及返回值
- 如果 mc 失败，可以在本地开发环境编译好 class 文件，上传到目标系统，使用 redefine 热加载 class
- 目前 redefine 和 watch/trace/jad/tt 等命令冲突，以后重新实现 redefine 功能会解决此问题

{% note warn no-icon %}

注意

注意， redefine 后的原来的类不能恢复，redefine 有可能失败（比如增加了新的 field），参考 jdk 本身的文档。

{% endnote %}

{% note info no-icon %}

提示

`reset`命令对`redefine`的类无效。如果想重置，需要`redefine`原始的字节码。

{% endnote %}

{% note info no-icon %}

提示

`redefine`命令和`jad`/`watch`/`trace`/`monitor`/`tt`等命令会冲突。执行完`redefine`之后，如果再执行上面提到的命令，则会把`redefine`的字节码重置。 原因是 jdk 本身 redefine 和 Retransform 是不同的机制，同时使用两种机制来更新字节码，只有最后修改的会生效。

{% endnote %}

-----

# retransform 命令

{% note info no-icon %}

提示

加载外部的 .class 文件，retransform jvm 已加载的类。

{% endnote %}

## 使用参考

```bash
   retransform /tmp/Test.class
   retransform -l
   retransform -d 1                    # delete retransform entry
   retransform --deleteAll             # delete all retransform entries
   retransform --classPattern demo.*   # triger retransform classes
   retransform -c 327a647b /tmp/Test.class /tmp/Test\$Inner.class
   retransform --classLoaderClass 'sun.misc.Launcher$AppClassLoader' /tmp/Test.class
```

## retransform 指定的 .class 文件

```bash
$ retransform /tmp/MathGame.class
retransform success, size: 1, classes:
demo.MathGame
```

加载指定的 .class 文件，然后解析出 class name，再 retransform jvm 中已加载的对应的类。每加载一个 `.class` 文件，则会记录一个 retransform entry.

{% note info no-icon %}

提示

如果多次执行 retransform 加载同一个 class 文件，则会有多条 retransform entry.

{% endnote %}

## 查看 retransform entry

```bash
$ retransform -l
Id              ClassName       TransformCount  LoaderHash      LoaderClassName
1               demo.MathGame   1               null            null
```

- TransformCount 统计在 ClassFileTransformer#transform 函数里尝试返回 entry 对应的 .class 文件的次数，但并不表明 transform 一定成功。

## 删除指定 retransform entry

需要指定 id：

```bash
retransform -d 1
```

## 删除所有 retransform entry

```bash
retransform --deleteAll
```

## 显式触发 retransform

```bash
$ retransform --classPattern demo.MathGame
retransform success, size: 1, classes:
demo.MathGame
```

> 注意：对于同一个类，当存在多个 retransform entry 时，如果显式触发 retransform ，则最后添加的 entry 生效(id 最大的)。

## 消除 retransform 的影响

如果对某个类执行 retransform 之后，想消除影响，则需要：

- 删除这个类对应的 retransform entry
- 重新触发 retransform

{% note info no-icon %}

提示

如果不清除掉所有的 retransform entry，并重新触发 retransform ，则 arthas stop 时，retransform 过的类仍然生效。

{% endnote %}

## 结合 jad/mc 命令使用

```bash
jad --source-only com.example.demo.arthas.user.UserController > /tmp/UserController.java

mc /tmp/UserController.java -d /tmp

retransform /tmp/com/example/demo/arthas/user/UserController.class
```

- jad 命令反编译，然后可以用其它编译器，比如 vim 来修改源码
- mc 命令来内存编译修改过的代码
- 用 retransform 命令加载新的字节码

## 上传 .class 文件到服务器的技巧

使用`mc`命令来编译`jad`的反编译的代码有可能失败。可以在本地修改代码，编译好后再上传到服务器上。有的服务器不允许直接上传文件，可以使用`base64`命令来绕过。

1. 在本地先转换`.class`文件为 base64，再保存为 result.txt

   ```bash
   base64 < Test.class > result.txt
   ```

2. 到服务器上，新建并编辑`result.txt`，复制本地的内容，粘贴再保存

3. 把服务器上的 `result.txt`还原为`.class`

   ```text
   base64 -d < result.txt > Test.class
   ```

4. 用 md5 命令计算哈希值，校验是否一致

## retransform 的限制

- 不允许新增加 field/method
- 正在跑的函数，没有退出不能生效，比如下面新增加的`System.out.println`，只有`run()`函数里的会生效

```java
public class MathGame {
    public static void main(String[] args) throws InterruptedException {
        MathGame game = new MathGame();
        while (true) {
            game.run();
            TimeUnit.SECONDS.sleep(1);
            // 这个不生效，因为代码一直跑在 while里
            System.out.println("in loop");
        }
    }

    public void run() throws InterruptedException {
        // 这个生效，因为run()函数每次都可以完整结束
        System.out.println("call run()");
        try {
            int number = random.nextInt();
            List<Integer> primeFactors = primeFactors(number);
            print(number, primeFactors);

        } catch (Exception e) {
            System.out.println(String.format("illegalArgumentCount:%3d, ", illegalArgumentCount) + e.getMessage());
        }
    }
}
```

---------

# Monitor 命令

{% note info no-icon %}

提示

方法执行监控

{% endnote %}

对匹配 `class-pattern`／`method-pattern`／`condition-express`的类、方法的调用进行监控。

`monitor` 命令是一个非实时返回命令.

实时返回命令是输入之后立即返回，而非实时返回的命令，则是不断的等待目标 Java 进程返回信息，直到用户输入 `Ctrl+C` 为止。

服务端是以任务的形式在后台跑任务，植入的代码随着任务的中止而不会被执行，所以任务关闭后，不会对原有性能产生太大影响，而且原则上，任何 Arthas 命令不会引起原有业务逻辑的改变。

## 监控的维度说明

|    监控项 | 说明                       |
| --------: | :------------------------- |
| timestamp | 时间戳                     |
|     class | Java 类                    |
|    method | 方法（构造方法、普通方法） |
|     total | 调用次数                   |
|   success | 成功次数                   |
|      fail | 失败次数                   |
|        rt | 平均 RT                    |
| fail-rate | 失败率                     |

## 参数说明

方法拥有一个命名参数 `[c:]`，意思是统计周期（cycle of output），拥有一个整型的参数值

|            参数名称 | 参数说明                                                     |
| ------------------: | :----------------------------------------------------------- |
|     *class-pattern* | 类名表达式匹配                                               |
|    *method-pattern* | 方法名表达式匹配                                             |
| *condition-express* | 条件表达式                                                   |
|                 [E] | 开启正则表达式匹配，默认为通配符匹配                         |
|              `[c:]` | 统计周期，默认值为 120 秒                                    |
|                 [b] | 在**方法调用之前**计算 condition-express                     |
|         `[m <arg>]` | 指定 Class 最大匹配数量，默认值为 50。长格式为`[maxMatch <arg>]`。 |

## 使用参考

```bash
$ monitor -c 5 demo.MathGame primeFactors
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 94 ms.
 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:38  demo.MathGame  primeFactors  5      1        4     1.15        80.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:43  demo.MathGame  primeFactors  5      3        2     42.29       40.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:48  demo.MathGame  primeFactors  5      3        2     67.92       40.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:53  demo.MathGame  primeFactors  5      2        3     0.25        60.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:06:58  demo.MathGame  primeFactors  1      1        0     0.45        0.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2018-12-03 19:07:03  demo.MathGame  primeFactors  2      2        0     3182.72     0.00%
```

### 指定 Class 最大匹配数量

```bash
$ monitor -c 1 -m 1 demo.MathGame primeFactors
Press Q or Ctrl+C to abort.
Affect(class count:1 , method count:1) cost in 384 ms, listenerId: 6.
 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2022-12-25 21:12:58  demo.MathGame  primeFactors  1      1        0     0.18        0.00%

 timestamp            class          method        total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2022-12-25 21:12:59  demo.MathGame  primeFactors  0      0        0     0.00       0.00%
```

### 计算条件表达式过滤统计结果(方法执行完毕之后)

```bash
monitor -c 5 demo.MathGame primeFactors "params[0] <= 2"
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 19 ms, listenerId: 5
 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
-----------------------------------------------------------------------------------------------
 2020-09-02 09:42:36  demo.MathGame  primeFactors    5       3       2      0.09       40.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:41  demo.MathGame  primeFactors    5       2       3      0.11       60.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:46  demo.MathGame  primeFactors    5       1       4      0.06       80.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:51  demo.MathGame  primeFactors    5       1       4      0.12       80.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:56  demo.MathGame  primeFactors    5       3       2      0.15       40.00%
```

### [#](https://arthas.aliyun.com/doc/monitor.html#计算条件表达式过滤统计结果-方法执行完毕之前)计算条件表达式过滤统计结果(方法执行完毕之前)

```bash
monitor -b -c 5 com.test.testes.MathGame primeFactors "params[0] <= 2"
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 21 ms, listenerId: 4
 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:41:57  demo.MathGame  primeFactors    1       0        1      0.10      100.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:02  demo.MathGame  primeFactors    3       0        3      0.06      100.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:07  demo.MathGame  primeFactors    2       0        2      0.06      100.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:12  demo.MathGame  primeFactors    1       0        1      0.05      100.00%

 timestamp            class          method         total  success  fail  avg-rt(ms)  fail-rate
----------------------------------------------------------------------------------------------
 2020-09-02 09:42:17  demo.MathGame  primeFactors    2       0        2      0.10      100.00%
```

------

# ognl 命令

在 Arthas 里，有一个单独的 ognl 命令，可以动态执行代码。

查看用法：`ognl --help`

## 参数说明

|              参数名称 | 参数说明                                                     |
| --------------------: | :----------------------------------------------------------- |
|             *express* | 执行的表达式                                                 |
|                `[c:]` | 执行表达式的 ClassLoader 的 hashcode，默认值是 SystemClassLoader |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name                   |
|                   [x] | 结果对象的展开层次，默认值 1                                 |

## 使用参考

- OGNL 特殊用法请参考：[https://github.com/alibaba/arthas/issues/71在新窗口打开](https://github.com/alibaba/arthas/issues/71)
- OGNL 表达式官方指南：[https://commons.apache.org/proper/commons-ognl/language-guide.html在新窗口打开](https://commons.apache.org/proper/commons-ognl/language-guide.html)

调用静态函数：

```bash
$ ognl '@java.lang.System@out.println("hello")'
null
```

获取静态类的静态字段：

```bash
$ ognl '@demo.MathGame@random'
@Random[
    serialVersionUID=@Long[3905348978240129619],
    seed=@AtomicLong[125451474443703],
    multiplier=@Long[25214903917],
    addend=@Long[11],
    mask=@Long[281474976710655],
    DOUBLE_UNIT=@Double[1.1102230246251565E-16],
    BadBound=@String[bound must be positive],
    BadRange=@String[bound must be greater than origin],
    BadSize=@String[size must be non-negative],
    seedUniquifier=@AtomicLong[-3282039941672302964],
    nextNextGaussian=@Double[0.0],
    haveNextNextGaussian=@Boolean[false],
    serialPersistentFields=@ObjectStreamField[][isEmpty=false;size=3],
    unsafe=@Unsafe[sun.misc.Unsafe@28ea5898],
    seedOffset=@Long[24],
]
```

通过 hashcode 指定 ClassLoader：

```bash
$ classloader -t
+-BootstrapClassLoader
+-jdk.internal.loader.ClassLoaders$PlatformClassLoader@301ec38b
  +-com.taobao.arthas.agent.ArthasClassloader@472067c7
  +-jdk.internal.loader.ClassLoaders$AppClassLoader@4b85612c
    +-org.springframework.boot.loader.LaunchedURLClassLoader@7f9a81e8

$ ognl -c 7f9a81e8 @org.springframework.boot.SpringApplication@logger
@Slf4jLocationAwareLog[
    FQCN=@String[org.apache.commons.logging.LogAdapter$Slf4jLocationAwareLog],
    name=@String[org.springframework.boot.SpringApplication],
    logger=@Logger[Logger[org.springframework.boot.SpringApplication]],
]
$
```

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。

对于只有唯一实例的 ClassLoader 可以通过 class name 指定，使用起来更加方便：

```bash
$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader  @org.springframework.boot.SpringApplication@logger
@Slf4jLocationAwareLog[
    FQCN=@String[org.apache.commons.logging.LogAdapter$Slf4jLocationAwareLog],
    name=@String[org.springframework.boot.SpringApplication],
    logger=@Logger[Logger[org.springframework.boot.SpringApplication]],
]
```

执行多行表达式，赋值给临时变量，返回一个 List：

```bash
$ ognl '#value1=@System@getProperty("java.home"), #value2=@System@getProperty("java.runtime.name"), {#value1, #value2}'
@ArrayList[
    @String[/opt/java/8.0.181-zulu/jre],
    @String[OpenJDK Runtime Environment],
]
```

# Options 命令

> 全局开关

| 名称                   | 默认值 | 描述                                                         |
| ---------------------- | ------ | ------------------------------------------------------------ |
| unsafe                 | false  | 是否支持对系统级别的类进行增强，打开该开关可能导致把 JVM 搞挂，请慎重选择！ |
| dump                   | false  | 是否支持被增强了的类 dump 到外部文件中，如果打开开关，class 文件会被 dump 到`/${application working dir}/arthas-class-dump/` 目录下，具体位置详见控制台输出 |
| batch-re-transform     | true   | 是否支持批量对匹配到的类执行 retransform 操作                |
| json-format            | false  | 是否支持 json 化的输出                                       |
| disable-sub-class      | false  | 是否禁用子类匹配，默认在匹配目标类的时候会默认匹配到其子类，如果想精确匹配，可以关闭此开关 |
| support-default-method | true   | 是否支持匹配到 default method，默认会查找 interface，匹配里面的 default method。参考 [#1105](https://github.com/alibaba/arthas/issues/1105) |
| save-result            | false  | 是否打开执行结果存日志功能，打开之后所有命令的运行结果都将保存到`~/logs/arthas-cache/result.log` 中 |
| job-timeout            | 1d     | 异步后台任务的默认超时时间，超过这个时间，任务自动停止；比如设置 1d, 2h, 3m, 25s，分别代表天、小时、分、秒 |
| print-parent-fields    | true   | 是否打印在 parent class 里的 filed                           |

## 查看所有的 options

```
options
```

## 获取 option 的值

```
options json-format
```

> 默认情况下`json-format` 为 false，如果希望`watch` /`tt` 等命令结果以 json 格式输出，则可以设置`json-format` 为 true。

## 设置指定的 option

例如，想打开执行结果存日志功能首先查看日志，发现无记录：

```
cat /root/logs/arthas-cache/result.log
```

输入如下命令即可激活记录日志功能：

```
options save-result true
```

稍候片刻，再次查看，发现出现记录：

```
cat /root/logs/arthas-cache/result.log
```

# Perfcounter 命令

查看当前 JVM 的 Perf Counter 信息

## 使用参考

```text
$ perfcounter
 java.ci.totalTime                            2325637411
 java.cls.loadedClasses                       3403
 java.cls.sharedLoadedClasses                 0
 java.cls.sharedUnloadedClasses               0
 java.cls.unloadedClasses                     0
 java.property.java.version                   11.0.4
 java.property.java.vm.info                   mixed mode
 java.property.java.vm.name                   OpenJDK 64-Bit Server VM
...
```

可以用`-d`参数打印更多信息：

```text
$ perfcounter -d
 Name                                   Variability   Units        Value
---------------------------------------------------------------------------------
 java.ci.totalTime                      Monotonic     Ticks        3242526906
 java.cls.loadedClasses                 Monotonic     Events       3404
 java.cls.sharedLoadedClasses           Monotonic     Events       0
 java.cls.sharedUnloadedClasses         Monotonic     Events       0
 java.cls.unloadedClasses               Monotonic     Events       0
```

## jdk9 以上的应用

如果没有打印出信息，应用在启动时，加下面的参数：

```text
--add-opens java.base/jdk.internal.perf=ALL-UNNAMED --add-exports java.base/jdk.internal.perf=ALL-UNNAMED --add-opens java.management/sun.management.counter.perf=ALL-UNNAMED --add-opens java.management/sun.management.counter=ALL-UNNAMED
```

# Plaintext 命令

将输出结果去除 ANSI 颜色

示例：

```
jad demo.MathGame main
jad demo.MathGame main | plaintext
```

# Profiler 命令

> 使用[async-profiler](https://github.com/jvm-profiling-tools/async-profiler)生成火焰图

`profiler` 命令支持生成应用热点的火焰图。本质上是通过不断的采样，然后把收集到的采样结果生成火焰图。

`profiler` 命令基本运行结构是 `profiler action [actionArg]`

## 参数说明

|    参数名称 | 参数说明                                                     |
| ----------: | :----------------------------------------------------------- |
|    *action* | 要执行的操作                                                 |
| *actionArg* | 属性名模式                                                   |
|        [i:] | 采样间隔（单位：ns）（默认值：10'000'000，即 10 ms）         |
|        [f:] | 将输出转储到指定路径                                         |
|        [d:] | 运行评测指定秒                                               |
|        [e:] | 要跟踪哪个事件（cpu, alloc, lock, cache-misses 等），默认是 cpu |

## 启动 profiler

```text
$ profiler start
Started [cpu] profiling
```

提示

默认情况下，生成的是 cpu 的火焰图，即 event 为`cpu`。可以用`--event`参数来指定。

## 获取已采集的 sample 的数量

```text
$ profiler getSamples
23
```

## 查看 profiler 状态

```bash
$ profiler status
[cpu] profiling is running for 4 seconds
```

可以查看当前 profiler 在采样哪种`event`和采样时间。

## 停止 profiler

### 生成 html 格式结果

默认情况下，结果文件是`html`格式，也可以用`--format`参数指定：

```bash
$ profiler stop --format html
profiler output file: /tmp/test/arthas-output/20211207-111550.html
OK
```

或者在`--file`参数里用文件名指名格式。比如`--file /tmp/result.html` 。

## 通过浏览器查看 arthas-output 下面的 profiler 结果

默认情况下，arthas 使用 3658 端口，则可以打开： [http://localhost:3658/arthas-output/在新窗口打开](http://localhost:3658/arthas-output/) 查看到`arthas-output`目录下面的 profiler 结果：

![img](https://arthas.aliyun.com/images/arthas-output.jpg)

点击可以查看具体的结果：

![img](https://arthas.aliyun.com/images/arthas-output-svg.jpg)

提示

如果是 chrome 浏览器，可能需要多次刷新。

## profiler 支持的 events

在不同的平台，不同的 OS 下面，支持的 events 各有不同。比如在 macos 下面：

```bash
$ profiler list
Basic events:
  cpu
  alloc
  lock
  wall
  itimer
```

在 linux 下面

```bash
$ profiler list
Basic events:
  cpu
  alloc
  lock
  wall
  itimer
Perf events:
  page-faults
  context-switches
  cycles
  instructions
  cache-references
  cache-misses
  branches
  branch-misses
  bus-cycles
  L1-dcache-load-misses
  LLC-load-misses
  dTLB-load-misses
  mem:breakpoint
  trace:tracepoint
```

如果遇到 OS 本身的权限/配置问题，然后  缺少部分 event，可以参考`async-profiler`本身文档：[async-profiler在新窗口打开](https://github.com/jvm-profiling-tools/async-profiler)

可以用`--event`参数指定要采样的事件，比如对`alloc`事件进入采样：

```bash
$ profiler start --event alloc
```

## 恢复采样

```bash
$ profiler resume
Started [cpu] profiling
```

`start`和`resume`的区别是：`start`是新开始采样，`resume`会保留上次`stop`时的数据。

通过执行`profiler getSamples`可以查看 samples 的数量来验证。

## 使用`execute`来执行复杂的命令

比如开始采样：

```bash
profiler execute 'start,framebuf=5000000'
```

停止采样，并保存到指定文件里：

```bash
profiler execute 'stop,file=/tmp/result.html'
```

具体的格式参考： [arguments.cpp在新窗口打开](https://github.com/jvm-profiling-tools/async-profiler/blob/v2.5/src/arguments.cpp#L50)

## 查看所有支持的 action

```bash
$ profiler actions
Supported Actions: [resume, dumpCollapsed, getSamples, start, list, execute, version, stop, load, dumpFlat, actions, dumpTraces, status]
```

## 查看版本

```bash
$ profiler version
Async-profiler 1.6 built on Sep  9 2019
Copyright 2019 Andrei Pangin
```

## 配置 framebuf 参数

> 如果遇到生成的火焰图有 `[frame_buffer_overflow]`，则需要增大 framebuf（默认值是 1'000'000），可以显式配置，比如：

```bash
profiler start --framebuf 5000000
```

## 配置 include/exclude 来过滤数据

如果应用比较复杂，生成的内容很多，想只关注部分数据，可以通过 include/exclude 来过滤。比如

```bash
profiler start --include 'java/*' --include 'demo/*' --exclude '*Unsafe.park*'
```

> include/exclude 都支持设置多个值 ，但是需要配置在命令行的最后。

## 指定执行时间

比如，希望 profiler 执行 300 秒自动结束，可以用 `-d`/`--duration` 参数指定：

```bash
profiler start --duration 300
```

## 生成 jfr 格式结果

> 注意，jfr 只支持在 `start`时配置。如果是在`stop`时指定，则不会生效。

```text
profiler start --file /tmp/test.jfr
```

`file`参数支持一些变量：

- 时间戳： `--file /tmp/test-%t.jfr`
- 进程 ID： `--file /tmp/test-%p.jfr`

生成的结果可以用支持 jfr 格式的工具来查看。比如：

- JDK Mission Control ： https://github.com/openjdk/jmc
- JProfiler ： https://github.com/alibaba/arthas/issues/1416

## 生成的火焰图里的 unknown

- https://github.com/jvm-profiling-tools/async-profiler/discussions/409

------

# Pwd 命令

通过 pwd 命令可以获知当前的工作目录，和 linux 命令类似

```
pwd
```

# Quit-stop 命令

## 退出 Arthas

用 `exit` 或者 `quit` 命令可以退出 Arthas。

退出 Arthas 之后，还可以再次用 `java -jar arthas-boot.jar` 来连接。

## 彻底退出 Arthas

exit/quit 命令 只是退出当前 session，arthas server 还在目标进程中运行。

想完全退出 Arthas，可以执行 `stop` 命令。

-----

# Reset 命令

{% note info no-icon %}

提示

重置增强类，将被 Arthas 增强过的类全部还原，Arthas 服务端`stop`时会重置所有增强过的类

{% endnote %}

## 使用参考

```text
$ reset -h
 USAGE:
   reset [-h] [-E] [class-pattern]

 SUMMARY:
   Reset all the enhanced classes

 EXAMPLES:
   reset
   reset *List
   reset -E .*List

 OPTIONS:
 -h, --help                                                         this help
 -E, --regex                                                        Enable regular expression to match (wildcard matching by default)
 <class-pattern>                                                    Path and classname of Pattern Matching
```

## 还原指定类

```text
$ trace Test test
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 57 ms.
`---ts=2017-10-26 17:10:33;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@14dad5dc
    `---[0.590102ms] Test:test()

`---ts=2017-10-26 17:10:34;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@14dad5dc
    `---[0.068692ms] Test:test()

$ reset Test
Affect(class-cnt:1 , method-cnt:0) cost in 11 ms.
```

## 还原所有类

```text
$ trace Test test
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 15 ms.
`---ts=2017-10-26 17:12:06;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@14dad5dc
    `---[0.128518ms] Test:test()

$ reset
Affect(class-cnt:1 , method-cnt:0) cost in 9 ms.
```

# Sc 命令

> 查看 JVM 已加载的类信息

“Search-Class”的简写，这个命令能搜索出所有已经加载到 JVM 中的 Class 信息，这个命令支持的参数有 `[d]` 、`[E]` 、`[f]` 和 `[x:]` 。

## 参数说明

| 参数名称              | 参数说明                                                     |
| --------------------- | ------------------------------------------------------------ |
| *class-pattern*       | 类名表达式匹配                                               |
| *method-pattern*      | 方法名表达式匹配                                             |
| [d]                   | 输出当前类的详细信息，包括这个类所加载的原始文件来源、类的声明、加载的 ClassLoader 等详细信息。 如果一个类被多个 ClassLoader 所加载，则会出现多次 |
| [E]                   | 开启正则表达式匹配，默认为通配符匹配                         |
| [f]                   | 输出当前类的成员变量信息（需要配合参数-d 一起使用）          |
| [x:]                  | 指定输出静态变量时属性的遍历深度，默认为 0，即直接使用 `toString` 输出 |
| `[c:]`                | 指定 class 的 ClassLoader 的 hashcode                        |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name                   |
| `[n:]`                | 具有详细信息的匹配类的最大数量（默认为 100）                 |

[sc 命令文档](https://arthas.aliyun.com/doc/sc.html)

> class-pattern 支持全限定名，如 com.taobao.test.AAA，也支持 com/taobao/test/AAA 这样的格式，这样，我们从异常堆栈里面把类名拷贝过来的时候，不需要在手动把`/` 替换为`.` 啦。

> sc 默认开启了子类匹配功能，也就是说所有当前类的子类也会被搜索出来，想要精确的匹配，请打开`options disable-sub-class true` 开关

## 使用参考

- 模糊搜索 `sc demo.*`
- 打印类的详细信息 `sc -d demo.MathGame`

## 指定 classLoader

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。
如果你使用`-c` ，你需要手动输入 hashcode：`-c <hashcode>`
对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass` 指定 class name，使用起来更加方便：

```
sc --classLoaderClass jdk.internal.loader.ClassLoaders$AppClassLoader -d demo*
```

- 注：这里 classLoaderClass 在 java 8 是 sun.misc.Launcher$AppClassLoader，而 java 11 的 classloader 是 jdk.internal.loader.ClassLoaders$AppClassLoader，killercoda 目前环境是 java11。

`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>` 是动态变化的。

- 打印出类的 Field 信息 `sc -d -f demo.MathGame`

-----------

# session 命令

查看当前会话的信息，显示当前绑定的 pid 以及会话 id。

> 提示
>
> 如果配置了 tunnel server，会追加打印 代理 id、tunnel 服务器的 url 以及连接状态。
>
> 如果使用了 staturl 做统计，会追加显示 statUrl 地址。

## 使用参考

```text
$ session
  Name        Value
--------------------------------------------------
 JAVA_PID    14584
 SESSION_ID  c2073d3b-443a-4a9b-9249-0c5d24a5756c
```

# Sm 命令

> 提示
>
> 查看已加载类的方法信息

“Search-Method” 的简写，这个命令能搜索出所有已经加载了 Class 信息的方法信息。

`sm` 命令只能看到由当前类所声明 (declaring) 的方法，父类则无法看到。

## 参数说明

|              参数名称 | 参数说明                                     |
| --------------------: | :------------------------------------------- |
|       *class-pattern* | 类名表达式匹配                               |
|      *method-pattern* | 方法名表达式匹配                             |
|                   [d] | 展示每个方法的详细信息                       |
|                   [E] | 开启正则表达式匹配，默认为通配符匹配         |
|                `[c:]` | 指定 class 的 ClassLoader 的 hashcode        |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name   |
|                `[n:]` | 具有详细信息的匹配类的最大数量（默认为 100） |

## 使用参考

```bash
$ sm java.lang.String
java.lang.String-><init>
java.lang.String->equals
java.lang.String->toString
java.lang.String->hashCode
java.lang.String->compareTo
java.lang.String->indexOf
java.lang.String->valueOf
java.lang.String->checkBounds
java.lang.String->length
java.lang.String->isEmpty
java.lang.String->charAt
java.lang.String->codePointAt
java.lang.String->codePointBefore
java.lang.String->codePointCount
java.lang.String->offsetByCodePoints
java.lang.String->getChars
java.lang.String->getBytes
java.lang.String->contentEquals
java.lang.String->nonSyncContentEquals
java.lang.String->equalsIgnoreCase
java.lang.String->compareToIgnoreCase
java.lang.String->regionMatches
java.lang.String->startsWith
java.lang.String->endsWith
java.lang.String->indexOfSupplementary
java.lang.String->lastIndexOf
java.lang.String->lastIndexOfSupplementary
java.lang.String->substring
java.lang.String->subSequence
java.lang.String->concat
java.lang.String->replace
java.lang.String->matches
java.lang.String->contains
java.lang.String->replaceFirst
java.lang.String->replaceAll
java.lang.String->split
java.lang.String->join
java.lang.String->toLowerCase
java.lang.String->toUpperCase
java.lang.String->trim
java.lang.String->toCharArray
java.lang.String->format
java.lang.String->copyValueOf
java.lang.String->intern
Affect(row-cnt:44) cost in 1342 ms.
```

```bash
$ sm -d java.lang.String toString
 declaring-class  java.lang.String
 method-name      toString
 modifier         public
 annotation
 parameters
 return           java.lang.String
 exceptions

Affect(row-cnt:1) cost in 3 ms.
```

----

# Stack 命令

{% note info no-icon %}

提示

输出当前方法被调用的调用路径

{% endnote %}

很多时候我们都知道一个方法被执行，但这个方法被执行的路径非常多，或者你根本就不知道这个方法是从那里被执行了，此时你需要的是 stack 命令。

## 参数说明

|            参数名称 | 参数说明                                                     |
| ------------------: | :----------------------------------------------------------- |
|     *class-pattern* | 类名表达式匹配                                               |
|    *method-pattern* | 方法名表达式匹配                                             |
| *condition-express* | 条件表达式                                                   |
|                 [E] | 开启正则表达式匹配，默认为通配符匹配                         |
|              `[n:]` | 执行次数限制                                                 |
|         `[m <arg>]` | 指定 Class 最大匹配数量，默认值为 50。长格式为`[maxMatch <arg>]`。 |

这里重点要说明的是观察表达式，观察表达式的构成主要由 ognl 表达式组成，所以你可以这样写`"{params,returnObj}"`，只要是一个合法的 ognl 表达式，都能被正常支持。

观察的维度也比较多，主要体现在参数 `advice` 的数据结构上。`Advice` 参数最主要是封装了通知节点的所有信息。

请参考[表达式核心变量](https://arthas.aliyun.com/doc/advice-class.html)中关于该节点的描述。

- 特殊用法请参考：[https://github.com/alibaba/arthas/issues/71在新窗口打开](https://github.com/alibaba/arthas/issues/71)
- OGNL 表达式官网：[https://commons.apache.org/proper/commons-ognl/language-guide.html在新窗口打开](https://commons.apache.org/proper/commons-ognl/language-guide.html)

## 使用例子

### stack

```bash
$ stack demo.MathGame primeFactors
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 36 ms.
ts=2018-12-04 01:32:19;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    @demo.MathGame.run()
        at demo.MathGame.main(MathGame.java:16)
```

### 指定 Class 最大匹配数量

```bash
$ stack demo.MathGame primeFactors -m 1
Press Q or Ctrl+C to abort.
Affect(class count:1 , method count:1) cost in 561 ms, listenerId: 5.
ts=2022-12-25 21:07:07;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@b4aac2
    @demo.MathGame.primeFactors()
        at demo.MathGame.run(MathGame.java:46)
        at demo.MathGame.main(MathGame.java:38)
```

### 据条件表达式来过滤

```bash
$ stack demo.MathGame primeFactors 'params[0]<0' -n 2
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 30 ms.
ts=2018-12-04 01:34:27;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    @demo.MathGame.run()
        at demo.MathGame.main(MathGame.java:16)

ts=2018-12-04 01:34:30;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    @demo.MathGame.run()
        at demo.MathGame.main(MathGame.java:16)

Command execution times exceed limit: 2, so command will exit. You can set it with -n option.
```

### 据执行时间来过滤

```bash
$ stack demo.MathGame primeFactors '#cost>5'
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 35 ms.
ts=2018-12-04 01:35:58;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    @demo.MathGame.run()
        at demo.MathGame.main(MathGame.java:16)
```

# Sysenv 命令

{% note info no-icon %}

提示

查看当前 JVM 的环境属性( System Environment Variables )

{% endnote %}

## 使用参考

```text
 USAGE:
   sysenv [-h] [env-name]

 SUMMARY:
   Display the system env.

 EXAMPLES:
   sysenv
   sysenv USER

 WIKI:
   https://arthas.aliyun.com/doc/sysenv

 OPTIONS:
 -h, --help                                                 this help
 <env-name>                                                 env name
```

### 查看所有环境变量

```text
$ sysenv
 KEY                      VALUE
----------------------------------------------------------------------------------------------------------------------------
 PATH                     /Users/admin/.sdkman/candidates/visualvm/current/bin:/Users/admin/.sdkman/candidates/ja
                          va/current/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Wireshark.app/Contents/
                          MacOS
 SDKMAN_VERSION           5.7.3+337
 JAVA_HOME                /Users/admin/.sdkman/candidates/java/current
 JAVA_MAIN_CLASS_65244    demo.MathGame
 TERM                     xterm-256color
 LANG                     zh_CN.UTF-8
 AUTOJUMP_SOURCED         1
 COLORTERM                truecolor
 LOGNAME                  admin
 XPC_SERVICE_NAME         0
 PWD                      /Users/admin/code/ali/arthas/demo
 TERM_PROGRAM_VERSION     3.2.5
 _                        /Users/admin/.sdkman/candidates/java/current/bin/java
 SHELL                    /bin/bash
 TERM_PROGRAM             iTerm.app
 SDKMAN_PLATFORM          Darwin
 USER                     admin
 ITERM_PROFILE            Default
 TMPDIR                   /var/folders/0r/k561bkk917gg972stqclbz9h0000gn/T/
 XPC_FLAGS                0x0
 TERM_SESSION_ID          w0t4p0:60BC264D-9649-42AC-A7E4-AF85B69F93F8
 __CF_USER_TEXT_ENCODING  0x1F5:0x19:0x34
 Apple_PubSub_Socket_Ren  /private/tmp/com.apple.launchd.DwmmjSQsll/Render
 der
 COLORFGBG                7;0
 HOME                     /Users/admin
 SHLVL                    1
 AUTOJUMP_ERROR_PATH      /Users/admin/Library/autojump/errors.log
```

### 查看单个环境变量

> 提示
>
> 支持通过`TAB`键自动补全

```text
$ sysenv USER
USER=admin
```

# sysprop 命令

{% note info no-icon %}

提示

查看当前 JVM 的系统属性( System Property )

{% endnote %}

## 使用参考

```text
 USAGE:
   sysprop [-h] [property-name] [property-value]

 SUMMARY:
   Display, and change all the system properties.

 EXAMPLES:
 sysprop
 sysprop file.encoding
 sysprop production.mode true

 WIKI:
   https://arthas.aliyun.com/doc/sysprop

 OPTIONS:
 -h, --help                                  this help
 <property-name>                             property name
 <property-value>                            property value
```

### 查看所有属性

```text
$ sysprop
 KEY                                                  VALUE
-------------------------------------------------------------------------------------------------------------------------------------
 java.runtime.name                                    Java(TM) SE Runtime Environment
 sun.boot.library.path                                /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre/lib
 java.vm.version                                      25.51-b03
 user.country.format                                  CN
 gopherProxySet                                       false
 java.vm.vendor                                       Oracle Corporation
 java.vendor.url                                      http://java.oracle.com/
 path.separator                                       :
 java.vm.name                                         Java HotSpot(TM) 64-Bit Server VM
 file.encoding.pkg                                    sun.io
 user.country                                         US
 sun.java.launcher                                    SUN_STANDARD
 sun.os.patch.level                                   unknown
 java.vm.specification.name                           Java Virtual Machine Specification
 user.dir                                             /private/var/tmp
 java.runtime.version                                 1.8.0_51-b16
 java.awt.graphicsenv                                 sun.awt.CGraphicsEnvironment
 java.endorsed.dirs                                   /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre/lib/endors
                                                      ed
 os.arch                                              x86_64
 java.io.tmpdir                                       /var/folders/2c/tbxwzs4s4sbcvh7frbcc7n000000gn/T/
 line.separator

 java.vm.specification.vendor                         Oracle Corporation
 os.name                                              Mac OS X
 sun.jnu.encoding                                     UTF-8
 java.library.path                                    /Users/wangtao/Library/Java/Extensions:/Library/Java/Extensions:/Network/Libra
                                                      ry/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
 sun.nio.ch.bugLevel
 java.specification.name                              Java Platform API Specification
 java.class.version                                   52.0
 sun.management.compiler                              HotSpot 64-Bit Tiered Compilers
 os.version                                           10.12.6
 user.home                                            /Users/wangtao
 user.timezone                                        Asia/Shanghai
 java.awt.printerjob                                  sun.lwawt.macosx.CPrinterJob
 file.encoding                                        UTF-8
 java.specification.version                           1.8
 user.name                                            wangtao
 java.class.path                                      .
 java.vm.specification.version                        1.8
 sun.arch.data.model                                  64
 java.home                                            /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre
 sun.java.command                                     Test
 java.specification.vendor                            Oracle Corporation
 user.language                                        en
 awt.toolkit                                          sun.lwawt.macosx.LWCToolkit
 java.vm.info                                         mixed mode
 java.version                                         1.8.0_51
 java.ext.dirs                                        /Users/wangtao/Library/Java/Extensions:/Library/Java/JavaVirtualMachines/jdk1.
                                                      8.0_51.jdk/Contents/Home/jre/lib/ext:/Library/Java/Extensions:/Network/Library
                                                      /Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java
 sun.boot.class.path                                  /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre/lib/resour
                                                      ces.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre/li
                                                      b/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/jre/l
                                                      ib/sunrsasign.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/H
                                                      ome/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Content
                                                      s/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Conte
                                                      nts/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jd
                                                      k/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_51.
                                                      jdk/Contents/Home/jre/classes
 java.vendor                                          Oracle Corporation
 file.separator                                       /
 java.vendor.url.bug                                  http://bugreport.sun.com/bugreport/
 sun.cpu.endian                                       little
 sun.io.unicode.encoding                              UnicodeBig
 sun.cpu.isalist
```

### 查看单个属性

> 提示
>
> 支持通过`TAB`键自动补全

```text
$ sysprop java.version
java.version=1.8.0_51
```

### 修改单个属性

```text
$ sysprop user.country
user.country=US
$ sysprop user.country CN
Successfully changed the system property.
user.country=CN
```

# tee 命令

类似传统的 [tee 命令](https://arthas.aliyun.com/doc/tee.html) 用于读取标准输入的数据，并将其内容输出成文件。

tee 指令会从标准输入设备读取数据，将其内容输出到标准输出设备，同时保存成文件。

使用 `tee -h` 查看帮助信息

## 命令示范

### 将 sysprop 命令执行结果另外存储在`/tmp/logfile` 中

```
sysprop | tee /tmp/logfile
```

查看`/tmp/logfile` 文件：

```
cat /tmp/logfile
```

### 将 sysprop 命令执行结果匹配`java` 后另外追加在`/tmp/logfile` 中

```
sysprop | grep java | tee -a /path/to/logfile
```

查看`/tmp/logfile` 文件：

```
cat /tmp/logfile
```

# thread 命令

查看当前线程信息，查看线程的堆栈

## 参数说明

| 参数名称 | 参数说明                                |
| -------- | --------------------------------------- |
| id       | 线程 id                                 |
| [n:]     | 指定最忙的前 N 个线程并打印堆栈         |
| [b]      | 找出当前阻塞其他线程的线程              |
| [i ]     | 指定 cpu 占比统计的采样间隔，单位为毫秒 |
| [--all]  | 显示所有匹配的线程                      |

## Thread 用法

### 支持一键展示当前最忙的前 N 个线程并打印堆栈：

```
thread -n 3
```

- 没有线程 ID，包含`[Internal]` 表示为 JVM 内部线程，参考`dashboard` 命令的介绍。
- `cpuUsage` 为采样间隔时间内线程的 CPU 使用率，与`dashboard` 命令的数据一致。
- `deltaTime` 为采样间隔时间内线程的增量 CPU 时间，小于 1ms 时被取整显示为 0ms。
- `time` 线程运行总 CPU 时间。

注意：线程栈为第二采样结束时获取，不能表明采样间隔时间内该线程都是在处理相同的任务。建议间隔时间不要太长，可能间隔时间越大越不准确。 可以根据具体情况尝试指定不同的间隔时间，观察输出结果。

### 当没有参数时，显示第一页线程信息

默认按照 CPU 增量时间降序排列，只显示第一页数据，避免滚屏。

```
thread
```

### thread --all, 显示所有匹配的线程

显示所有匹配线程信息，有时需要获取全部 JVM 的线程数据进行分析。

```
thread --all
```

### thread id，显示指定线程的运行堆栈

查看线程 ID 16 的栈：

```
thread 16
```

### thread -b, 找出当前阻塞其他线程的线程

有时候我们发现应用卡住了，通常是由于某个线程拿住了某个锁，并且其他线程都在等待这把锁造成的。为了排查这类问题，arthas 提供了`thread -b` ，一键找出那个罪魁祸首。

```
thread -b
```

**注意**，目前只支持找出 synchronized 关键字阻塞住的线程，如果是`java.util.concurrent.Lock` ，目前还不支持。

### thread -i, 指定采样时间间隔

- `thread -i 1000` : 统计最近 1000ms 内的线程 CPU 时间。

```
thread -i 1000
```

- `thread -n 3 -i 1000` : 列出 1000ms 内最忙的 3 个线程栈

```
thread -n 3 -i 1000
```

### thread –state，查看指定状态的线程

```
thread --state WAITING
```

--------

# trace 命令

{% note info no-icon %}

提示

方法内部调用路径，并输出方法路径上的每个节点上耗时

{% endnote %}

`trace` 命令能主动搜索 `class-pattern`／`method-pattern` 对应的方法调用路径，渲染和统计整个调用链路上的所有性能开销和追踪调用链路。

## 参数说明

|            参数名称 | 参数说明                                                     |
| ------------------: | :----------------------------------------------------------- |
|     *class-pattern* | 类名表达式匹配                                               |
|    *method-pattern* | 方法名表达式匹配                                             |
| *condition-express* | 条件表达式                                                   |
|                 [E] | 开启正则表达式匹配，默认为通配符匹配                         |
|              `[n:]` | 命令执行次数                                                 |
|             `#cost` | 方法执行耗时                                                 |
|         `[m <arg>]` | 指定 Class 最大匹配数量，默认值为 50。长格式为`[maxMatch <arg>]`。 |

这里重点要说明的是`条件表达式`，`条件表达式`的构成主要由 ognl 表达式组成，所以你可以这样写`"params[0]<0"`，只要是一个合法的 ognl 表达式，都能被正常支持。

请参考[表达式核心变量](https://arthas.aliyun.com/doc/advice-class.html)中关于该节点的描述。

- 特殊用法请参考：[https://github.com/alibaba/arthas/issues/71在新窗口打开](https://github.com/alibaba/arthas/issues/71)
- OGNL 表达式官网：[https://commons.apache.org/proper/commons-ognl/language-guide.html在新窗口打开](https://commons.apache.org/proper/commons-ognl/language-guide.html)

很多时候我们只想看到某个方法的 rt 大于某个时间之后的 trace 结果，现在 Arthas 可以按照方法执行的耗时来进行过滤了，例如`trace *StringUtils isBlank '#cost>100'`表示当执行时间超过 100ms 的时候，才会输出 trace 的结果。

{% note info no-icon %}

提示

watch/stack/trace 这个三个命令都支持`#cost`

{% endnote %}

## 注意事项

- `trace` 能方便的帮助你定位和发现因 RT 高而导致的性能问题缺陷，但其每次只能跟踪一级方法的调用链路。

  参考：[Trace 命令的实现原理在新窗口打开](https://github.com/alibaba/arthas/issues/597)

- 3.3.0 版本后，可以使用动态 Trace 功能，不断增加新的匹配类，参考下面的示例。

- 目前不支持 `trace java.lang.Thread getName`，参考 issue: [#1610在新窗口打开](https://github.com/alibaba/arthas/issues/1610) ，考虑到不是非常必要场景，且修复有一定难度，因此当前暂不修复

## 使用参考

### trace 函数

```bash
$ trace demo.MathGame run
Press Q or Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 28 ms.
`---ts=2019-12-04 00:45:08;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[0.617465ms] demo.MathGame:run()
        `---[0.078946ms] demo.MathGame:primeFactors() #24 [throws Exception]

`---ts=2019-12-04 00:45:09;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[1.276874ms] demo.MathGame:run()
        `---[0.03752ms] demo.MathGame:primeFactors() #24 [throws Exception]
```

{% note info no-icon %}

提示

结果里的 `#24`，表示在 run 函数里，在源文件的第`24`行调用了`primeFactors()`函数。

{% endnote %}

### 指定 Class 匹配的最大数量

```bash
$ trace demo.MathGame run -m 1
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 412 ms, listenerId: 4
`---ts=2022-12-25 21:00:00;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@b4aac2
    `---[0.762093ms] demo.MathGame:run()
        `---[30.21% 0.230241ms] demo.MathGame:primeFactors() #46 [throws Exception]

`---ts=2022-12-25 21:00:10;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@b4aac2
    `---[0.315298ms] demo.MathGame:run()
        `---[13.95% 0.043995ms] demo.MathGame:primeFactors() #46 [throws Exception]
```

### trace 次数限制

如果方法调用的次数很多，那么可以用`-n`参数指定捕捉结果的次数。比如下面的例子里，捕捉到一次调用就退出命令。

```bash
$ trace demo.MathGame run -n 1
Press Q or Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 20 ms.
`---ts=2019-12-04 00:45:53;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[0.549379ms] demo.MathGame:run()
        +---[0.059839ms] demo.MathGame:primeFactors() #24
        `---[0.232887ms] demo.MathGame:print() #25

Command execution times exceed limit: 1, so command will exit. You can set it with -n option.
```

### 包含 jdk 的函数

- `--skipJDKMethod <value> `skip jdk method trace, default value true.

默认情况下，trace 不会包含 jdk 里的函数调用，如果希望 trace jdk 里的函数，需要显式设置`--skipJDKMethod false`。

```bash
$ trace --skipJDKMethod false demo.MathGame run
Press Q or Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 60 ms.
`---ts=2019-12-04 00:44:41;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[1.357742ms] demo.MathGame:run()
        +---[0.028624ms] java.util.Random:nextInt() #23
        +---[0.045534ms] demo.MathGame:primeFactors() #24 [throws Exception]
        +---[0.005372ms] java.lang.StringBuilder:<init>() #28
        +---[0.012257ms] java.lang.Integer:valueOf() #28
        +---[0.234537ms] java.lang.String:format() #28
        +---[min=0.004539ms,max=0.005778ms,total=0.010317ms,count=2] java.lang.StringBuilder:append() #28
        +---[0.013777ms] java.lang.Exception:getMessage() #28
        +---[0.004935ms] java.lang.StringBuilder:toString() #28
        `---[0.06941ms] java.io.PrintStream:println() #28

`---ts=2019-12-04 00:44:42;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[3.030432ms] demo.MathGame:run()
        +---[0.010473ms] java.util.Random:nextInt() #23
        +---[0.023715ms] demo.MathGame:primeFactors() #24 [throws Exception]
        +---[0.005198ms] java.lang.StringBuilder:<init>() #28
        +---[0.006405ms] java.lang.Integer:valueOf() #28
        +---[0.178583ms] java.lang.String:format() #28
        +---[min=0.011636ms,max=0.838077ms,total=0.849713ms,count=2] java.lang.StringBuilder:append() #28
        +---[0.008747ms] java.lang.Exception:getMessage() #28
        +---[0.019768ms] java.lang.StringBuilder:toString() #28
        `---[0.076457ms] java.io.PrintStream:println() #28
```

### 根据调用耗时过滤

```bash
$ trace demo.MathGame run '#cost > 10'
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 41 ms.
`---ts=2018-12-04 01:12:02;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[12.033735ms] demo.MathGame:run()
        +---[0.006783ms] java.util.Random:nextInt()
        +---[11.852594ms] demo.MathGame:primeFactors()
        `---[0.05447ms] demo.MathGame:print()
```

{% note info no-icon %}

提示

只会展示耗时大于 10ms 的调用路径，有助于在排查问题的时候，只关注异常情况

{% endnote %}

- 是不是很眼熟，没错，在 JProfiler 等收费软件中你曾经见识类似的功能，这里你将可以通过命令就能打印出指定调用路径。 友情提醒下，`trace` 在执行的过程中本身是会有一定的性能开销，在统计的报告中并未像 JProfiler 一样预先减去其自身的统计开销。所以这统计出来有些许的不准，渲染路径上调用的类、方法越多，性能偏差越大。但还是能让你看清一些事情的。
- [12.033735ms] 的含义，`12.033735` 的含义是：当前节点在当前步骤的耗时，单位为毫秒
- [0,0,0ms,11]xxx:yyy() [throws Exception]，对该方法中相同的方法调用进行了合并，`0,0,0ms,11` 表示方法调用耗时，`min,max,total,count`；`throws Exception` 表明该方法调用中存在异常返回
- 这里存在一个统计不准确的问题，就是所有方法耗时加起来可能会小于该监测方法的总耗时，这个是由于 Arthas 本身的逻辑会有一定的耗时

### trace 多个类或者多个函数

trace 命令只会 trace 匹配到的函数里的子调用，并不会向下 trace 多层。因为 trace 是代价比较贵的，多层 trace 可能会导致最终要 trace 的类和函数非常多。

可以用正则表匹配路径上的多个类和函数，一定程度上达到多层 trace 的效果。

```bash
trace -E com.test.ClassA|org.test.ClassB method1|method2|method3
```

### 排除掉指定的类

使用 `--exclude-class-pattern` 参数可以排除掉指定的类，比如：

```bash
trace javax.servlet.Filter * --exclude-class-pattern com.demo.TestFilter
```

## 动态 trace

{% note info no-icon %}

提示

3.3.0 版本后支持。

{% endnote %}

打开终端 1，trace 上面 demo 里的`run`函数，可以看到打印出 `listenerId: 1`：

```bash
[arthas@59161]$ trace demo.MathGame run
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 112 ms, listenerId: 1
`---ts=2020-07-09 16:48:11;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[1.389634ms] demo.MathGame:run()
        `---[0.123934ms] demo.MathGame:primeFactors() #24 [throws Exception]

`---ts=2020-07-09 16:48:12;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[3.716391ms] demo.MathGame:run()
        +---[3.182813ms] demo.MathGame:primeFactors() #24
        `---[0.167786ms] demo.MathGame:print() #25
```

现在想要深入子函数`primeFactors`，可以打开一个新终端 2，使用`telnet localhost 3658`连接上 arthas，再 trace `primeFactors`时，指定`listenerId`。

```bash
[arthas@59161]$ trace demo.MathGame primeFactors --listenerId 1
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 34 ms, listenerId: 1
```

这时终端 2 打印的结果，说明已经增强了一个函数：`Affect(class count: 1 , method count: 1)`，但不再打印更多的结果。

再查看终端 1，可以发现 trace 的结果增加了一层，打印了`primeFactors`函数里的内容：

```bash
`---ts=2020-07-09 16:49:29;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[0.492551ms] demo.MathGame:run()
        `---[0.113929ms] demo.MathGame:primeFactors() #24 [throws Exception]
            `---[0.061462ms] demo.MathGame:primeFactors()
                `---[0.001018ms] throw:java.lang.IllegalArgumentException() #46

`---ts=2020-07-09 16:49:30;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@3d4eac69
    `---[0.409446ms] demo.MathGame:run()
        +---[0.232606ms] demo.MathGame:primeFactors() #24
        |   `---[0.1294ms] demo.MathGame:primeFactors()
        `---[0.084025ms] demo.MathGame:print() #25
```

通过指定`listenerId`的方式动态 trace，可以不断深入。另外 `watch`/`tt`/`monitor`等命令也支持类似的功能。

## trace 结果时间不准确问题

比如下面的结果里：`0.705196 > (0.152743 + 0.145825)`

```bash
$ trace demo.MathGame run -n 1
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 66 ms, listenerId: 1
`---ts=2021-02-08 11:27:36;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@232204a1
    `---[0.705196ms] demo.MathGame:run()
        +---[0.152743ms] demo.MathGame:primeFactors() #24
        `---[0.145825ms] demo.MathGame:print() #25
```

那么其它的时间消耗在哪些地方？

1. 没有被 trace 到的函数。比如`java.*` 下的函数调用默认会忽略掉。通过增加`--skipJDKMethod false`参数可以打印出来。

   ```bash
   $ trace demo.MathGame run --skipJDKMethod false
   Press Q or Ctrl+C to abort.
   Affect(class count: 1 , method count: 1) cost in 35 ms, listenerId: 2
   `---ts=2021-02-08 11:27:48;thread_name=main;id=1;is_daemon=false;priority=5;TCCL=sun.misc.Launcher$AppClassLoader@232204a1
       `---[0.810591ms] demo.MathGame:run()
           +---[0.034568ms] java.util.Random:nextInt() #23
           +---[0.119367ms] demo.MathGame:primeFactors() #24 [throws Exception]
           +---[0.017407ms] java.lang.StringBuilder:<init>() #28
           +---[0.127922ms] java.lang.String:format() #57
           +---[min=0.01419ms,max=0.020221ms,total=0.034411ms,count=2] java.lang.StringBuilder:append() #57
           +---[0.021911ms] java.lang.Exception:getMessage() #57
           +---[0.015643ms] java.lang.StringBuilder:toString() #57
           `---[0.086622ms] java.io.PrintStream:println() #57
   ```

2. 非函数调用的指令消耗。比如 `i++`, `getfield`等指令。

3. 在代码执行过程中，JVM 可能出现停顿，比如 GC，进入同步块等。

### 使用 -v 参数打印更多信息

{% note info no-icon %}

提示

watch/trace/monitor/stack/tt 命令都支持 `-v` 参数

{% endnote %}

当命令执行之后，没有输出结果。有两种可能：

1. 匹配到的函数没有被执行
2. 条件表达式结果是 false

但用户区分不出是哪种情况。

使用 `-v`选项，则会打印`Condition express`的具体值和执行结果，方便确认。

---------

# tt 命令

{% note info no-icon %}

提示

方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测

{% endnote %}

`watch` 虽然很方便和灵活，但需要提前想清楚观察表达式的拼写，这对排查问题而言要求太高，因为很多时候我们并不清楚问题出自于何方，只能靠蛛丝马迹进行猜测。

这个时候如果能记录下当时方法调用的所有入参和返回值、抛出的异常会对整个问题的思考与判断非常有帮助。

于是乎，TimeTunnel 命令就诞生了。

## 注意事项

- tt 命令的实现是：把函数的入参/返回值等，保存到一个`Map<Integer, TimeFragment>`里，默认的大小是 100。
- tt 相关功能在使用完之后，需要手动释放内存，否则长时间可能导致OOM。退出 arthas 不会自动清除 tt 的缓存 map。

## 使用参考

### 记录调用

对于一个最基本的使用来说，就是记录下当前方法的每次调用环境现场。



```bash
$ tt -t demo.MathGame primeFactors
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 66 ms.
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors
```

### 指定 Class 最大匹配数量

```bash
$ tt -t -m 1 demo.MathGame primeFactors
Press Q or Ctrl+C to abort.
Affect(class count:1 , method count:1) cost in 130 ms, listenerId: 1.
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2022-12-25 19:41:45  2.629929  true    false    0x3bf400       MathGame                       primeFactors
 1001    2022-12-25 19:41:55  0.146161  false   true     0x3bf400       MathGame                       primeFactors
```

- 命令参数解析

  - `-t`

    tt 命令有很多个主参数，`-t` 就是其中之一。这个参数的表明希望记录下类 `*Test` 的 `print` 方法的每次执行情况。

  - `-n 3`

    当你执行一个调用量不高的方法时可能你还能有足够的时间用 `CTRL+C` 中断 tt 命令记录的过程，但如果遇到调用量非常大的方法，瞬间就能将你的 JVM 内存撑爆。

    此时你可以通过 `-n` 参数指定你需要记录的次数，当达到记录次数时 Arthas 会主动中断 tt 命令的记录过程，避免人工操作无法停止的情况。

  - `-m 1`

    通过 `-m` 参数指定 Class 匹配的最大数量，防止匹配到的 Class 数量太多导致 JVM 挂起，默认值是 50。

- 表格字段说明

| 表格字段  | 字段解释                                                     |
| --------- | ------------------------------------------------------------ |
| INDEX     | 时间片段记录编号，每一个编号代表着一次调用，后续 tt 还有很多命令都是基于此编号指定记录操作，非常重要。 |
| TIMESTAMP | 方法执行的本机时间，记录了这个时间片段所发生的本机时间       |
| COST(ms)  | 方法执行的耗时                                               |
| IS-RET    | 方法是否以正常返回的形式结束                                 |
| IS-EXP    | 方法是否以抛异常的形式结束                                   |
| OBJECT    | 执行对象的`hashCode()`，注意，曾经有人误认为是对象在 JVM 中的内存地址，但很遗憾他不是。但他能帮助你简单的标记当前执行方法的类实体 |
| CLASS     | 执行的类名                                                   |
| METHOD    | 执行的方法名                                                 |

- 条件表达式

  不知道大家是否有在使用过程中遇到以下困惑

  - Arthas 似乎很难区分出重载的方法
  - 我只需要观察特定参数，但是 tt 却全部都给我记录了下来

  条件表达式也是用 `OGNL` 来编写，核心的判断对象依然是 `Advice` 对象。除了 `tt` 命令之外，`watch`、`trace`、`stack` 命令也都支持条件表达式。

- 解决方法重载

  `tt -t *Test print params.length==1`

  通过制定参数个数的形式解决不同的方法签名，如果参数个数一样，你还可以这样写

  `tt -t *Test print 'params[1] instanceof Integer'`

- 解决指定参数

  `tt -t *Test print params[0].mobile=="13989838402"`

- 构成条件表达式的 `Advice` 对象

  前边看到了很多条件表达式中，都使用了 `params[0]`，有关这个变量的介绍，请参考[表达式核心变量](https://arthas.aliyun.com/doc/advice-class.html)

### 检索调用记录

当你用 `tt` 记录了一大片的时间片段之后，你希望能从中筛选出自己需要的时间片段，这个时候你就需要对现有记录进行检索。

假设我们有这些记录

```bash
$ tt -l
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors
                              9
 1005    2018-12-04 11:15:43  0.4776    false   true     0x4b67cf4d     MathGame                       primeFactors
Affect(row-cnt:6) cost in 4 ms.
```

我需要筛选出 `primeFactors` 方法的调用信息

```bash
$ tt -s 'method.name=="primeFactors"'
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors
                              9
 1005    2018-12-04 11:15:43  0.4776    false   true     0x4b67cf4d     MathGame                       primeFactors
Affect(row-cnt:6) cost in 607 ms.
```

你需要一个 `-s` 参数。同样的，搜索表达式的核心对象依旧是 `Advice` 对象。

### 查看调用信息

对于具体一个时间片的信息而言，你可以通过 `-i` 参数后边跟着对应的 `INDEX` 编号查看到他的详细信息。

```bash
$ tt -i 1003
 INDEX            1003
 GMT-CREATE       2018-12-04 11:15:41
 COST(ms)         0.186073
 OBJECT           0x4b67cf4d
 CLASS            demo.MathGame
 METHOD           primeFactors
 IS-RETURN        false
 IS-EXCEPTION     true
 PARAMETERS[0]    @Integer[-564322413]
 THROW-EXCEPTION  java.lang.IllegalArgumentException: number is: -564322413, need >= 2
                      at demo.MathGame.primeFactors(MathGame.java:46)
                      at demo.MathGame.run(MathGame.java:24)
                      at demo.MathGame.main(MathGame.java:16)

Affect(row-cnt:1) cost in 11 ms.
```

### 重做一次调用

当你稍稍做了一些调整之后，你可能需要前端系统重新触发一次你的调用，此时得求爷爷告奶奶的需要前端配合联调的同学再次发起一次调用。而有些场景下，这个调用不是这么好触发的。

`tt` 命令由于保存了当时调用的所有现场信息，所以我们可以自己主动对一个 `INDEX` 编号的时间片自主发起一次调用，从而解放你的沟通成本。此时你需要 `-p` 参数。通过 `--replay-times` 指定 调用次数，通过 `--replay-interval` 指定多次调用间隔(单位 ms, 默认 1000ms)

```bash
$ tt -i 1004 -p
 RE-INDEX       1004
 GMT-REPLAY     2018-12-04 11:26:00
 OBJECT         0x4b67cf4d
 CLASS          demo.MathGame
 METHOD         primeFactors
 PARAMETERS[0]  @Integer[946738738]
 IS-RETURN      true
 IS-EXCEPTION   false
 COST(ms)         0.186073
 RETURN-OBJ     @ArrayList[
                    @Integer[2],
                    @Integer[11],
                    @Integer[17],
                    @Integer[2531387],
                ]
Time fragment[1004] successfully replayed.
Affect(row-cnt:1) cost in 14 ms.
```

你会发现结果虽然一样，但调用的路径发生了变化，由原来的程序发起变成了 Arthas 自己的内部线程发起的调用了。

### 观察表达式

`-w, --watch-express` 观察时空隧道使用`ognl` 表达式

- 使用[表达式核心变量](https://arthas.aliyun.com/doc/advice-class.html)中所有变量作为已知条件编写表达式。

```bash
[arthas@10718]$ tt -t demo.MathGame run -n 5
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 56 ms, listenerId: 1
 INDEX      TIMESTAMP                   COST(ms)     IS-RET     IS-EXP      OBJECT              CLASS                                     METHOD
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 1000       2021-01-08 21:54:17         0.901091     true       false       0x7699a589          MathGame                                  run
[arthas@10718]$ tt -w 'target.illegalArgumentCount'  -x 1 -i 1000
@Integer[60]
Affect(row-cnt:1) cost in 7 ms.
```

- 获取类的静态字段、调用类的静态方法

```bash
[arthas@10718]$ tt -t demo.MathGame run -n 5
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 56 ms, listenerId: 1
 INDEX      TIMESTAMP                   COST(ms)     IS-RET     IS-EXP      OBJECT              CLASS                                     METHOD
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 1000       2021-01-08 21:54:17         0.901091     true       false       0x7699a589          MathGame                                  run
[arthas@10718]$ tt -w '@demo.MathGame@random.nextInt(100)'  -x 1 -i 1000
@Integer[46]
```

注意这里使用 `com.taobao.arthas.core.advisor.Advice#getLoader`加载,使用精确`classloader` [ognl](https://arthas.aliyun.com/doc/ognl.html)更好。

高级用法 [获取 spring context 调用 bean 方法在新窗口打开](https://github.com/alibaba/arthas/issues/482)

- 需要强调的点

  1. **ThreadLocal 信息丢失**

     很多框架偷偷的将一些环境变量信息塞到了发起调用线程的 ThreadLocal 中，由于调用线程发生了变化，这些 ThreadLocal 线程信息无法通过 Arthas 保存，所以这些信息将会丢失。

     一些常见的 CASE 比如：鹰眼的 TraceId 等。

  2. **引用的对象**

     需要强调的是，`tt` 命令是将当前环境的对象引用保存起来，但仅仅也只能保存一个引用而已。如果方法内部对入参进行了变更，或者返回的对象经过了后续的处理，那么在 `tt` 查看的时候将无法看到当时最准确的值。这也是为什么 `watch` 命令存在的意义。

### 通过索引删除指定的 tt 记录

```text
tt -d 1001
```

### 清除所有的 tt 记录

```text
tt --delete-all
```

-----------

# version 命令

输出当前目标 Java 进程所加载的 Arthas 版本号

## 使用参考

```text
$ version
 3.5.1
```

# Vmoption 命令

## 使用参考

### 查看所有的 option

```bash
[arthas@56963]$ vmoption
 KEY                    VALUE                   ORIGIN                 WRITEABLE
---------------------------------------------------------------------------------------------
 HeapDumpBeforeFullGC   false                   DEFAULT                true
 HeapDumpAfterFullGC    false                   DEFAULT                true
 HeapDumpOnOutOfMemory  false                   DEFAULT                true
 Error
 HeapDumpPath                                   DEFAULT                true
 CMSAbortablePrecleanW  100                     DEFAULT                true
 aitMillis
 CMSWaitDuration        2000                    DEFAULT                true
 CMSTriggerInterval     -1                      DEFAULT                true
 PrintGC                false                   DEFAULT                true
 PrintGCDetails         true                    MANAGEMENT             true
 PrintGCDateStamps      false                   DEFAULT                true
 PrintGCTimeStamps      false                   DEFAULT                true
 PrintGCID              false                   DEFAULT                true
 PrintClassHistogramBe  false                   DEFAULT                true
 foreFullGC
 PrintClassHistogramAf  false                   DEFAULT                true
 terFullGC
 PrintClassHistogram    false                   DEFAULT                true
 MinHeapFreeRatio       0                       DEFAULT                true
 MaxHeapFreeRatio       100                     DEFAULT                true
 PrintConcurrentLocks   false                   DEFAULT                true
```

### 查看指定的 option

```bash
$ vmoption PrintGC
 KEY                 VALUE                ORIGIN              WRITEABLE
---------------------------------------------------------------------------------
 PrintGC             false                MANAGEMENT          true
```

### 更新指定的 option

```bash
$ vmoption PrintGC true
Successfully updated the vm option.
 NAME     BEFORE-VALUE  AFTER-VALUE
------------------------------------
 PrintGC  false         true
```

再使用`vmtool` 命令执行强制 GC，则可以在`Tab 1` 看到打印出 GC 日志：

```
vmtool --action forceGc
```

### 配置打印 GC 详情

```
vmoption PrintGCDetails true
```

再使用`vmtool` 命令执行强制 GC，则可以在`Tab 1` 看到打印出 GC 详情：

```
vmtool --action forceGc
```

---------

# vmtool 命令

{% note info no-icon %}

提示

@since 3.5.1

{% endnote %}

`vmtool` 利用`JVMTI`接口，实现查询内存对象，强制 GC 等功能。

## 查找 jvm 里的字符串对象

```
vmtool --action getInstances --className java.lang.String
```

## limit 参数

> 通过 `--limit` 参数，可以限制返回值数量，避免获取超大数据时对 JVM 造成压力。默认值是 10。

所以上面的命令实际上等值于：

```
vmtool --action getInstances --className java.lang.String --limit 10
```

如果设置`--limit` 为负数，则遍历所有对象。

下面使用`vmtool` 命令查找 spring 里的对象。

## 查找 spring context

```
vmtool --action getInstances --className org.springframework.context.ApplicationContext
```

## 指定返回结果展开层数

> `getInstances` action 返回结果绑定到`instances` 变量上，它是数组。

> 通过 `-x` /`--expand` 参数可以指定结果的展开层次，默认值是 1。

```
vmtool --action getInstances --className org.springframework.context.ApplicationContext -x 2
```

## 执行表达式

> `getInstances` action 返回结果绑定到`instances` 变量上，它是数组。可以通过`--express` 参数执行指定的表达式。

查找所有的 spring beans 名字：

```
vmtool --action getInstances --className org.springframework.context.ApplicationContext --express 'instances[0].getBeanDefinitionNames()'
```

调用`userController.findUserById(1)` 函数：

```
vmtool --action getInstances --className org.springframework.context.ApplicationContext --express 'instances[0].getBean("userController").findUserById(1)'
```

## 直接获取 UserController 并调用方法

```
vmtool --action getInstances --className com.example.demo.arthas.user.UserController --express 'instances[0].findUserById(1)'
```

## 指定 classloader

可以通过`sc` 命令查找到加载 class 的 classloader。

```
sc -d org.springframework.context.ApplicationContext
```

通过上面命令得到 `org.springframework.boot.loader.LaunchedURLClassLoader` hashcode 之后用`-c` / `--classloader` 参数指定，这里使用 `--classLoaderClass `来指定 classloader

```
vmtool --action getInstances --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader --className org.springframework.context.ApplicationContext
```

## 强制 GC

```
vmtool --action forceGc
```

## interrupt 指定线程

thread id 通过`-t`参数指定，可以使用 `thread`命令获取。

```bash
vmtool --action interruptThread -t 1
```

# watch 命令

> 方法执行数据观测

让你能方便的观察到指定方法的调用情况。能观察到的范围为：`返回值` 、`抛出异常` 、`入参` ，通过编写 OGNL 表达式进行对应变量的查看。

## 参数说明

watch 的参数比较多，主要是因为它能在 4 个不同的场景观察对象

| 参数名称            | 参数说明                                          |
| ------------------- | ------------------------------------------------- |
| *class-pattern*     | 类名表达式匹配                                    |
| *method-pattern*    | 方法名表达式匹配                                  |
| *express*           | 观察表达式，默认值：`{params, target, returnObj}` |
| *condition-express* | 条件表达式                                        |
| [b]                 | 在**方法调用之前**观察                            |
| [e]                 | 在**方法异常之后**观察                            |
| [s]                 | 在**方法返回之后**观察                            |
| [f]                 | 在**方法结束之后**(正常返回和异常返回) 观察       |
| [E]                 | 开启正则表达式匹配，默认为通配符匹配              |
| [x:]                | 指定输出结果的属性遍历深度，默认为 1              |

这里重点要说明的是观察表达式，观察表达式的构成主要由 ognl 表达式组成，所以你可以这样写`"{params,returnObj}"` ，只要是一个合法的 ognl 表达式，都能被正常支持。

观察的维度也比较多，主要体现在参数 `advice` 的数据结构上。`Advice` 参数最主要是封装了通知节点的所有信息。请参考[表达式核心变量](https://killercoda.com/arthas/course/arthas-tutorials-cn/advice-class.md)中关于该节点的描述。

- 特殊用法请参考：https://github.com/alibaba/arthas/issues/71
- OGNL 表达式官网：https://commons.apache.org/proper/commons-ognl/language-guide.html

**特别说明**：

- watch 命令定义了 4 个观察事件点，即 `-b` 方法调用前，`-e` 方法异常后，`-s` 方法返回后，`-f` 方法结束后
- 4 个观察事件点 `-b` 、`-e` 、`-s` 默认关闭，`-f` 默认打开，当指定观察点被打开后，在相应事件点会对观察表达式进行求值并输出
- 这里要注意`方法入参` 和`方法出参` 的区别，有可能在中间被修改导致前后不一致，除了 `-b` 事件点 `params` 代表方法入参外，其余事件都代表方法出参
- 当使用 `-b` 时，由于观察事件点是在方法调用前，此时返回值或异常均不存在

## 使用参考

### 观察方法出参、this 对象和返回值

> 观察表达式，默认值是`{params, target, returnObj}` 。

```
watch demo.MathGame primeFactors -x 2
```

按 `Q` 或者 `Ctrl+c` 退出

- 从运行结果里，说明函数被执行了两次，第一次结果是`location=AtExceptionExit` ，说明函数抛出异常了，因此`returnObj` 是 null
- 在第二次结果里是`location=AtExit` ，说明函数正常返回，因此可以看到`returnObj` 结果是一个 ArrayList

### 观察方法入参

```
watch demo.MathGame primeFactors "{params,returnObj}" -x 2 -b
```

按 `Q` 或者 `Ctrl+c` 退出

- 对比前一个例子，返回值为空（事件点为方法执行前，因此获取不到返回值）

### 同时观察方法调用前和方法返回后

```
watch demo.MathGame primeFactors "{params,target,returnObj}" -x 2 -b -s -n 2
```

- 参数里`-n 2` ，表示只执行两次
- 这里输出结果中，第一次输出的是方法调用前的观察表达式的结果，第二次输出的是方法返回后的表达式的结果
- 结果的输出顺序和事件发生的先后顺序一致，和命令中 `-s -b` 的顺序无关

### 调整`-x` 的值，观察具体的方法参数值

```
watch demo.MathGame primeFactors "{params,target}" -x 3
```

按 `Q` 或者 `Ctrl+c` 退出

- `-x` 表示遍历深度，可以调整来打印具体的参数和结果内容，默认值是 1。

### 条件表达式的例子

```
watch demo.MathGame primeFactors "{params[0],target}" "params[0]<0"
```

按 `Q` 或者 `Ctrl+c` 退出

- 只有满足条件的调用，才会有响应。
- `watch-express` 单个值可以不加'{}'，多个值需要加'{a,b,c}'。
- `condition-express` 不能加'{}'，可以使用逗号分隔子表达式，取表达式最后一个值来判断。
- 如果 watch 的方法存在同名的其它重载方法，可以通过下面的办法进行过滤：
- 根据参数类型进行过滤

```
watch demo.MathGame primeFactors '{params, params[0].class.name}' 'params[0].class.name == "java.lang.Integer"'
```

按 `Q` 或者 `Ctrl+c` 退出

- 根据参数个数进行过滤

```
watch demo.MathGame primeFactors '{params, params.length}' 'params.length==1'
```

按 `Q` 或者 `Ctrl+c` 退出

### 观察异常信息的例子

```
watch demo.MathGame primeFactors "{params[0],throwExp}" -e -x 2
```

按 `Q` 或者 `Ctrl+c` 退出

- `-e` 表示抛出异常时才触发
- express 中，表示异常信息的变量是`throwExp`

根据异常类型或者 message 进行过滤：

```
watch demo.MathGame primeFactors '{params, throwExp}' '#msg=throwExp.toString(), #msg.contains("IllegalArgumentException")' -e -x 2
```

按 `Q` 或者 `Ctrl+c` 退出

### 按照耗时进行过滤

```
watch demo.MathGame primeFactors '{params, returnObj}' '#cost>200' -x 2
```

按 `Q` 或者 `Ctrl+c` 退出

- `#cost>200` (单位是`ms` ) 表示只有当耗时大于 200ms 时才会输出，过滤掉执行时间小于 200ms 的调用

### 观察当前对象中的属性

```
watch demo.MathGame primeFactors 'target'
```

按 `Q` 或者 `Ctrl+c` 退出

如果想查看方法运行前后，当前对象中的属性，可以使用`target` 关键字，代表当前对象
然后使用`target.field_name` 访问当前对象的某个属性

```
watch demo.MathGame primeFactors 'target.illegalArgumentCount'
```

按 `Q` 或者 `Ctrl+c` 退出

# wc 命令

按行统计输出结果

通过 `jad demo.MathGame main` 输出 `demo.MathGame` 类的 `main` 方法

计算输出结果行数 `jad demo.MathGame main | wc -l`

# memory 命令

查看 JVM 内存信息。

## 使用参考

```text
$ memory
Memory                           used      total      max        usage
heap                             32M       256M       4096M      0.79%
g1_eden_space                    11M       68M        -1         16.18%
g1_old_gen                       17M       184M       4096M      0.43%
g1_survivor_space                4M        4M         -1         100.00%
nonheap                          35M       39M        -1         89.55%
codeheap_'non-nmethods'          1M        2M         5M         20.53%
metaspace                        26M       27M        -1         96.88%
codeheap_'profiled_nmethods'     4M        4M         117M       3.57%
compressed_class_space           2M        3M         1024M      0.29%
codeheap_'non-profiled_nmethods' 685K      2496K      120032K    0.57%
mapped                           0K        0K         -          0.00%
direct                           48M       48M        -          100.00%
```

------

# auth 命令

> 提示
>
> 验证当前会话

## 配置用户名和密码

在 attach 时，可以在命令行指定密码。比如：

```text
java -jar arthas-boot.jar --password ppp
```

- 可以通过 `--username` 选项来指定用户，默认值是`arthas`。

- 也可以在 `arthas.properties` 里中配置 username/password。命令行的优先级大于配置文件。

- 如果只配置`username`，没有配置`password`，则会生成随机密码，打印在`~/logs/arthas/arthas.log`中

  ```text
  Using generated security password: 0vUBJpRIppkKuZ7dYzYqOKtranj4unGh
  ```

## 本地连接不鉴权

默认情况下，在`arthas.properties`文件里有配置：

```text
arthas.localConnectionNonAuth=true
```

当配置密码时，使用本地连接，也不需要鉴权。默认配置值是 true，方便本地连接使用。只有远程连接时，才需要鉴权。

## 在 telnet console 里鉴权

连接到 arthas 后，直接执行命令会提示需要鉴权：

```bash
[arthas@37430]$ help
Error! command not permitted, try to use 'auth' command to authenticates.
```

使用`auth`命令来鉴权，成功之后可以执行其它命令。

```text
[arthas@37430]$ auth ppp
Authentication result: true
```

- 可以通过 `--username` 选项来指定用户，默认值是`arthas`。

## Web console 密码验证

打开浏览器，会有弹窗提示需要输入 用户名 和 密码。

成功之后，则可以直接连接上 web console。

## HTTP API 验证

### Authorization Header 方式（推荐）

Arthas 采用的是 HTTP 标准的 Basic Authorization，客户端请求时增加对应的 header 即可。

- 参考：[https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication在新窗口打开](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

例如，用户名是：`admin`，密码是 `admin`，则组合为字符串： `admin:admin`，base64 结果是： `YWRtaW46YWRtaW4=`，则 HTTP 请求增加`Authorization` header：

```bash
curl 'http://localhost:8563/api' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  --data-raw '{"action":"exec","command":"version"}'
```

### URL 参数传递方式

为了方便各种特殊情况，支持了以 parameters 方式传递 username 和 password。比如：

```bash
curl 'http://localhost:8563/api?password=admin' \
  --data-raw '{"action":"exec","command":"version"}'
```

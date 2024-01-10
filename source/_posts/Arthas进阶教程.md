---
title: Arthas进阶教程
date: 2024-01-05 09:27:15
tags: 
- Arthas
- Java诊断工具
- 线上问题排查
- JVM
- 反编译
categories: Java诊断工具
---

# 查看 JVM 信息

下面介绍 Arthas 里查看 `JVM` 信息的命令。

## sysprop

`sysprop` 可以打印所有的 System Properties 信息。

{% note info no-icon %}

提示

查看当前 JVM 的系统属性( System Property )

{% endnote %}

### 使用参考

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

## sysenv

`sysenv` 命令可以获取到环境变量。和`sysprop` 命令类似。

{% note info no-icon %}

提示

查看当前 JVM 的环境属性( System Environment Variables )

{% endnote %}

### 使用参考

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

```text
$ sysenv USER
USER=admin
```

## JVM

`jvm` 命令会打印出`JVM` 的各种详细信息。

{% note info no-icon %}

提示

查看当前 JVM 信息

{% endnote %}

### 使用参考

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

### THREAD 相关

- `COUNT`: JVM 当前活跃的线程数
- `DAEMON-COUNT`: JVM 当前活跃的守护线程数
- `PEAK-COUNT`: 从 JVM 启动开始曾经活着的最大线程数
- `STARTED-COUNT`: 从 JVM 启动开始总共启动过的线程次数
- `DEADLOCK-COUNT`: JVM 当前死锁的线程数

### 文件描述符相关

- `MAX-FILE-DESCRIPTOR-COUNT`：JVM 进程最大可以打开的文件描述符数
- `OPEN-FILE-DESCRIPTOR-COUNT`：JVM 当前打开的文件描述符数

-------

# Tips

为了更好使用 Arthas，下面先介绍 Arthas 里的一些使用技巧。

## help

Arthas 里每一个命令都有详细的帮助信息。可以用`-h` 来查看。帮助信息里有`EXAMPLES` 和`WIKI` 链接。

比如：

```sh
sysprop -h
```

## 自动补全

`Arthas` 支持丰富的自动补全功能，在使用有疑惑时，可以输入`Tab` 来获取更多信息。

比如输入 `sysprop java.` 之后，再输入`Tab` ，会补全出对应的 key。

## readline 的快捷键支持

`Arthas` 支持常见的命令行快捷键，比如`Ctrl + A` 跳转行首，`Ctrl + E` 跳转行尾。

更多的快捷键可以用 `keymap` 命令查看。

## 历史命令的补全

如果想再执行之前的命令，可以在输入一半时，按`Up/↑` 或者 `Ddown/↓` ，来匹配到之前的命令。

比如之前执行过`sysprop java.version` ，那么在输入`sysprop ja` 之后，可以输入`Up/↑` ，就会自动补全为`sysprop java.version` 。

如果想查看所有的历史命令，也可以通过 `history` 命令查看到。

## pipeline

`Arthas` 支持在 `pipeline` 之后，执行一些简单的命令，比如：

```
sysprop | grep java
sysprop | wc -l
```

# Sc/sm 查看已加载的类

下面介绍 `Arthas` 里查找已加载类的命令。

## Sc

`sc` 命令可以查找到所有 JVM 已经加载到的类。

如果搜索的是接口，还会搜索所有的实现类。比如查看所有的`Filter` 实现类：

```
sc javax.servlet.Filter
```

通过`-d` 参数，可以打印出类加载的具体信息，很方便查找类加载问题。

```
sc -d javax.servlet.Filter
```

`sc` 支持通配，比如搜索所有的`StringUtils` ：

```
sc *StringUtils
```

## sm

{% note info no-icon %}

提示

查看已加载类的方法信息

{% endnote %}

“Search-Method” 的简写，这个命令能搜索出所有已经加载了 Class 信息的方法信息。

`sm` 命令只能看到由当前类所声明 (declaring) 的方法，父类则无法看到。

### 参数说明

|              参数名称 | 参数说明                                     |
| --------------------: | :------------------------------------------- |
|       *class-pattern* | 类名表达式匹配                               |
|      *method-pattern* | 方法名表达式匹配                             |
|                   [d] | 展示每个方法的详细信息                       |
|                   [E] | 开启正则表达式匹配，默认为通配符匹配         |
|                `[c:]` | 指定 class 的 ClassLoader 的 hashcode        |
| `[classLoaderClass:]` | 指定执行表达式的 ClassLoader 的 class name   |
|                `[n:]` | 具有详细信息的匹配类的最大数量（默认为 100） |

### 使用参考

```sh
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



```sh
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

-----

# Jad

可以通过 `jad` 命令来反编译代码：

```sh
jad com.example.demo.arthas.user.UserController
```

通过`--source-only` 参数可以只打印出在反编译的源代码：

```sh
jad --source-only com.example.demo.arthas.user.UserController
```

------------

# OGNL 

## OGNL 一般语法

### 索引

- 数组和列表的索引，可以使用 `array["length"]`

- JavaBeans 索引属性

  如一个 JavaBeans 有以下四个重载方法：

  ```java
  public PropertyType[] getPropertyName();
  public void setPropertyName(PropertyType[] anArray);
  public PropertyType getPropertyName(int index);
  public void setPropertyName(int index, PropertyType value);
  ```

  则

  ```sh
  someProperty[2]
  ```

  等价于 Java 代码的

  ```sh
  getPropertyName(2)
  ```

```sh
ognl "{1,2,3,4}[0]"
```

通过上面命令可以获取到列表的第一个元素

### 变量引用

使用 `#` 在 OGNL 中定义临时变量，他们全局可见，此外表达式计算的每一步结果都保存在变量 `this` 中：

```sh
ognl "{10,20,30}[0].(#this > 5 ? #this*2 : #this+10)"
```

上面命令通过获取列表的第一个元素进行判断，如果大于 `5` 则乘以 `2` 反之则加 `10` 。

### 方法调用

```sh
method( ensureLoaded(), name )
```

注意：

- OGNL 是运行时调用，因此没有任何静态类型的信息可以参考，所以如果解析到有多个匹配的方法，则任选其中一个方法调用
- 常量 null 可以匹配所有的非原始类型的对象

```sh
ognl "{1,2,3,4}.size()"
```

通过上面命令可以调用 `ArrayList` 的 `size()` 方法获取到 ArrayList 的大小

### 复杂链式表达式

```sh
headline.parent.(ensureLoaded(), name)
```

等价于

```sh
headline.parent.ensureLoaded(), headline.parent.name
ognl "@java.lang.System@out.(print('Hello '), print('world\n'))"
```

运行上面这个命令后，你可以在 Tab1 的终端看到 `Hello world` 的输出。

### 集合操作

#### 新建列表

```
ognl "1 in {2, 3}"
```

上面这条命令判断 `1` 是否在列表 `[2, 3]` 中。

#### 新建原生数组

```
ognl "new int[] {1, 2, 3}"
```

指定长度

```
ognl "new int[9]"
```

#### 新建 Maps

新建普通 Map

```
ognl "#{ 'foo': 'foo value', 'bar': 'bar value' }"
```

新建特定类型 Map

```
ognl "#@java.util.HashMap@{ 'foo': 'foo value', 'bar': 'bar value' }"
```

#### 集合的投影

OGNL 把对针对集合上的每个元素调用同一个方法并返回新的集合的行为称之为“投影”。

```
ognl "{1, 2, 3}.{#this*2}"
```

#### 查找集合元素

- 查找所有匹配的元素
  `ognl "{1024, 'Hello world!', true, 2048}.{? #this instanceof Number}"`
- 查找第一个匹配的元素
  `ognl "{1024, 'Hello world!', true, 2048}.{^ #this instanceof Number}"`
- 查找最后一个匹配的元素
  `ognl "{1024, 'Hello world!', true, 2048}.{$ #this instanceof Number}"`

#### 集合的虚拟属性

OGNL 定义了一些特定的集合属性，含义与相应的 Java 集合方法完全等价。

- `Collections`
  - `size` 集合大小
  - `isEmpty` 集合为空时返回 `true`
- `List`
  - `iterator` 返回 List 的 iterator
- `Map`
  - `keys` 返回 Map 的所有 Key 值
  - `values` 返回 Map 的所有 Value 值
- `Set`
  - `iterator` 返回 Set 的 iterator

#### 构造函数

非 `java.lang` 包下的所有类的构造函数都要用类的权限定名称。

```
ognl "new java.util.ArrayList()"
```

#### 静态方法

```
ognl -x 3 '@java.lang.Math@sqrt(9.0D)'
```

#### 静态属性

```
ognl -x 3 '@java.io.File@separator'
```

#### 伪 lambda 表达式

```
ognl "#fact = :[#this<=1? 1 : #this*#fact(#this-1)], #fact(3)"
```

该命令实现了一个 lambda 递归实现了一个阶乘函数，并求 3 的阶乘。

#### 补充

更多详细语法请查看[官方文档](https://commons.apache.org/proper/commons-ognl/language-guide.html)。

## Arthas-OGNL

在 Arthas 里，有一个单独的 ognl 命令，可以动态执行代码。

### 调用 static 函数

```
ognl '@java.lang.System@out.println("hello ognl")'
```

### 查找 UserController 的 ClassLoader

```
sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash
```

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。
如果你使用`-c` ，你需要手动输入由上述命令获取到的 hashcode：`-c <hashcode>`
对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass` 指定 class name，使用起来更加方便：
`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>` 是动态变化的。

### 获取静态类的静态字段

获取`CRBCMsgAdapterController` 类里的`logger` 字段：

```sh
[arthas@9012]$ ognl -c 18b4aac2 @com.crb.adaptor.controller.CRBCMsgAdapterController@logger
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[com.crb.adaptor.controller.CRBCMsgAdapterController],
    level=null,
    effectiveLevelInt=@Integer[20000],
    parent=@Logger[Logger[com.crb.adaptor.controller]],
    childrenList=null,
    aai=null,
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

还可以通过`-x` 参数控制返回值的展开层数。比如：

```sh
[arthas@9012]$ ognl c 18b4aacc2 -x 2 @com.crb.adaptor.controller.CRBCMsgAdapterController@logger
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[com.crb.adaptor.controller.CRBCMsgAdapterController],
    level=null,
    effectiveLevelInt=@Integer[20000],
    parent=@Logger[
        serialVersionUID=@Long[5454405123156820674],
        FQCN=@String[ch.qos.logback.classic.Logger],
        name=@String[com.crb.adaptor.controller],
        level=null,
        effectiveLevelInt=@Integer[20000],
        parent=@Logger[Logger[com.crb.adaptor]],
        childrenList=@CopyOnWriteArrayList[isEmpty=false;size=1],
        aai=null,
        additive=@Boolean[true],
        loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
    ],
    childrenList=null,
    aai=null,
    additive=@Boolean[true],
    loggerContext=@LoggerContext[
        DEFAULT_PACKAGING_DATA=@Boolean[false],
        root=@Logger[Logger[ROOT]],
        size=@Integer[416],
        noAppenderWarning=@Integer[2],
        loggerContextListenerList=@ArrayList[isEmpty=false;size=1],
        loggerCache=@ConcurrentHashMap[isEmpty=false;size=416],
        loggerContextRemoteView=@LoggerContextVO[LoggerContextVO{name='default', propertyMap={PORT=8610, APPLICATION_NAME=core-tf-application}, birthTime=1704438833539}],
        turboFilterList=@TurboFilterList[isEmpty=true;size=0],
        packagingDataEnabled=@Boolean[false],
        maxCallerDataDepth=@Integer[8],
        resetCount=@Integer[2],
        frameworkPackages=@ArrayList[isEmpty=true;size=0],
        birthTime=@Long[1704438833539],
        name=@String[default],
        sm=@BasicStatusManager[ch.qos.logback.core.BasicStatusManager@2eb37a8d],
        propertyMap=@HashMap[isEmpty=false;size=2],
        objectMap=@HashMap[isEmpty=false;size=7],
        configurationLock=@LogbackLock[ch.qos.logback.core.spi.LogbackLock@1db45c81],
        scheduledExecutorService=@ScheduledThreadPoolExecutor[java.util.concurrent.ScheduledThreadPoolExecutor@ac21a5d[Running, pool size = 8, active threads = 0, queued tasks = 1, completed tasks = 28]],
        scheduledFutures=@ArrayList[isEmpty=false;size=1],
        lifeCycleManager=@LifeCycleManager[ch.qos.logback.core.LifeCycleManager@74197f30],
        started=@Boolean[false],
    ],
]
```

### 执行多行表达式，赋值给临时变量，返回一个 List

```sh
$ ognl '#value1=@System@getProperty("java.home"), #value2=@System@getProperty("java.runtime.name"), {#value1, #value2}'
@ArrayList[
    @String[/opt/java/8.0.181-zulu/jre],
    @String[OpenJDK Runtime Environment],
]
```

### 更多

在 Arthas 里`ognl` 表达式是很重要的功能，在很多命令里都可以使用`ognl` 表达式。

一些更复杂的用法，可以参考：

- OGNL 特殊用法请参考：https://github.com/alibaba/arthas/issues/71
- OGNL 表达式官方指南：https://commons.apache.org/proper/commons-ognl/language-guide.html

---------

# Options

在 Arthas 里有一些开关，可以通过 `options` 命令来查看 。

## 查看所有的 options

```sh
$ options
 LEVEL  TYPE    NAME          VALUE   SUMMARY               DESCRIPTION
-------------------------------------------------------------------------------------------------------
 0      boolea  unsafe        false   Option to support sy  This option enables to proxy functionality
        n                             stem-level class       of JVM classes. Due to serious security r
                                                            isk a JVM crash is possibly be introduced.
                                                             Do not activate it unless you are able to
                                                             manage.
 1      boolea  dump          false   Option to dump the e  This option enables the enhanced classes t
        n                             nhanced classes       o be dumped to external file for further d
                                                            e-compilation and analysis.
 1      boolea  batch-re-tra  true    Option to support ba  This options enables to reTransform classe
        n       nsform                tch reTransform Clas  s with batch mode.
                                      s
 2      boolea  json-format   false   Option to support JS  This option enables to format object outpu
        n                             ON format of object   t with JSON when -x option selected.
                                      output
 1      boolea  disable-sub-  false   Option to control in  This option disable to include sub class w
        n       class                 clude sub class when  hen matching class.
                                       class matching
 1      boolea  support-defa  true    Option to control in  This option disable to include default met
        n       ult-method            clude default method  hod in interface when matching class.
                                       in interface when c
                                      lass matching
 1      boolea  save-result   false   Option to print comm  This option enables to save each command's
        n                             and's result to log    result to log file, which path is ${user.
                                      file                  home}/logs/arthas-cache/result.log.
 2      String  job-timeout   1d      Option to job timeou  This option setting job timeout,The unit c
                                      t                     an be d, h, m, s for day, hour, minute, se
                                                            cond. 1d is one day in default
 1      boolea  print-parent  true    Option to print all   This option enables print files in parent
        n       -fields               fileds in parent cla  class, default value true.
                                      ss
 1      boolea  verbose       false   Option to print verb  This option enables print verbose informat
        n                             ose information       ion, default value false.
 1      boolea  strict        true    Option to strict mod  By default, strict mode is true, not allow
        n                             e                     ed to set object properties. Want to set o
                                                            bject properties, execute `options strict
                                                            false`
```

## 参数说明

| 名称                   | 默认值 | 描述                                                         |
| ---------------------- | ------ | ------------------------------------------------------------ |
| unsafe                 | false  | 是否支持对系统级别的类进行增强，打开该开关可能导致把 JVM 搞挂，请慎重选择！ |
| dump                   | false  | 是否支持被增强了的类 dump 到外部文件中，如果打开开关，class 文件会被 dump 到`/${application working dir}/arthas-class-dump/`目录下，具体位置详见控制台输出 |
| batch-re-transform     | true   | 是否支持批量对匹配到的类执行 retransform 操作                |
| json-format            | false  | 是否支持 json 化的输出                                       |
| disable-sub-class      | false  | 是否禁用子类匹配，默认在匹配目标类的时候会默认匹配到其子类，如果想精确匹配，可以关闭此开关 |
| support-default-method | true   | 是否支持匹配到 default method， 默认会查找 interface，匹配里面的 default method。参考 [#1105在新窗口打开](https://github.com/alibaba/arthas/issues/1105) |
| save-result            | false  | 是否打开执行结果存日志功能，打开之后所有命令的运行结果都将保存到`~/logs/arthas-cache/result.log`中 |
| job-timeout            | 1d     | 异步后台任务的默认超时时间，超过这个时间，任务自动停止；比如设置 1d, 2h, 3m, 25s，分别代表天、小时、分、秒 |
| print-parent-fields    | true   | 是否打印在 parent class 里的 filed                           |
| verbose                | false  | 是否打印更多详细信息                                         |
| strict                 | true   | 是否启用 strict 模式                                         |

## 获取单个option 的值

```text
$ options json-format
 LEVEL  TYPE  NAME         VALUE  SUMMARY             DESCRIPTION
--------------------------------------------------------------------------------------------
 2      bool  json-format  false  Option to support   This option enables to format object
        ean                       JSON format of obj  output with JSON when -x option selec
                                  ect output          ted.

```

{% note info no-icon %}

提示

默认情况下 json-format 为 false，如果希望 watch / tt 等命令结果以 json 格式输出，则可以设置 json-format 为 true。

{% endnote %}

## 设置指定的 option

例如，想打开执行结果存日志功能，输入如下命令即可：

```text
$ options save-result true
 NAME         BEFORE-VALUE  AFTER-VALUE
----------------------------------------
 save-result  false         true
```

## 允许增强 JDK 的类

默认情况下`unsafe` 为 false，即 watch/trace 等命令不会增强 JVM 的类，即`java.*` 下面的类。

如果想增强 JVM 里的类，可以执行 `options unsafe true` ，设置`unsafe` 为 true。

## 以 JSON 格式打印对象

`options json-format` 可以看到当前的 `json-format` 为 false
运行 `ognl '#value1=@System@getProperty("java.home"), #value2=@System@getProperty("java.runtime.name"), {#value1, #value2}'` 得到的结果并不是 JSON 格式

如果希望输出 JSON 格式，可以使用 `options json-format true` 开启，开启后再运行 `ognl '#value1=@System@getProperty("java.home"), #value2=@System@getProperty("java.runtime.name"), {#value1, #value2}'` 这是可以看到输出的格式已经转变为 JSON 格式。

----------------

# 案例：排查函数调用异常

## 现象

目前，访问 `/user/0` ，会返回 500 异常：

但请求的具体参数，异常栈是什么呢？

## 查看 UserController 的 参数/异常

在 Arthas 里执行：

```
watch com.example.demo.arthas.user.UserController * '{params, throwExp}'
```

1. 第一个参数是类名，支持通配
2. 第二个参数是函数名，支持通配

访问 `/user/0`,`watch` 命令会打印调用的参数和异常

```
[arthas@5728]$ watch  com.example.demo.arthas.user.UserController  * '{params, throwExp}'
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 2) cost in 172 ms, listenerId: 1
method=com.example.demo.arthas.user.UserController.findUserById location=AtExceptionExit
ts=2024-01-05 16:31:06; [cost=6.6047ms] result=@ArrayList[
    @Object[][isEmpty=false;size=1],
    @IllegalArgumentException[java.lang.IllegalArgumentException: id < 1],
]
```

可以看到实际抛出的异常是`IllegalArgumentException` 。

可以输入 `Q` 或者 `Ctrl+C` 退出 watch 命令。

如果想把获取到的结果展开，可以用`-x` 参数：

```
[arthas@6392]$ watch  com.example.demo.arthas.user.UserController  * '{params, throwExp}' -x 2
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 2) cost in 119 ms, listenerId: 1
method=com.example.demo.arthas.user.UserController.findUserById location=AtExceptionExit
ts=2024-01-05 16:38:32; [cost=6.8937ms] result=@ArrayList[
    @Object[][
        @Integer[0],
    ],
    java.lang.IllegalArgumentException: id < 1
        at com.example.demo.arthas.user.UserController.findUserById(UserController.java:19)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
        at java.lang.reflect.Method.invoke(Unknown Source)
        at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
        at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:133)
        at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:97)
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:827)
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:738)
        at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:85)
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:967)
        at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:901)
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:970)
        at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:861)
        at javax.servlet.http.HttpServlet.service(HttpServlet.java:635)
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
        at javax.servlet.http.HttpServlet.service(HttpServlet.java:742)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        ......
 ]
```

## 返回值表达式

在上面的例子里，第三个参数是`返回值表达式` ，它实际上是一个`ognl` 表达式，它支持一些内置对象：

- `loader`
- `clazz`
- `method`
- `target`
- `params`
- `returnObj`
- `throwExp`
- `isBefore`
- `isThrow`
- `isReturn`

你可以利用这些内置对象来组成不同的表达式。比如返回一个数组：

```
watch com.example.demo.arthas.user.UserController * '{params[0], target, returnObj}'
```

更多参考：https://arthas.aliyun.com/doc/advice-class.html

## 条件表达式

`watch` 命令支持在第 4 个参数里写条件表达式，比如：

```
watch com.example.demo.arthas.user.UserController * returnObj 'params[0] > 100'
```

当访问 `/user/1` 时，`watch` 命令没有输出

当访问 `/user/101` 时，`watch` 会打印出结果。

```
[arthas@6392]$ watch com.example.demo.arthas.user.UserController  * returnObj 'params[0]>100'
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 2) cost in 31 ms, listenerId: 2
method=com.example.demo.arthas.user.UserController.findUserById location=AtExit
ts=2024-01-05 16:46:59; [cost=0.5729ms] result=@User[
    id=@Integer[101],
    name=@String[name101],
]
```

## 当异常时捕获

`watch` 命令支持`-e` 选项，表示只捕获抛出异常时的请求：

```
watch com.example.demo.arthas.user.UserController * "{params[0],throwExp}" -e
```

当访问`/user/0`时，`watch`会打印异常信息。

```
[arthas@6392]$ watch com.example.demo.arthas.user.UserController * "{params[0],throwExp}" -e
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 2) cost in 38 ms, listenerId: 6
method=com.example.demo.arthas.user.UserController.findUserById location=AtExceptionExit
ts=2024-01-05 16:57:40; [cost=1.197ms] result=@ArrayList[
    @Integer[0],
    @IllegalArgumentException[java.lang.IllegalArgumentException: id < 1],
]
```

## 按照耗时进行过滤

watch 命令支持按请求耗时进行过滤，比如：

```
watch com.example.demo.arthas.user.UserController * '{params, returnObj}' '#cost>200'
```

-----------

# 案例：热更新代码

下面介绍通过`jad` /`mc` /`redefine` 命令实现动态更新代码的功能。

目前，访问 `/user/0` ，会返回 500 异常：

下面通过热更新代码，修改这个逻辑。

## jad 反编译 UserController

```
jad --source-only com.example.demo.arthas.user.UserController > /tmp/UserController.java
```

jad 反编译的结果保存在 `/tmp/UserController.java` 文件里了。

再打开一个终端于 `Tab 3` ，然后在 `Tab3` 里用 `sed` 来编辑`/tmp/UserController.java` ：

```
sed -i 's/throw new IllegalArgumentException("id < 1")/return new User(id, "name" + id)/g' /tmp/UserController.java
```

使用 `cat` 命令查看修改后的内容：

```
cat /tmp/UserController.java
```

比如当 user id 小于 1 时，也正常返回，不抛出异常：

```java
    @GetMapping(value={"/user/{id}"})
    public User findUserById(@PathVariable Integer id) {
        logger.info("id: {}", (Object)id);
        if (id != null && id < 1) {
			return new User(id, "name" + id);
            // throw new IllegalArgumentException("id < 1");
        }
        return new User(id.intValue(), "name" + id);
    }
```

`mc` (Memory Compiler) 命令来编译加载 UserController， 可以通过 -c 指定 classLoaderHash 或者 --classLoaderClass 参数指定 ClassLoader，这里为了操作连贯性使用 classLoaderClass。

## 查询 UserController 类加载器

### sc 查找加载 UserController 的 ClassLoader

回到 `Tab 2` 里运行 `sc -d *UserController | grep classLoaderHash`

```
[arthas@6392]$ sc -d *UserController | grep classLoaderHash
 classLoaderHash   38af3868
```

### classloader 查询类加载器名称

`classloader -l` 查询所有的类加载器列表

```
[arthas@6392]$ classloader  -l
 name                                                             loadedCount  hash      parent
 BootstrapClassLoader                                             3221         null      null
 com.taobao.arthas.agent.ArthasClassloader@384abb78               2630         384abb78  sun.misc.Launcher$ExtClassLoade
                                                                                         r@a09ee92
 org.springframework.boot.loader.LaunchedURLClassLoader@38af3868  4591         38af3868  sun.misc.Launcher$AppClassLoade
                                                                                         r@5c647e05
 sun.misc.Launcher$AppClassLoader@5c647e05                        45           5c647e05  sun.misc.Launcher$ExtClassLoade
                                                                                         r@a09ee92
 sun.misc.Launcher$ExtClassLoader@a09ee92                         19           a09ee92   null
Affect(row-cnt:5) cost in 7 ms.
```

`UserController classLoaderHash` 值对应的类加载器为 `org.springframework.boot.loader.LaunchedURLClassLoader`

### mc 编译加载 UserController

保存到 `/tmp/UserController.java` 之后可以使用 mc (Memory Compiler) 命令来编译

### mc 指定 classloader 编译 UserController

```
[arthas@3880]$ mc -c 33c7353a /tmp/UserController.java  -d /tmp/
Memory compiler output:
D:\tmp\com\example\demo\arthas\user\UserController.class
Affect(row-cnt:1) cost in 4442 ms.
```

### redefine

再使用`redefine` 命令重新加载新编译好的`UserController.class` ：

```
[arthas@3880]$ redefine /tmp/com/example/demo/arthas/user/UserController.class
redefine success, size: 1, classes:
com.example.demo.arthas.user.UserController
```

### 热修改代码结果

`redefine` 成功之后，再次访问 `/user/0`，结果是：

```json
{
  "id": 0,
  "name": "name0"
}
```

----------

# 案例：Arthas 后台异步任务

arthas 中的后台异步任务，使用了仿 linux 系统任务相关的命令。

## 使用 & 在后台执行任务，任务输出重定向

可通过 `>` 或者 `>>` 将任务输出结果输出到指定的文件中，可以和 `&` 一起使用，实现 arthas 命令的后台异步任务。

当前我们需要排查一个问题，但是这个问题的出现时间不能确定，那我们就可以把检测命令挂在后台运行，并将保存到输出日志，如下命令：

```
watch com.example.demo.arthas.user.UserController * '{params, throwExp}' 'throwExp != null' >> a.log &
```

这时命令在后台执行，可以在 console 中继续执行其他命令。

之后我们去访问：`/user/0`

然后使用 `cat a.log` 可以看到我们刚刚访问的 URL 抛出了一个异常

## 通过 jobs 查看任务

如果希望查看当前有哪些 arthas 任务在执行，可以执行 jobs 命令，执行结果如下

```
[arthas@3880]$ jobs
[4]*
       Running           watch com.example.demo.arthas.user.UserController * '{params, throwExp != null}' >> a.log &
       execution count : 1
       start time      : Sat Jan 06 09:36:28 CST 2024
       timeout date    : Sun Jan 07 09:36:28 CST 2024
       session         : 4251ad90-2951-4d33-8da1-2d241a3e0a0f (current)
```

可以看到目前有一个后台任务在执行

job id 是 4, `*` 表示此 job 是当前 session 创建（生命周期默认为一天）

状态是 `Running`

`execution count` 是执行次数，从启动开始已经执行了 1 次

`timeout date` 是超时的时间，到这个时间，任务将会自动超时退出。

## 停止命令、切换前后台

异步执行的命令，如果希望停止，可执行 `kill`, 希望命令转到前台、后台继续执行 fg、bg 命令。

`kill`掉job id 是 4 的任务：

```
[arthas@3880]$ kill 4
kill job 4 success
[arthas@3880]$ jobs
```

## 注意事项

- 最多同时支持 8 个命令使用重定向将结果写日志
- 请勿同时开启过多的后台异步命令，以免对目标 JVM 性能造成影响
- 如果不想停止 arthas，继续执行后台任务，可以执行 `quit` 退出 arthas 控制台（`stop` 会停止 arthas 服务）

-------

# 案例：动态更新应用 Logger Level

在这个案例里，动态修改应用的 Logger Level。

## 使用 sc 查找 UserController 的 ClassLoader

```
sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash
```

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。
如果你使用`-c` ，你需要手动输入 hashcode：`-c <hashcode>`
对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass` 指定 class name，使用起来更加方便：
`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>` 是动态变化的。

## 使用 ognl

### 用 ognl 获取 logger

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@com.example.demo.arthas.user.UserController@logger'
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[com.example.demo.arthas.user.UserController],
    level=null,
    effectiveLevelInt=@Integer[20000],
    parent=@Logger[Logger[com.example.demo.arthas.user]],
    childrenList=null,
    aai=null,
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

可以知道 `UserController@logger` 实际使用的是 logback。可以看到 `level=null` ，则说明实际最终的 level 是从 `root logger `里来的。

查看`root logger `的`Lever`:

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@org.slf4j.LoggerFactory@getLogger("root")'
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[ROOT],
    level=@Level[INFO],
    effectiveLevelInt=@Integer[20000],
    parent=null,
    childrenList=@CopyOnWriteArrayList[isEmpty=false;size=3],
    aai=@AppenderAttachableImpl[ch.qos.logback.core.spi.AppenderAttachableImpl@452a4e77],
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

### 单独设置 UserController 的 logger level

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@com.example.demo.arthas.user.UserController@logger.setLevel(@ch.qos.logback.classic.Level@DEBUG)'
null
```

再次获取 `UserController@logger` ，可以发现已经是 `DEBUG` 了：

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@com.example.demo.arthas.user.UserController@logger'
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[com.example.demo.arthas.user.UserController],
    level=@Level[DEBUG],
    effectiveLevelInt=@Integer[10000],
    parent=@Logger[Logger[com.example.demo.arthas.user]],
    childrenList=null,
    aai=null,
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

### 修改 logback 的全局 logger level

通过获取 `root` logger，可以修改全局的 logger level：

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@org.slf4j.LoggerFactory@getLogger("root").setLevel(@ch.qos.logback.classic.Level@DEBUG)'
null
```

运行下面命令可以看到 `ROOT` 的 level 已经修改为了 `DEBUG`

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@org.slf4j.LoggerFactory@getLogger("root")'
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[ROOT],
    level=@Level[DEBUG],
    effectiveLevelInt=@Integer[10000],
    parent=null,
    childrenList=@CopyOnWriteArrayList[isEmpty=false;size=3],
    aai=@AppenderAttachableImpl[ch.qos.logback.core.spi.AppenderAttachableImpl@452a4e77],
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

## 使用 logger

使用 `logger` 命令可以相较于 `ognl` 更加便捷的实现 logger level 的动态设置。

### 使用 logger 命令获取对应 logger 信息

```
[arthas@3880]$ logger --name  com.example.demo.arthas.user.UserController
 name                                      com.example.demo.arthas.user.UserController
 class                                     ch.qos.logback.classic.Logger
 classLoader                               org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a
 classLoaderHash                           33c7353a
 level                                     DEBUG
 effectiveLevel                            DEBUG
 additivity                                true
 codeSource                                jar:file:/D:/arthas/arthas-packaging-3.7.1-bin/demo-arthas-spring-boot.jar!/BOOT-INF/lib/logback-classic-1.1.11.jar!/
```

### 单独设置 UserController 的 logger level

```
[arthas@3880]$ logger --name com.example.demo.arthas.user.UserController --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader --level WARN
Update logger level success.
```

再次获取对应 `logger` 信息，可以发现已经是 `WARN` 了：

```
[arthas@3880]$ logger --name com.example.demo.arthas.user.UserController
 name                                      com.example.demo.arthas.user.UserController
 class                                     ch.qos.logback.classic.Logger
 classLoader                               org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a
 classLoaderHash                           33c7353a
 level                                     WARN
 effectiveLevel                            WARN
 additivity                                true
 codeSource                                jar:file:/D:/arthas/arthas-packaging-3.7.1-bin/demo-arthas-spring-boot.jar!/BOOT-INF/lib/logback-classic-1.1.11.jar!/
```

### 修改 logback 的全局 logger level

```
[arthas@3880]$ logger --name ROOT --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader --level ERROR
Update logger level success.
```

运行下面命令可以看到 `ROOT` 的 level 已经修改为了 `ERROR`

```
[arthas@3880]$ logger  --name ROOT --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader
 name                                      ROOT
 class                                     ch.qos.logback.classic.Logger
 classLoader                               org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a
 classLoaderHash                           33c7353a
 level                                     ERROR
 effectiveLevel                            ERROR
 additivity                                true
 codeSource                                jar:file:/D:/arthas/arthas-packaging-3.7.1-bin/demo-arthas-spring-boot.jar!/BOOT-INF/lib/logback-classic-1.1.11.jar!/
 appenders                                 name            CONSOLE
                                           class           ch.qos.logback.core.ConsoleAppender
                                           classLoader     org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a
                                           classLoaderHash 33c7353a
                                           target          System.out
```

-----------

# 案例：排查 logger 冲突问题

在这个案例里，展示排查 logger 冲突的方法。

## 查找 UserController 的 ClassLoader

```
sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash
```

注意 hashcode 是变化的，需要先查看当前的 ClassLoader 信息，提取对应 ClassLoader 的 hashcode。
如果你使用`-c` ，你需要手动输入 hashcode：`-c <hashcode>`
对于只有唯一实例的 ClassLoader 可以通过`--classLoaderClass` 指定 class name，使用起来更加方便：
`--classLoaderClass` 的值是 ClassLoader 的类名，只有匹配到唯一的 ClassLoader 实例时才能工作，目的是方便输入通用命令，而`-c <hashcode>` 是动态变化的。

## 确认应用使用的 logger 系统

以 `UserController` 为例，它使用的是 slf4j api，但实际使用到的 logger 系统是 logback。

```
[arthas@3880]$ ognl --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader '@com.example.demo.arthas.user.UserController@logger'
@Logger[
    serialVersionUID=@Long[5454405123156820674],
    FQCN=@String[ch.qos.logback.classic.Logger],
    name=@String[com.example.demo.arthas.user.UserController],
    level=@Level[INFO],
    effectiveLevelInt=@Integer[20000],
    parent=@Logger[Logger[com.example.demo.arthas.user]],
    childrenList=null,
    aai=null,
    additive=@Boolean[true],
    loggerContext=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
]
```

## 获取 logback 实际加载的配置文件

```
[arthas@3880]$ ognl --classLoaderClass  org.springframework.boot.loader.LaunchedURLClassLoader  '#map1=@org.slf4j.LoggerFactory@getLogger("root").loggerContext.objectMap, #map1.get("CONFIGURATION_WATCH_LIST")'
@ConfigurationWatchList[
    mainURL=@URL[jar:file:/D:/arthas/arthas-packaging-3.7.1-bin/demo-arthas-spring-boot.jar!/BOOT-INF/classes!/logback-spring.xml],
    fileWatchList=@ArrayList[isEmpty=true;size=0],
    lastModifiedList=@ArrayList[isEmpty=true;size=0],
    noContextWarning=@Integer[0],
    context=@LoggerContext[ch.qos.logback.classic.LoggerContext[default]],
    declaredOrigin=@ConfigurationWatchList[ch.qos.logback.core.joran.spi.ConfigurationWatchList@5149876],
]
```

## 使用classloader 命令查找可能存在的 logger 配置文件

```
[arthas@3880]$ classloader --classLoaderClass  org.springframework.boot.loader.LaunchedURLClassLoader -r logback-spring.xml
 jar:file:/D:/arthas/arthas-packaging-3.7.1-bin/demo-arthas-spring-boot.jar!/BOOT-INF/classes!/logback-spring.xml

Affect(row-cnt:1) cost in 1 ms.
```

可以知道加载的配置的具体来源。

可以尝试加载容易冲突的文件：

```
classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r logback.xml
classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r log4j.properties
```

# 案例：获取 Spring Context

在这个案例里，展示获取 spring context，再获取 bean，然后调用函数。

## 使用 tt 命令获取到 spring context

[tt](https://arthas.aliyun.com/doc/tt.html) 即 TimeTunnel，它可以记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测。

```
tt -t org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter invokeHandlerMethod
```

访问：`/user/1`

可以看到`tt` 命令捕获到了请求

```
[arthas@3880]$ tt -t  org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter invokeHandlerMethod
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 1) cost in 50 ms, listenerId: 2
 INDEX           TIMESTAMP                              COST(ms)           IS-RET          IS-EXP         OBJECT                        CLASS                                                     METHOD
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 1000            2024-01-06 15:47:59                    4.1345             true            false          0x369e7ab5                    RequestMappingHandlerAdapter                              invokeHandlerMethod
```

## 使用 tt 命令从调用记录里获取到 spring context

输入 `Q` 或者 `Ctrl + C` 退出上面的 `tt -t` 命令。
使用 `tt -l` 命令可以查看之前 `tt` 命令捕获到的请求信息。

```
tt -i 1000 -w 'target.getApplicationContext()'
```

## 获取 spring bean，并调用函数

```
[arthas@3880]$ tt -i 1000 -w 'target.getApplicationContext().getBean("helloWorldService").getHelloMessage()'
@String[Hello World]
Affect(row-cnt:1) cost in 4 ms.
```

## Vmtool 获取 HelloWorldService 对象实例，并调用函数

上面的方法使用 tt 命令获取 spring bean，并调用函数，有点繁琐，更换为使用 [vmtool](https://arthas.aliyun.com/doc/vmtool.html) 将会极大的简化这个流程，命令如下：

```
[arthas@3880]$ vmtool --action getInstances --className com.example.demo.arthas.aop.HelloWorldService --express 'instances[0].getHelloMessage()'
@String[Hello World]
```

-----------

# 案例：排查 HTTP 请求返回 401

在这个案例里，展示排查 HTTP 401 问题的技巧。

访问： `/admin`

结果是：

```yaml
Something went wrong: 401 Unauthorized
```

我们知道`401` 通常是被权限管理的`Filter` 拦截了，那么到底是哪个`Filter` 处理了这个请求，返回了 401？

## 跟踪所有的 Filter 函数

开始 trace：

```
trace javax.servlet.Filter *
```

访问： `/admin`

可以在调用树的最深层，找到`AdminFilterConfig$AdminFilter` 返回了`401` ：

```javascript
+---[3.806273ms] javax.servlet.FilterChain:doFilter()
|   `---[3.447472ms] com.example.demo.arthas.AdminFilterConfig$AdminFilter:doFilter()
|       `---[0.17259ms] javax.servlet.http.HttpServletResponse:sendError()
```

输入 `Q` 或者 `Ctrl+C` 退出 watch 命令。

### 通过 stack 获取调用栈

上面是通过`trace` 命令来获取信息，从结果里，我们可以知道通过`stack` 跟踪`HttpServletResponse:sendError()` ，同样可以知道是哪个`Filter` 返回了`401`

执行：

```
stack javax.servlet.http.HttpServletResponse sendError 'params[0]==401'
```

访问： `/admin`

```
[arthas@3880]$ stack  javax.servlet.http.HttpServletResponse sendError 'params[0]==401'
Press Q or Ctrl+C to abort.
Affect(class count: 3 , method count: 4) cost in 99 ms, listenerId: 4
ts=2024-01-06 16:33:24;thread_name=http-nio-80-exec-3;id=14;is_daemon=true;priority=5;TCCL=org.springframework.boot.context.embedded.tomcat.TomcatEmbeddedWebappClassLoader@54c14576
    @org.apache.catalina.connector.Response.sendError()
        at org.apache.catalina.connector.ResponseFacade.sendError(ResponseFacade.java:462)
        at com.example.demo.arthas.AdminFilterConfig$AdminFilter.doFilter(AdminFilterConfig.java:38)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99)
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.springframework.web.filter.HttpPutFormContentFilter.doFilterInternal(HttpPutFormContentFilter.java:109)
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:81)
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:197)
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:198)
        at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
        at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:496)
        at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140)
        at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
        at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87)
        at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342)
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:803)
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:790)
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1468)
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
        at java.lang.Thread.run(Thread.java:748)
```

输入 `Q` 或者 `Ctrl+C` 退出 watch 命令。

# 案例：理解 Spring Boot 应用的 ClassLoader 结构

下面介绍`classloader` 命令的功能。

先访问一个 jsp 网页，触发 jsp 的加载： `访问 hello 页面`

## 列出所有 ClassLoader

```
[arthas@3880]$ classloader -l
 name                                                             loadedCount  hash      parent
 BootstrapClassLoader                                             3132         null      null
 com.taobao.arthas.agent.ArthasClassloader@a3df247                1810         a3df247   sun.misc.Launcher$ExtClassLoader@f6f4d33
 java.net.FactoryURLClassLoader@7b8e788f                          842          7b8e788f  sun.misc.Launcher$AppClassLoader@3d4eac69
 org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a  4759         33c7353a  sun.misc.Launcher$AppClassLoader@3d4eac69
 sun.misc.Launcher$AppClassLoader@3d4eac69                        45           3d4eac69  sun.misc.Launcher$ExtClassLoader@f6f4d33
 sun.misc.Launcher$ExtClassLoader@f6f4d33                         23           f6f4d33   null
Affect(row-cnt:6) cost in 4 ms.
```

## 统计 ClassLoader 实际使用 URL 和未使用的 URL

```
classloader --url-stat
```

{% note info no-icon %}

注意：基于 JVM 目前已加载的所有类统计，不代表 Unused URLs 可以从应用中删掉。因为可能将来需要从 Unused URLs 里加载类，或者需要加载 resources

{% endnote %}

## 列出 ClassLoader 里加载的所有类

列出上面的`org.apache.jasper.servlet.JasperLoader` 加载的类：

```
[arthas@3880]$ classloader -a --classLoaderClass  org.apache.jasper.servlet.JasperLoader | grep hello
 org.apache.jsp.jsp.hello_jsp
```

## 查看类的 classloader 层次

```
[arthas@3880]$ sc -d org.apache.jsp.jsp.hello_jsp
 class-info        org.apache.jsp.jsp.hello_jsp
 code-source       /C:/Users/pt-tianzhencai/AppData/Local/Temp/tomcat.2859845778385490372.80/work/Tomcat/localhost/ROOT/
 name              org.apache.jsp.jsp.hello_jsp
 isInterface       false
 isAnnotation      false
 isEnum            false
 isAnonymousClass  false
 isArray           false
 isLocalClass      false
 isMemberClass     false
 isPrimitive       false
 isSynthetic       false
 simple-name       hello_jsp
 modifier          final,public
 annotation
 interfaces        org.apache.jasper.runtime.JspSourceDependent,org.apache.jasper.runtime.JspSourceImports
 super-class       +-org.apache.jasper.runtime.HttpJspBase
                     +-javax.servlet.http.HttpServlet
                       +-javax.servlet.GenericServlet
                         +-java.lang.Object
 class-loader      +-org.apache.jasper.servlet.JasperLoader@4bc4fe20





                       +-org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a
                         +-sun.misc.Launcher$AppClassLoader@3d4eac69
                           +-sun.misc.Launcher$ExtClassLoader@f6f4d33
 classLoaderHash   4bc4fe20

Affect(row-cnt:1) cost in 7 ms.
```

## 查看 ClassLoader 树

```
[arthas@3880]$ classloader -t
+-BootstrapClassLoader
+-sun.misc.Launcher$ExtClassLoader@f6f4d33
  +-com.taobao.arthas.agent.ArthasClassloader@a3df247
  +-sun.misc.Launcher$AppClassLoader@3d4eac69
    +-java.net.FactoryURLClassLoader@7b8e788f
    +-org.springframework.boot.loader.LaunchedURLClassLoader@33c7353a





        +-org.apache.jasper.servlet.JasperLoader@4bc4fe20
Affect(row-cnt:8) cost in 4 ms.
```

## 查看 URLClassLoader 实际的 urls

比如上面查看到的 spring LaunchedURLClassLoader 为 `org.springframework.boot.loader.LaunchedURLClassLoader` ，可以通过 `-c <hashcode>` 参数来指定 classloader，还有一种方法可以通过使用 `--classLoaderClass` 指定类名，从而查看 URLClassLoader 实际的 urls：

```
classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader
```

## 查找 ClassLoader 里的资源文件

查找指定的资源文件： `classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r logback-spring.xml`

```
[arthas@3880]$ classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r java/lang/String.class
 jar:file:/D:/developInstall/JDK/jdk1.8.0_161/jre/lib/rt.jar!/java/lang/String.class

Affect(row-cnt:1) cost in 1 ms.
```

也可以尝试查找类的 class 文件：

```
[arthas@3880]$ classloader --classLoaderClass org.springframework.boot.loader.LaunchedURLClassLoader -r java/lang/String.class
 jar:file:/D:/developInstall/JDK/jdk1.8.0_161/jre/lib/rt.jar!/java/lang/String.class

Affect(row-cnt:1) cost in 1 ms.
```

# 案例：查找 Top N 线程

## 查看所有线程信息

```
thread
```

## 查看具体线程的栈

查看线程 ID 16 的栈：

```
thread 16
```

## 查看 CPU 使用率 top n 线程的栈

参数`n` 用来指定最忙的前 N 个线程并打印堆栈

```
thread -n 3
```

参数`i` 用来指定 cpu 占比统计的采样间隔，单位为毫秒

查看 5 秒内的 CPU 使用率 top n 线程栈

```
thread -n 3 -i 5000
```

## 查找线程是否有阻塞

参数`b` 用来指定找出当前阻塞其他线程的线程

```
thread -b
```

# 案例：获取 Spring 应用运行时配置值

众所周之，Spring 应用的配置注入方式非常多。除了我们熟悉的方式，比如

- System Properties/System Env
- application.properties/application.yaml
- spring profiles
- spring cloud config

还有很多配置注入的方式，令人眼花缭乱。

## 获取运行时具体配置

对于开发人员来说，在运行时怎样确定某个配置是否生效？它的具体值是什么？

用Arthas可以一行命令获取。

比如获取`server.port`的具体值：

```bash
$ vmtool --action getInstances --className org.springframework.context.ConfigurableApplicationContext --express 'instances[0].getEnvironment().getProperty("server.port")'
@String[8810]
```

## 获取具体的配置来源

但是怎样具体是从哪个配置源来的？

1.  对于 spring boot应用，可以打开一个新的 terminal 窗口，执行 `telnet 127.0.0.1 3658`连接上Arthas。

2. 执行下面的 watch 命令

   ```
   org.springframework.boot.context.properties.source.ConfigurationPropertySourcesPropertySource findConfigurationProperty
   ```

   

3.  在原来窗口用上面的`vmtool`命令来获取`server.port`的值

可以看到 watch 返回结果里有具体的配置来源`application.yml`：

```bash
$ watch org.springframework.boot.context.properties.source.ConfigurationPropertySourcesPropertySource findConfigurationProperty
Press Q or Ctrl+C to abort.
Affect(class count: 1 , method count: 2) cost in 217 ms, listenerId: 5
method=org.springframework.boot.context.properties.source.ConfigurationPropertySourcesPropertySource.findConfigurationProperty location=AtExit
ts=2023-11-27 16:08:07.696; [cost=0.327042ms] result=@ArrayList[
    @Object[][isEmpty=false;size=1],
    @ConfigurationPropertySourcesPropertySource[ConfigurationPropertySourcesPropertySource {name='configurationProperties'}],
    @ConfigurationProperty[[ConfigurationProperty@18fb3ec9 name = server.port, value = 7001, origin = class path resource [application.yml] - 2:9]],
```

# Exit/Stop

## reset

Arthas 在 watch/trace 等命令时，实际上是修改了应用的字节码，插入增强的代码。显式执行 `reset` 命令，可以清除掉这些增强代码。

## 退出 Arthas

用 `exit` 或者 `quit` 命令可以退出 Arthas。

退出 Arthas 之后，还可以再次用 `java -jar arthas-boot.jar` 来连接。

## 彻底退出 Arthas

`exit/quit` 命令只是退出当前 session，arthas server 还在目标进程中运行。

想完全退出 Arthas，可以执行 `stop` 命令。

# Arthas-boot 支持的参数

`arthas-boot.jar` 支持很多参数，可以执行 `java -jar arthas-boot.jar -h` 来查看。

## 允许外部访问

默认情况下，arthas server 侦听的是 `127.0.0.1` 这个 IP，如果希望远程可以访问，可以使用`--target-ip` 的参数。

```
java -jar arthas-boot.jar --target-ip
```

## 列出所有的版本

```
java -jar arthas-boot.jar --versions
```

使用指定版本：

```
java -jar arthas-boot.jar --use-version 3.1.0
```

## 只侦听 Telnet 端口，不侦听 HTTP 端口

```
java -jar arthas-boot.jar --telnet-port 9999 --http-port -1
```

## 打印运行的详情

```
java -jar arthas-boot.jar -v
```

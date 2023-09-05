---
title: Java日志系统Logback+SLf4J
date: 2023-08-19 09:54:54
tags:
- 日志
- SpringBoot
- Logback
- SLF4J
categories: SpringBoot
---

# Java中常用日志框架

## 日志框架的演化

> - 1996 年初，欧洲安全电子市场 (EU SEMPER) 项目组决定编写它自己的程序跟踪 API(Tracing API)。经过不断的完善，这个 API 终于成为一个十分受欢迎的 Java 日志软件包，即 Log4j。后来 Log4j 成为 Apache 基金会项目中的一员。
> - Log4j 发布后近乎成了 Java 社区的日志标准。据说 Apache 基金会还曾经建议 sun 引入 Log4j 到 java 的标准库中，但 Sun 拒绝了。
> - 2002年 Java1.4 发布，Sun 推出了自己的日志库 JUL(Java Util Logging),其实现基本模仿了 Log4j 的实现。在 JUL 出来以前，log4j 就已经成为一项成熟的技术，使得 log4j 在选择上占据了一定的优势。
> - 2005年，Apache 推出了 JCL(Jakarta Commons Logging)，JCL只是定义了一套日志接口(其内部也提供一个Simple Log的简单实现)，支持运行时动态加载日志组件的实现，也就是说，在你应用代码里，只需调用 Commons Logging 的接口，底层实现可以是log4j，也可以是Java Util Logging。
> - 2006年，Ceki Gülcü (Log4j最初由他编写) 开发了 SLF4J(The Simple Logging Facade for Java) 作为 JCL(Jakarta Commons Logging) 的更可靠替代方案。之后又开发了 Logback 框架 (Slf4j 的实现)，并回瑞典创建了 QOS 公司，QOS 官网上是这样描述 Logback 的：The Generic，Reliable Fast&Flexible Logging Framework(一个通用，可靠，快速且灵活的日志框架)。
> - 之后，Java 日志领域被划分为两大阵营：Commons Logging 阵营和 SLF4J 阵营。 Commons Logging 在 Apache 大树的笼罩下，有很大的用户基数。但有证据表明，形式正在发生变化。2013 年底有人分析了 GitHub 上30000个项目，统计出了最流行的100个 Libraries，可以看出 slf4j 的发展趋势更好
> - 2012-07 Apache 重写了 log4j 1.x，成立了新的项目 Log4j2。Log4j2 具有 Logback 的所有特性。

现在，SLF4J + Logback 是 Spring Boot 的默认日志框架。

## 日志框架的分类

日志框架分为**日志门面框架**和**日志实现框架**，那什么是**日志门面框架**和**日志实现框架**呢

- **日志门面框架**指的是：无具体实现的抽象门面框架，如：Commons Logging、SLF4J
- **日志实现框架**：具体实现的框架，如：Log4j，Log4j2，Logback，Jul(java.util.logging)

### 日志门面框架

日志门面:是门面模式的一个典型的应用,门面模式，也称外观模式。日志门面框架就使一套提供了日志相关功能的接口而无具体实现的框架，其调用具体的实现框架来进行日志记录。也就是说日志门面天然的兼容日志实现框架。

在java 生态中国日志门面框架主要有两个 Commons Logging 和 SLF4J

#### **Commons Logging**

common-logging 是 apache 提供的一个通用的日志接口， 在 common-logging 中，有一个 Simple logger 的简单实现，但是它功能很弱，所以使用 common-logging，通常都是配合着 log4j 来使用。

#### SLF4J

SLF4J(Simple Logging Facade for Java），是一套包装 Logging 框架的界面程式，以外观模式实现。可以在软件部署的时候决定要使用的 Logging 框架，目前主要支援的有Java Logging API、Log4j及logback等框架。以MIT 授权方式发布。SLF4J 的作者就是 Log4j和Logback 的作者 Ceki Gülcü。

### 日志实现框架

#### Log4j

最早的日志实现框架，早期由 Ceki Gülcü 开发，现在则是Apache软件基金会的一个项目。

#### JUL(Java Util Logging)

Java 1.4 以来的官方日志实现,主要参考自 Log4j

#### Logback

Log4j 的早期作者 Ceki Gülcü 开发的新一代日志框架

#### Log4j2

Apache Log4j2 是 Apache Log4j 的升级产品,并且不兼容 Log4j，主要参考自 Logback

## 框架选择

- **成本考虑**：Logback 的所有文档是全面免费提供，Apache Log4J 只提供部分免费文档,其余部分需要付费购买。
- **资源开销**：Commons Logging 相比较与 SLF4J 开销更高.
- **性能**：Logback 相比 Log4j、Log4j2 拥有更好的性能。Logback声称：某些关键操作，比如判定是否记录一条日志语句的操作，其性能得到了显著的提高。这个操作在Logback中需要3纳秒，而在Log4J中则需要30纳秒。LogBack创建记录器（logger）的速度也更快：13毫秒，而在Log4J中需要23毫秒。更重要的是，它获取已存在的记录器只需94纳秒，而Log4J需要2234纳秒，时间减少到了1/23。跟JUL相比的性能提高也是显著的。

SLF4J + Logback 成为了实际开发中的最佳选择。

-----------

# SpringBoot项目中配置SLF4J+Logback

## 引入依赖

要开始使用 `Logback`，首先需要将`logback-classic`依赖项添加到类路径中。让我们用 `Maven` 来做到这一点：

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

引入这个单一依赖项就足够了，因为它将间接引入`logback-core`和`slf4j-api`这两个依赖项。

**如果没有定义自定义配置，Logback 会自行提供简单的自动配置。**默认情况下，这可确保日志语句以 `DEBUG` 级别打印到控制台。

因此，您现在可以获得*`Logger`*实例并开始使用默认的基本配置写入日志消息。

首先，您可以使用`slf4j LoggerFactory`类创建`Logger`：

```java
private final static Logger LOGGER = LoggerFactory.getLogger(LogbackTestApplication.class);
```

接下来，您可以简单地使用你想要的日志级别并打印日志：

```java
LOGGER.info("LogbackTestApplication running...");
```

## Logback配置文件

**要为 Logback 创建配置，您可以使用 XML 以及 Groovy**。只要您遵守命名约定，系统就会自动选择并使用配置。

您可以选择三个有效的标准文件名：

- *logback-test.xml*
- *logback.groovy*
- *logback.xml*

这里值得注意的是，`Logback` 将按照这个确切的顺序搜索这些文件。

展望未来，本教程中的示例将依赖于基于 `XML` 的 `logback.xml`文件。

让我们看看默认配置的基本配置是什么样的：

```xml
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>
    <root level="debug">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

此配置定义了一个`ConsoleAppender`。控制台上的日志消息将使用`<pattern>`中配置的样式显示在打印控制台。

```
2023-08-19 15:57:13.086  INFO 72780 --- [           main] c.g.logbacktest.LogbackTestApplication   : LogbackTestApplication running...
```

## Logback 配置文件结构

Logback 配置文件的基本结构如下:

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/logback/23144709_brxi.webp)

如上，根节点为<code>\<configuration></code>元素，包含零个或多个<code>\<appender></code>元素，然后是零个或多个<code>\<logger></code>元素，然后是**最多一个**<code>\<root></code>**元素**。

`logger`、`root` 作为日志的记录器，把它关联到应用的对应的 `context` 上后，主要用于存放日志对象，也可以定义日志类型、级别。

`appender` 主要用于指定日志输出的目的地，目的地可以是控制台、文件、远程套接字服务器、 `MySQL`、`PostreSQL`、 `Oracle` 和其他数据库、 `JMS` 和远程 `UNIX Syslog` 守护进程等。

### 根节点\<configuration>

根节点 <code>\<configuration></code> 常用的属性包含`scan`、`scanPeriod`、`debug`三个属性。

1. **scan**：当此属性设置为 `true` 时，如果配置文件发生改变，会自动重新加载配置文件，默认值为 `true`.
2. **scanPeriod**：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当 scan 为 true 时，此属性生效。默认的时间间隔为 1 分钟.
3. **debug**：当此属性设置为 `true` 时，将打印出 logback 内部日志信息，实时查看 logback 运行状态。默认值为 false.

```xml
<configuration scan="true" scanPeriod="60 second" debug="false">  
      <!-- 其他配置省略-->  
</configuration>
```

注意 `scanPeriod` 值的写法，`数值英文空格时间单位` 。 如果未指定时间单位，则默认时间单位为毫秒

### 变量 \<property>

#### 自定义变量

在<code>\<configuration> </code>中，可以通过<code>\<property> </code> 来定义一个变量，属性 **name** 是变量的名称，属性 **value** 是变量的值。

```xml
<configuration>
  <property name="LOG_PATTERN" value="%date %level [%thread] %logger{10} [%file : %line] %msg%n" />
  ...
</configuration>
```

#### 使用自定义变量

通过 <code>${变量名}</code> 来引用变量

```xml
<configuration>
    <property name="LOG_PATTERN" value="%date %level [%thread] %logger{10} [%file : %line] %msg%n" />
    
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>
  ...
</configuration>
```

#### 使用配置文件中的属性

```xml
<!-- 读取spring.application.name中的属性来生成日志文件名 -->
<springProperty scope="context" name="logName" source="spring.application.name" defaultValue="localhost.log"/>
```

<code>\<springProperty> </code>标签允许我们从`Spring`中显示属性，`Environment` 以便在`Logback`中使用。如果你想将读取`application.properties`文件中的值，这将非常有用。

```xml
<!-- 读取spring.application.name中的属性来生成日志文件名 -->
<springProperty scope="context" name="logName" source="spring.application.name" defaultValue="localhost.log"/>
```

### 日志输出器 \<appender>

<code>\<appender></code> (日志输出器)，用于将日志按照**一定的格式**输出到控制台、文件、数据库等地方，**logger(日志记录器)** 需要使用 **appender(日志输出器)** 将记录器中的日志输出。

**appender** 有两个**必填属性**

- **name** ：`appender` 的名称，任意填写，不要重名就行
- **calss**：某个具体的 `appender` 的完全类名，它是 `appender` 的具体实现，`Logback` 自带常用的几个 `appender`。
  - ch.qos.logback.core.ConsoleAppender ：将日志输出到控制台的 `appender`
  - ch.qos.logback.core.rolling.RollingFileAppender ：将日志输出到文件，并按照条件切换输出到新建文件(滚动输出，自动切割)

<code>\<encoder></code> 负责将事件(日志)转换为字节数组，并将该字节数组写出为 `OutputStream`。

<code>\<encoder></code> 是<code>\<appender></code> 的子节点，在 <code>\<encoder></code> 节点中，最重要的是配置 <code>\<pattern></code>，它是用来定义日志输出格式。

```xml
<appender name="" class="">
    <encoder>
        <pattern></pattern>
        <charset></charset> 
    </encoder>
</appender>
```

#### \<pattern>

 <code>\<pattern></code>用于定义日志的输出格式，通过 `Logback` 中的转换说明符(Conversion specifier)（其实就是一些预定义变量），可以方便的组合出我们想要的日志格式

在这里我只列举出常用的转换说明符(Conversion specifier)，更多请参考[官方文档](https://link.juejin.cn?target=http%3A%2F%2Flogback.qos.ch%2Fmanual%2Flayouts.html)

##### 日期、时间

假设系统时间是 2006-10-20 14:06:49,812

| Conversion Pattern                            | Result                  |
| --------------------------------------------- | ----------------------- |
| %d                                            | 2006-10-20 14:06:49,812 |
| %date                                         | 2006-10-20 14:06:49,812 |
| %date{ISO8601}                                | 2006-10-20 14:06:49,812 |
| %date{HH:mm:ss.SSS}                           | 14:06:49.812            |
| %date{yyyy-MM-dd HH:mm:ss.SSS, Asia/Shanghai} | 2006-10-20 14:06:49,812 |

##### 日志记录器

输出日志记录事件源处的记录器名称，下表提供了缩写算法的实际应用示例。

| 转换说明符  | 记录器名称                 | 输出结果                   |
| ----------- | -------------------------- | -------------------------- |
| %logger     | mainPackage.sub.sample.Bar | mainPackage.sub.sample.Bar |
| %logger{0}  | mainPackage.sub.sample.Bar | Bar                        |
| %logger{5}  | mainPackage.sub.sample.Bar | m.s.s.Bar                  |
| %logger{10} | mainPackage.sub.sample.Bar | m.s.s.Bar                  |
| %logger{15} | mainPackage.sub.sample.Bar | m.s.sample.Bar             |
| %logger{16} | mainPackage.sub.sample.Bar | m.sub.sample.Bar           |
| %logger{26} | mainPackage.sub.sample.Bar | mainPackage.sub.sample.Bar |

##### 类名、方法名、行号

| Conversion Pattern | Result                                                      |
| ------------------ | ----------------------------------------------------------- |
| %C、%class         | 输出log的类的完整类名,获取类名的效率一般,只建议在需要时使用 |
| %M 、%method       | 输出log的方法                                               |
| %L 、%line         | 输出log的行号。获取行号的效率一般，只建议在需要时使用       |

##### 线程

| Conversion Pattern | Result         |
| ------------------ | -------------- |
| %t 、%thread       | 输出日志的线程 |

##### 消息请求级别

| Conversion Pattern | Result       |
| ------------------ | ------------ |
| %p、%le、 %level   | 消息请求级别 |

##### 日志信息

| Conversion Pattern   | Result   |
| -------------------- | -------- |
| %m 、%msg 、%message | 日志信息 |

##### 换行

| Conversion Pattern | Result |
| ------------------ | ------ |
| %n                 | 换行   |

组合示例：

```xml
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern>%date{yyyy-MM-dd HH:mm:ss.SSS, Asia/Shanghai} %red(%-5level) --- [%thread] %cyan(%class.%method/%line) : %msg%n</pattern>
    </encoder>
</appender>
```

##### 颜色

对于控制台的输出，Logback 支持填充颜色，支持的颜色如下

- %black()
- %red()
- %green()
- %yellow()
- %blue()
- %magenta()
- %cyan()
- %white()
- %gray()
- %boldRed()
- %boldGreen()
- %boldYellow()
- %boldBlue()
- %boldMagenta()
- %boldCyan()
- %boldWhite()
- %highlight()

使用方法是用颜色的代码把消息包起来，比如想将日志级别设置成红色 `%red(%-5level)`

#### 设置字符集 \<charset>

<code>\<charset></code> 是 <code>\<encoder></code> 的子节点，用于设置输出字符集

```xml
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern>%date{yyyy-MM-dd HH:mm:ss.SSS, Asia/Shanghai} %red(%-5level) --- [%thread] %cyan(%class.%method/%line) : %msg%n</pattern>
        <charset>UTF-8</charset> 
    </encoder>
</appender>
```

#### ConsoleAppender

`ch.qos.logback.core.ConsoleAppender` 是 `Logback` 自带的 `appender`，用于将日志输出到控制台。

对应控制台，只需要重点配置 <code>\<encoder></code>节点

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%date %red(%-5level) --- [%thread] %cyan(%class.%method/%line) : %msg%n</pattern>
            <charset>UTF-8</charset> 
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

#### RollingFileAppender

`ch.qos.logback.core.rolling.RollingFileAppender` 是 `Logback` 自带的 `Appender`，用于将日志输出到文件，并按照条件切换输出到新建文件(滚动输出，自动切割)

| 属性             | 类型             | 说明                                         |
| ---------------- | ---------------- | -------------------------------------------- |
| file             | String           | 文件名，如果文件不存在，则创建。注意文件路径 |
| append           | boolean          | true 在文件末尾追加，false 覆盖，默认 true   |
| encoder          | Encoder          | 编码器，设置日志输出格式和编码               |
| rollingPolicy    | RollingPolicy    | 滚动策略，描述该如何滚动                     |
| triggeringPolicy | TriggeringPolicy | 触发策略，什么条件下触发滚动                 |

要使用 `RollingFileAppender RollingPolicy` (滚动策略)和 `TriggeringPolicy`(触发策略)必不可少。但是，如果 `RollingPolicy` 实现了 `TriggeringPolicy` 接口，则仅需要指定 `RollingPolicy`。

`rollingPolicy`(滚动策略) 里面有一个属性叫 `fileNamePattern`，它和 `file` 属性一样都表示日志文件名。

#### 滚动策略 —— RollingPolicy

`RollingPolicy` 负责日志文件的移动和重命名的过程。
 `Logback` 提供了多种 `RollingPolicy` 的实现

- `TimeBasedRollingPolicy` ：按时间滚动，比如按天或按月
- `SizeAndTimeBasedRollingPolicy` ：按时间和大小滚动，比如说在按天滚动的基础上设置大小，防止某天的日志文件过大！

#### TimeBasedRollingPolicy

`TimeBasedRollingPolicy`可能是最受欢迎的滚动政策。它定义了基于时间的展期策略，例如按天或按月。 

`ch.qos.logback.core.rolling.TimeBasedRollingPolicy` 滚动策略同时也实现了 `TriggeringPolicy`(触发策略)

因此配置好 `TimeBasedRollingPolicy` 后，就不需要配置 `TriggeringPolicy`。

`TimeBasedRollingPolicy`的配置包含一个必选的`fileNamePattern`属性和几个可选属性。

| 属性名              | 类型    | 描述                                                         |
| ------------------- | ------- | ------------------------------------------------------------ |
| fileNamePattern     | String  | fileNamePattern 是最为复杂也最为重要的属性，同时也是必填项，将在下文中详细介绍 |
| maxHistory          | Int     | 可选的 maxHistory 属性控制要保留的归档文件的最大数量，异步删除旧文件。 例如，如果您指定每月滚动，并将 maxHistory 设置为 6，则将保留近 6 个月的存档文件，并删除超过 6 个月的文件。 请注意，当旧的存档日志文件被删除时，为日志文件存档而创建的任何文件夹都将根据需要删除。<br/><br/>将 maxHistory 设置为0会禁用存档删除。 默认情况下，maxHistory 设置为0，即默认情况下不删除存档。 |
| totalSizeCap        | Int     | 可选的totalSizeCap属性控制所有归档文件的总大小，在 maxHistory 限制的基础上，进一步限制 所有存档文件的总大小。当超过总大小上限时，最旧的存档将被异步删除。totalSizeCap 依赖于 maxHistory ，如果没有 maxHistory ，单独设置 totalSizeCap 是不生效的。<br/><br/>TotalSizeCap 属性可以通过在数值后分别添加 KB、MB 和 GB 来指定为字节、千字节、兆字节或千兆字节的单位。 例如，5000000、5000KB、5MB 和 2GB 都是有效值，前三个是等效的。 没有后缀的数值以字节为单位。<br/><br/>默认情况下，totalSizeCap 设置为零，这意味着没有总大小上限。 |
| cleanHistoryOnStart | boolean | 如果设置为 true，则存档文件将在appender启动时删除。 默认情况下，此属性设置为 false。<br/><br/>存档删除通常在滚动期间执行。 但是，某些应用程序的生存时间可能不够长，无法触发滚动。 因此，对于此类短暂的应用程序，存档删除可能永远没有机会执行。 通过将 cleanHistoryOnStart 设置为 true，存档删除将在appender启动时执行。 |

#### fileNamePattern 属性

`fileNamePattern` 的值包含文件名以及支持绝对和相对路径和日期转换符——`%d`。

`% d`转换符可以包含由 `java.text.SimpleDateFormat`类指定的日期和时间模式。如果省略日期和时间模式，则 采用默认模式`yyyy-MM-dd` 。滚动周期是根据`fileNamePattern`的值推断出来的。

`%d` 表示系统当前的时间，默认格式为 %d{yyyy-MM-dd}，通过 `%d`{日期格式} 来自定义。

`fileNamePattern` 属性内或日期和时间模式内任意位置的 正斜杠“/”或反斜杠“\”字符将被解释为目录分隔符。

**时区**

%d 默认采用主机时区，也可以自定义%d{yyyy-MM-dd,UTC+8}

**多个%d**

可以指定多个 `%d` 转换符，但只有其中一个可以是主要的，即用于推断滚动周期。

多个 `%d`转换符允许您以不同于滚动周期的文件夹结构来组织存档文件。 例如，下面显示的文件名模式按年和月组织日志文件夹，但每天午夜滚动更新日志文件。

```xml
/var/log/%d{yyyy/MM, aux}/myapplication.%d{yyyy-MM-dd}.log
```

**自动文件压缩**
`TimeBasedRollingPolicy`支持自动文件压缩。如果`fileNamePattern`选项的值 以<code style="color: green; font-weight: bold">.gz</code>或.<code style="color: green; font-weight: bold">zip</code>结尾，则启用此功能。

| fileNamePattern     | **Rollover schedule**                              | **Example**                                                  |
| ------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
| `/wombat/foo.%d.gz` | 每日滚动（午夜），自动对存档文件进行 `GZIP` 压缩。 | 如果没有设置<code><file></code>，2009 年 11 月 23 日期间，日志将输出到文件 `/wombat/foo.2009-11-23`。但是，在午夜该文件将被压缩为 `/wombat/foo.2009-11-23.gz`。11 月 24 日，日志记录输出将定向到 `/wombat/folder/foo.2009-11-24`，以此类推。<br> 如果设置<code><file></code>为`/wombat/foo.txt`，2009 年 11 月 23 日期间，日志输出将转到文件`/wombat/foo.txt`。午夜该文件将被压缩并重命名为 `/wombat/foo.2009-11-23.gz`。并将创建一个新的 `/wombat/foo.tx`t文件，并将11月24日期间的日志记录到该文件。在11月24日午夜，`/wombat/foo.txt`将被压缩并重命名为`/wombat/foo.2009-11-24.gz`。 |

`fileNamePattern`有双重用途。首先，logback通过研究模式 计算请求的滚动周期。其次，它计算每个存档文件的名称。请注意，两种不同的模式可以指定相同的周期。模式yyyy-MM和 yyyy@MM都指定每月滚动，尽管生成的存档文件将带有不同的名称。

通过设置<code>\<file></code>属性，您可以分离活动日志文件的位置和归档日志文件的位置。日志输出将定位到文件属性指定的文件中 。由此可见，活动日志文件的名称不会随时间而改变。但是，如果您选择忽略file属性，则将根据fileNamePattern的值在每个周期重新计算活动文件名称。通过保留<code>\<file></code>属性未设置，您可以避免在滚动期间存在引用日志文件的外部文件句柄时发生的 文件重命名错误。

由于各种技术原因，滚动不是时钟驱动的，而是取决于日志记录事件的到达。例如，在 2002 年 3 月 8 日，假设 fileNamePattern设置为yyyy-MM-dd （每日滚动），则午夜后第一个事件的到达将触发滚动。如果在午夜后 23 分 47 秒期间没有记录事件，则滚动实际上将发生在 3 月 9 日上午 00:23'47，而不是上午 0:00。因此，根据事件的到达率，可能会以一定的延迟触发滚动。然而，无论延迟如何，滚动算法都已知是正确的，从某种意义上说，在特定时间段内生成的所有日志记录事件都将输出到界定该时间段的正确文件中。

#### SizeAndTimeBasedRollingPolicy

有时您可能希望基本上按日期归档文件，但同时限制每个日志文件的大小，特别是日志文件处理工具对大小有着明确的限制。为了满足这一要求，`logback` 附带了 `SizeAndTimeBasedRollingPolicy`.

请注意，`TimeBasedRollingPolicy`已经允许限制归档日志文件的组合大小。如果您只想限制日志归档的组合大小，那么 `TimeBasedRollingPolicy`上面描述的和设置`totalSizeCap`属性应该足够了。

`maxFileSize` 属性表示单个归档日志文件的大小，单位有 `KB`、`MB`、`GB`。

```xml
<configuration>
    <appender name="SizeAndTimeFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/app1/app1.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
          <fileNamePattern>logs/app1/backup/app1_%d_%i.zip</fileNamePattern>
           <!--单个日志文件最大10MB-->
           <maxFileSize>10MB</maxFileSize>
           <!--删除n个滚动周期之前的日志文件(最多保留前n个滚动周期的历史记录)件-->
           <maxHistory>60</maxHistory>
           <!--在 maxHistory 限制的基础上，进一步限制所有存档文件的总大小-->
           <totalSizeCap>1GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%date %-5level --- [%thread] %class.%method/%line : %msg%n</pattern>
        </encoder>
    </appender>
    
    <root level="DEBUG">
        <appender-ref ref="SizeAndTimeFile" />
    </root>
</configuration>
```

<code>%i</code> 是一个滚动周期内的归档序列号，从0开始

注意：`maxHistory` 是表了 `n` 个滚动周期的日志文件，而不是 `n` 个的日志文件，在设置了 maxFileSize 后，一个滚动周期内可能有多个日志文件。

-------------

### 日志记录器 \<logger>

```xml
<logger name="" level="" additivity="" >
    <appender-ref ref="" />
</logger>
```

<code>\<logger></code> 有三个属性

- **name** : 包名或类名。即，将该记录器作用于哪个类或包下。必填
- **level** : 该记录器的级别，低于该级别的日志消息不记录。可选级别从小到大为 **TRACE、DEBUG、INFO、WARN、ERROR、ALL、OFF**(不区分大小写)。选填，不填则默认从父记录器继承**level**
- **additivity** : 是否追加父 Logger 的输出源(appender),默认为true，选填。如果只想输出到自己的输出源(appender)，需要设置为 false

`logger` 还可以包含 0 或多个 `<appender-ref>` 元素, 将记录的日志使用 `appender` 进行输出

#### level 的继承

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds">
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%date %level [%thread] %logger{10} [%file : %line] %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="com.wqlm.boot.user.controller.UserController" ></logger>
    <logger name="com.wqlm.boot.user.controller" ></logger>

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

如上，`"com.wqlm.boot.user.controller.UserController"`的 `logger` 没有定义 `level`，因此它会继承父 logger 的 level。

那它的父 logger 是谁呢？ 其实就是下面的 `"com.wqlm.boot.user.controller"`的 `logger`!

{% note success no-icon %}

Logback 是根据包或类的层次结构来确定 logger 的父子关系的

{% endnote %}

`"com.wqlm.boot.user.controller"`的 `logger` 也没有定义 `level`，因此它也会继承它的父 `logger` 的 `level`。但是，在该 xml 中，已经没有比它层次还“浅”的 `logger` 了，因此，它的父 `logger` 为 **root**。也就是说它会继承 **root** `logger` 的 `level`，也就是 `info`。

#### additivity 的追加策略

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds">
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%date %level [%thread] %logger{10} [%file : %line] %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="com.wqlm.boot.user.controller.UserController" ></logger>
    <logger name="com.wqlm.boot.user.controller" ></logger>

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

如上，`"com.wqlm.boot.user.controller.UserController"`的 `logger` 没有设置 `additivity`，因此 `additivity` 采用默认值 `true`，即，追加它父 `logger` 中的 **appender** 到自己的 `logger` 中。

在看它的父 `logger` —— `"com.wqlm.boot.user.controller"` `logger`， `additivity` 也采用默认值，因此，它也会追加它父 `logger` 中的 **appender** 到自己的 `logger` 中。
 在该 `xml` 中 `"com.wqlm.boot.user.controller"` `logger` 的父 `logger` 为 `root logger`！，因此，它会追加 名为 STDOUT 的 `appender` 到自己的 `logger` 中

刚才是从上层往下层走，直到找到显示标注 `appender="false"` 的 `logger` 或 `root logger` 才停止追加。
 现在要从下层往上层走，把层层追加的 `appender` 往上传，直到遇到显示标注  `appender="false"` 的 `logger` 才停止

因此，`"com.wqlm.boot.user.controller.UserController"`的 `logger` 中，也有一个名为 STDOUT 的 `appender`，这个 `appender` 是从它的父 `logger` 中来的

所以，一般情况下，只需要给 `root logger` 添加 `appender` 即可！！！其他 `logger` 都会直接或间接获取到 `root logger` 的 `appender`。

### 根记录器 \<root>

```xml
<root level="">
    <appender-ref ref="" />
</root>
```

该<code>\<root></code>元素配置根记录器。它支持单一属性，即`level`属性。它不允许任何其他属性，因为可加性标志不适用于根记录器。此外，由于根记录器已被命名为“ROOT”，因此它也不允许使用名称属性。`level` 属性的值可以是不区分大小写的字符串 `TRACE`、`DEBUG`、`INFO`、`WARN`、`ERROR`、`ALL` 或 `OFF` 之一。请注意，根记录器的级别不能设置为 `INHERITED` 或 `NULL`。

与元素<code>\<logger></code>类似，<code>\<root></code>元素可以包含零个或多个 <code>\<appender-ref></code>元素；这样引用的每个日志记录器都会添加到根记录器中。请注意，与 `log4j` 不同，`logback-classic`在配置根记录器时 不会关闭或删除任何先前引用的日志记录器。

------

#### Filter

`Filter`：过滤器，过滤满足条件或者不满足条件的日志
允许组合使用，可配置多个过滤器有很多，只介绍两种常用的`LevelFilter`、`ThresholdFilter`

| 名称            | 值说明     | 说明                   | 备注 |
| --------------- | ---------- | ---------------------- | ---- |
| LevelFilter     | 级别过滤器 | 精确匹配指定的日志级别 |      |
| ThresholdFilter | 阈值过滤   | 匹配等于或高于指定级别 |      |

使用示例如下:

```xml
<filter class="ch.qos.logback.classic.filter.LevelFilter">
    <!-- 级别 -->
    <level>INFO</level>
    <!-- 匹配时的操作：接收（记录） -->
    <onMatch>ACCEPT</onMatch>
    <!-- 不匹配时的操作：拒绝（不记录） -->
    <onMismatch>DENY</onMismatch>
</filter>
    
<!-- deny all events with a level below INFO, that is TRACE and DEBUG -->
<!-- 拒绝所有级别低于 INFO 的, 也就是 TRACE 和 DEBUG -->
<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
    <level>INFO</level>
</filter>
```

LevelFilter属性如下:

`onMatch`、`onMismatch`的取值类型有三种:

- `DENY`：拒绝，不再执行后续的过滤器
- `NEUTRAL`：中性，继续执行后面的过滤器
- `ACCEPT`：接受，记录日志，不再执行后续过滤器

---

### 日志有效级别

记录器可以被分配级别。在类中定义 `ch.qos.logback.classic.Level`了可能的级别集（**TRACE**、**DEBUG**、**INFO**、**WARN** 和 **ERROR**）。

如果给定的记录器没有分配级别，那么它会从其最接近的祖先那里继承一个已分配级别的级别。

为了确保所有记录器最终都能继承级别，根记录器始终具有指定的级别。默认情况下，该级别为 **DEBUG**。

根据定义，打印方法决定日志记录请求的级别。例如，如果`L`是一个记录器实例，则该语句`L.info("..")`是 **INFO** 级别的日志记录语句。

如果日志请求的级别高于或等于其记录器的有效级别，则称该日志请求已*启用。*否则，该请求被称为被*禁用*。

{% note success no-icon %}

*基本选择规则：*

*如果`p >= q`，则启用 向有效级别q的记录器发出级别p的日志打印请求。*

{% endnote %}



这条规则是 `logback` 的核心。它假设级别按如下顺序排序： `TRACE < DEBUG < INFO <  WARN < ERROR`。

以更形象的方式，以下是选择规则的工作原理。在下表中，垂直标头显示日志记录请求的级别，由`p`指定，而水平标头显示记录器的有效级别，由`q`指定。行（级别请求）和列（有效级别）的交集是由基本选择规则产生的布尔值。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/logback/effectivelevel.png)

例子:

```java
import ch.qos.logback.classic.Level;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
....

// get a logger instance named "com.foo". Let us further assume that the
// logger is of type  ch.qos.logback.classic.Logger so that we can
// set its level
ch.qos.logback.classic.Logger logger = 
        (ch.qos.logback.classic.Logger) LoggerFactory.getLogger("com.foo");
//set its Level to INFO. The setLevel() method requires a logback logger
logger.setLevel(Level. INFO);

Logger barlogger = LoggerFactory.getLogger("com.foo.Bar");

// This request is enabled, because WARN >= INFO
logger.warn("Low fuel level.");

// This request is disabled, because DEBUG < INFO. 
logger.debug("Starting search for nearest gas station.");

// The logger instance barlogger, named "com.foo.Bar", 
// will inherit its level from the logger named 
// "com.foo" Thus, the following request is enabled 
// because INFO >= INFO. 
barlogger.info("Located nearest gas station.");

// This request is disabled, because DEBUG < INFO. 
barlogger.debug("Exiting gas station search");
```


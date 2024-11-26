---
title: 记一次线上jar包冲突问题
date: 2024-11-21 14:40:16
tags:
- jar包冲突
categories: 项目问题
---

一次在项目开发过程中需要用到加密相关的功能，然后公司就给了一个**jar**包，我们上传私服就通过**pom**引入在项目中。并基于这个新依赖开发相关的功能，我们在本地**IDEA**编译测试都没问题，然后我们打包上传到虚拟机上运行时，调用加密方法的时候会报错，并且错误信息给不了我们任何提示，然后在本地测试却是正常的。

**Linux**虚拟机上我们服务的运行方式如下：

1. 我们通过本地的**IDEA**把项目打成zip包上传到虚拟机上
2. 我们通过运行启动脚本来启动我们的服务
   - 启动脚步先把**zip**文件解压到指定的**lib**文件夹
   - 然后用**nohup**命令启动**Java**服务

我们通过启动脚本启动我们的服务时，服务起不起来，而且关键的错误信息没有打印。于是我们先调整了日志文件，让关键信息能够打印，调整日志文件后我们看到了关键的错误信息：`NoSuchMethodError`

通过这个错误信息，就很容易想到应该是jar包冲突了，但是很奇怪的就是我们在本地测试没问题，但是部署到虚拟机上就报错，应该是类的加载顺序不一致导致的。

**复习一下类加载机制**

我们写的 Java 应用代码，一般是通过 `App ClassLoader` 应用加载器进行加载，它不会自己先去加载它，而是通过 `Extension ClassLoader` 扩展类加载器进行加载（其中扩展类加载器又会去找 `Bootstrap ClassLoader` 启动类加载器进行加载），只有父加载器无法加载情况下，才会让下级加载器进行加载。

> ★
>
> 当一个**ClassLoader**实例需要加载某个类时，它会试图亲自搜索某个类之前，先把这个任务委托给它的父类加载器，这个过程是由上至下依次检查的，首先由最顶层的类加载器**Bootstrap ClassLoader**试图加载，如果没加载到，则把任务转交给**Extension ClassLoader**试图加载，如果也没加载到，则转交给**App ClassLoader** 进行加载，如果它也没有加载得到的话，则返回给委托的发起者，由它到指定的文件系统或网络等**URL**中加载该类。如果它们都没有加载到这个类时，则抛出**ClassNotFoundException**异常。
>
> ”

**ClassLoader**

Java 使用的是双亲委派加载机制，通过查看 `ClassLoader` 类，可以对此有所了解。

类被成功加载后，将被放入到内存中，内存中存放 `Class` 实例对象。

```java
protected Class<?> loadClass(String name, boolean resolve)
    throws ClassNotFoundException
{
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        // 首先，检查 class 是否已经被加载
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            // 如果没有被加载
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
                    // 寻找 parent 加载器
                    c = parent.loadClass(name, false);
                } else {
                    // 如果父加载器不存在，则委托给启动类加载器加载
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
            }
            if (c == null) {
                // If still not found, then invoke findClass in order
                // to find the class.
                // 如果仍然无法加载，才会尝试自身加载
                long t1 = System.nanoTime();
                c = findClass(name);
                // this is the defining class loader; record the stats
                sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                sun.misc.PerfCounter.getFindClasses().increment();
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}
```

**类加载顺序**

从代码中了解到，如果某个名字的类被加载后，类加载器是不会再重新加载，所以我们的问题根本原因可以是出现在：

我们本地通过IDEA启动时，加载的那个class文件正好是我们需要的使用的，但是当我们在虚拟机上启动的时候，首先加载的那个类是其他jar里面的同名类，并且全类名一样，并非我们需要的。

通过查阅文章：

> ★
>
> 跟JAR文件的文件名有关。按照字母的顺序加载JAR文件。有了这个类以后，后面的类则不会加载了。
>
> jvm 加载包名和类名相同的类时，先加载classpath中jar路径放在前面的，包名类名都相同，那jvm没法区分了，如果使用ide一般情况下是会提示发生冲突而报错，若不报错，只有第一个包被引入（在classpath路径下排在前面的包），第二个包会在classloader加载类时判断重复而忽略。
>
> ”

**查看加载顺序**

在 `jvm` 启动脚本中，添加 `-verbose` 参数或者 `-XX:+TraceClassLoading`

按理说加载顺序按照**字母顺序**加载，预发环境还是能够跟本地开发一样，加载到我们需要的类。实际上，加载器加载到的是另一个类，导致应用无法启动。

通过查找资料

> ★
>
> 问题就是jar的加载顺序问题，而这个顺序实际上是由文件系统决定的，linux内部是用inode来指示文件的。
>
> 这种储存文件元信息的区域就叫做inode，中文译名为”索引节点”。每一个文件都有对应的inode，里面包含了与该文件有关的一些信息。
>
> Unix/linux系统内部不使用文件名，而使用inode号码来识别文件。对于系统来说，文件名只是inode号码便于识别的别称或者绰号。
>
> ”

--------------------

**相关知识点**

**maven**包加载顺序和**jvm**类加载顺序

一、`mvaven jar`包加载顺序

`Maven`对版本不同的相同依赖包生效优先级：

1. 不同依赖层级深度的遵从【最短路径优先】原则。
2. 具有相同依赖层级深度的遵从`pom`中【最先声明优先】原则。

二、`JVM`类加载顺序

相同的类指：类的全限定名一样
问题：怎么优先加载自己写的类

1. 解析：

   `class`文件所在位置， 直接在`classpath`下，在`jar`包中：

- a.相同的类， 一个在工程`src`路径下， 一个在`jar`包中： 此时优先加载工程`src`文件。
- b.相同的类， 都在`jar`包中：此时按照`jar`的装载顺序，它加载的顺序完全取决于操作系统！

`jar`包冲突的解决办法:

1. 如果引起冲突类的jar包可以进行排除，在引入的`pom`文件中进行`exclude`
2. 如果引起冲突的`jar`包排除了会引起项目启动报错，即两个jar包必须共存
   - 如果冲突的`class`文件数量不多，则可以将存在冲突并且我们需要的`class`文件，从`jar`中类`copy`到`src`目录下
   - 如果冲突的`class`文件数量很多，上述方式不现实则可以考虑通过`maven shade plugin`插件改包名

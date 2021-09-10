---
title: 初识Maven
date: 2021-04-15 14:11:54
tags: [Maven,Java,依赖管理]
---

## 什么是Maven?

Maven 是 Apache 软件基金会组织维护的一款自动化构建工具 ，专注服务于ava平台的项目构建和依赖管理 。Maven 这个单词的本意是：专家 ，内行。

### 依赖管理

​    在今天的 JavaEE 开发领域，有大量的第三方框架和工具可以供我们使用。要使用这些 jar 包最简单的方法就是复制粘贴到 WEB-INF/lib 目录下。但是这会导致每次创建一个新的工程就需要将 jar 包重复复制到 lib 目录下，从而造成工作区中存在大量重复的文件，让我们的工程显得很臃肿。

​	而使用 Maven 后每个 jar 包本身只在本地仓库中保存一份，需要 jar 包的工程只需要以坐标的方式简单的引用一下就可以了。不仅极大的节约了存储空间，让项目更轻巧，更避免了重复文件太多而造成的混乱。

​	jar 包往往不是孤立存在的，很多 jar 包都需要在其他 jar 包的支持下才能够正常工作，我们称之为jar 包之间的依赖关系。最典型的例子是：commons-fileupload-1.3.jar 依赖于 commons-io-2.0.1.jar，如果没有 IO 包，FileUpload 包就不能正常工作。

​	那么问题来了，你知道你所使用的所有 jar 包的依赖关系吗？当你拿到一个新的从未使用过的 jar包，你如何得知他需要哪些 jar 包的支持呢？如果不了解这个情况，导入的 jar 包不够，那么现有的程序将不能正常工作。再进一步，当你的项目中需要用到上百个 jar 包时，你还会人为的，手工的逐一确认它们依赖的其他 jar 包吗？这简直是不可想象的。

​	而引入 Maven 后，Maven 就可以替我们自动的将当前 jar 包所依赖的其他所有 jar 包全部导入进来，无需人工参与，节约了我们大量的时间和精力。

​	此外,JavaEE 开发中需要使用到的 jar 包种类繁多，几乎每个 jar 包在其本身的官网上的获取方式都不尽相同。为了查找一个 jar 包找遍互联网，身心俱疲，没有经历过的人或许体会不到这种折磨。不仅如此，费劲心血找的 jar 包里有的时候并没有你需要的那个类，又或者又同名的类没有你要的方法——以不规范的方式获取的 jar 包也往往是不规范的。

​	使用 Maven 我们可以享受到一个完全统一规范的 jar 包管理体系。你只需要在你的项目中以坐标的方式依赖一个 jar 包，Maven 就会自动从中央仓库进行下载，并同时下载这个 jar 包所依赖的其他 jar 包。

### 项目构建

​	在实际项目中整合第三方框架，Web 工程中除了 Java 程序和 JSP 页面、图片等静态资源之外，还包括第三方框架的 jar 包以及各种各样的配置文件。所有这些资源都必须按照正确的目录结构部署到服务器上，项目才可以运行。

​	所以综上所述：构建就是以我们编写的 Java 代码、框架配置文件、国际化等其他资源文件、JSP 页面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。

​	那么项目构建的全过程中都包含哪些环节呢？

​	①**清理**：删除以前的编译结果，为重新编译做好准备。

​	②**编译**：将 Java 源程序编译为字节码文件。

​	③**测试**：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。

​	④**报告**：在每一次测试后以标准的格式记录和展示测试结果。

​	⑤**打包**：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java 工程对应 jar 包，Web工程对应 war 包。

​	⑥**安装**：在 Maven 环境下特指将打包的结果——jar 包或 war 包安装到本地仓库中。

​	⑦**部署**：将打包的结果部署到远程仓库或将 war 包部署到服务器上运行。

​	大家看看，项目的构建可绝不仅仅是编译软件这件事情。除了写代码，在项目层面做的大部分工作，都包含在构建的过程中。有了Maven，构建中的这些过程都能够进行良好的定义（模式、固化、共识，记住这些关键词哪），而且Maven能够帮我们串起来形成一个自动构建过程，这样比我们手动执行要高效得多。

## Maven核心概念

​	Maven 能够实现自动化构建是和它的内部原理分不开的，这里我们从 Maven 的九个核心概念入手，

​	看看 Maven 是如何实现自动化构建的

​		①POM

​		②约定的目录结构

​		③坐标

​		④依赖管理

​		⑤仓库管理

​		⑥生命周期

​		⑦插件和目标

​		⑧继承

​		⑨聚合

### Maven 的核心程序

​	Maven 的**核心程序**中仅仅定义了抽象的生命周期，而具体的操作则是由 Maven 的**插件**来完成的。可是Maven 的插件并不包含在 Maven 的核心程序中，在首次使用时需要联网下载。

​	下载得到的插件会被保存到本地仓库中。本地仓库默认的位置是：~\.m2\repository。

### 核心概念

#### POM

​	Project Object Model：项目对象模型。将 Java **工程**的相关信息封装为**对象**作为便于操作和管理的**模型**。Maven 工程的核心配置。可以说学习 Maven 就是学习 pom.xml 文件中的配置。

#### 坐标

​	使用如下三个向量在 Maven 的仓库中唯一的确定一个 Maven 工程。

​	[1]groupid：公司或组织的域名倒序+当前项目名称

​	[2]artifactId：当前项目的模块名称

​	[3]version：当前模块的版本

​	使用如下三个向量在 Maven 的仓库中唯一的确定一个 Maven 工程。

```xml
<groupId>com.atguigu.maven</groupId>

<artifactId>Hello</artifactId>

<version>0.0.1-SNAPSHOT</version>
```

如何通过坐标到仓库中查找 jar 包？

​	[1]将 gav 三个向量连起来

​	com.atguigu.maven+Hello+0.0.1-SNAPSHOT

​	[2]以连起来的字符串作为目录结构到仓库中查找

​	com/atguigu/maven/Hello/0.0.1-SNAPSHOT/Hello-0.0.1-SNAPSHOT.jar

​	※**注意**：我们自己的 Maven 工程必须执行安装操作才会进入仓库。安装的命令是：mvn install

#### 依赖

​	Maven 中最关键的部分，我们使用 Maven 最主要的就是使用它的依赖管理功能。要理解和掌握 Maven的依赖管理，我们只需要解决一下几个问题：

​	①依赖的目的是什么

​	当 A jar 包用到了 B jar 包中的某些类时，A 就对 B 产生了依赖，这是概念上的描述。那么如何在项目中以依赖的方式引入一个我们需要的 jar 包呢？

​	答案非常简单，就是使用 dependency 标签指定被依赖 jar 包的坐标就可以了。

```xaml
<dependency>
		<groupId>com.atguigu.maven</groupId>
		<artifactId>Hello</artifactId>
		<version>0.0.1-SNAPSHOT</version>
		<scope>compile</scope>
</dependency>
```

##### 依赖的范围

大家注意到上面的依赖信息中除 了目标jar包的坐标还有一个scope设置,这是依赖的范围.依赖的范围有几个可选值,常用的是:compile  test provide三个	

​	compile: 编译依赖范围。如果没有指定，就会默认使用该依赖范围。使用此依赖范围的maven依赖，对于编译 测试 运行三种的classpath都有效。

​	test：测试依赖范围。使用此依赖范围的Maven依赖，只对于测试的classpath有效，在编译主代码或者运行主代码的时候都无法依赖此类依赖。典型的例子是jUnit,它只有在编译测试代码及运行测试代码的时候才有效。

​	provided:以提供依赖范围。使用此依赖范围的maven依赖，对于编译和测试classpath有效，但在运行时无效。典型的例子是servlet-api，编译和测试项目的时候需要该依赖，但在运行的时候，由于容器已经提供，就不需要maven重复地引入一遍。打包的时候可以不用包进去，别的设施会提供。事实上该依赖理论上可以参与编译，测试，运行等周期。相当于compile，但是打包阶段做了exclude操作

##### 依赖的排除

```xml
<executions>  
	<execution>  
		<groupId> </groupId> 
		<artifactId></artifactId>
	</execution>  
</executions>
```

##### 统一管理所依赖jar包的版本

[1] 统一声明版本号

```xml
<properties> 
	<atguigu.spring.version>4.1.1.RELEASE</atguigu.spring.version>
</properties>
```

其中 atguigu.spring.version 部分是自定义标签。

 [2]引用前面声明的版本号

```xml
<dependencies>
  	<dependency>
		<groupId>org.springframework</groupId> 
		<artifactId>spring-core</artifactId> 
		<version>${atguigu.spring.version}</version>
	</dependency> 
</dependencies>
```

## 仓库

### 分类

[1]本地仓库:为当前本机电脑上的所有 Maven 工程服务。 

[2]远程仓库
	(1)私服:架设在当前局域网环境下，为当前局域网范围内的所有 Maven 工程服务。

![Maven服务器](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/mavensifu.png)

(2)中央仓库：架设在 Internet 上，为全世界所有 Maven 工程服务。

(3)中央仓库的镜像：架设在各个大洲，为中央仓库分担流量。减轻中央仓库的压力，同时更快的响应用户请求。

## 生命周期

### 什么是 Maven 的生命周期？

Maven 生命周期定义了各个构建环节的执行顺序，有了这个清单，Maven 就可以自动化的执行构建命令了。

Maven 有三套相互独立的生命周期，分别是：

①Clean Lifecycle 在进行真正的构建之前进行一些清理工作。

②Default Lifecycle 构建的核心部分，编译，测试，打包，安装，部署等等。

③Site Lifecycle 生成项目报告，站点，发布站点。它们是相互独立的，你可以仅仅调用 clean 来清理工作目录，仅仅调用 site 来生成站点。当然你也可以直接运行 mvn clean install site 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。

### Clean 生命周期

Clean 生命周期一共包含了三个阶段：

①pre-clean 执行一些需要在 clean 之前完成的工作

②clean 移除所有上一次构建生成的文件

③post-clean 执行一些需要在 clean 之后立刻完成的工作

### Site 生命周期

①pre-site 执行一些需要在生成站点文档之前完成的工作

②site 生成项目的站点文档

③post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备

④site-deploy 将生成的站点文档部署到特定的服务器上

这里经常用到的是 site 阶段和 site-deploy 阶段，用以生成和发布 Maven 站点，这可是 Maven 相当强大的功能，Manager 比较喜欢，文档及统计数据自动生成，很好看。

### Default 生命周期

Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段：

validate

generate-sources

process-sources

generate-resources

process-resources 复制并处理资源文件，至目标目录，准备打包。

compile 编译项目的源代码。

process-classes

generate-test-sources

process-test-sources

generate-test-resources

process-test-resources 复制并处理资源文件，至目标测试目录。

test-compile 编译测试源代码。

process-test-classes

test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。

prepare-package

package 接受编译好的代码，打包成可发布的格式，如 JAR。

pre-integration-test

integration-test

post-integration-test

verify

install 将包安装至本地仓库，以让其它项目依赖。

deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

### 生命周期与自动化构建

**运行任何一个阶段的时候，它前面的所有阶段都会被运行**，例如我们运行 mvn install 的时候，代码会被编译，测试，打包。这就是 Maven 为什么能够自动执行构建过程的各个环节的原因。此外，Maven 的插件机制是完全依赖 Maven 的生命周期的，因此理解生命周期至关重要。

## 插件和目标

● Maven 的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的。

● 每个插件都能实现多个功能，每个功能就是一个插件目标。

● Maven 的生命周期与插件目标相互绑定，以完成某个具体的构建任务。

例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。

## 继承

Maven继承的特性则能够帮助抽取各模块之前相同依赖和插件配置，在简化POM的同时还能存在各个模块配置的一致性。对于规范项目开发，避免可能存在的版本不一致的问题，有良好的预防作用。

## 聚合

### 为什么要使用聚合？

将多个工程拆分为模块后，需要手动逐个安装到仓库后依赖才能够生效。修改源码后也需要逐个手动进行 clean 操作。而使用了聚合之后就可以批量进行 Maven 工程的安装、清理工作。

### 如何配置聚合?

在总的聚合工程中使用 modules/module 标签组合，指定模块工程的相对路径即可 

```xml
<modules> 
	<module>../Hello</module> 
	<module>../HelloFriend</module> 
	<module>../MakeFriends</module>
</modules>
```


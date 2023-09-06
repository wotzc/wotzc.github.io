---
title: Maven 构建生命周期
date: 2023-09-05 19:34:57
tags:
- Maven
- SpringBoot
categories: JavaWeb
---

# 基础知识

Maven 基于构建生命周期的中心概念。 这意味着构建和分发特定项的过程是明确定义的。

对于构建项目的人来说，这意味着只需要学习一小部分命令即可构建任何 Maven 项目，POM 将确保他们获得所需的结果。

共有三个内置构建生命周期：<code>default</code>, <code>clean</code> and  <code>site</code>。 <code>default</code>生命周期处理项目部署，<code>clean</code>生命周期处理项目清理，而 <code>site</code>生命周期处理项目网站的创建。

# 构建生命周期由多个阶段组成

每个构建生命周期都由不同的构建阶段列表定义，其中一个构建阶段代表生命周期中的一个阶段。

例如，默认生命周期包括以下阶段（有关生命周期阶段的完整列表，请参阅生命周期参考）：

- <code>validate</code>- 验证项目是否正确并且所有必要的信息均可用
- <code>compile</code>- 编译项目的源代码，编译后的类被放置在 中<code>${basedir}/target/classes</code>
- <code>test</code>- 使用合适的单元测试框架测试编译后的源代码。这些测试不应要求打包或部署代码
- <code>package</code>- 将编译后的代码打包为可发布的格式，如 JAR
- <code>verify</code>- 对集成测试的结果进行检查，确保符合质量标准
- <code>install</code>- 将软件包（例如JAR包）安装到本地存储库（本地Repository）中，作为本地其他项目的依赖项使用
- <code>deploy</code>- 将最终包复制到远程存储库以与其他开发人员和项目共享

这些生命周期阶段**按顺序执行**以完成默认生命周期。 考虑到上面的生命周期阶段，这意味着当使用默认生命周期时，Maven 将首先验证项目，然后尝试编译源代码，针对测试运行这些源代码，打包二进制文件（例如 jar），针对该项目运行集成测试 包，验证集成测试，将验证的包安装到本地存储库，然后将安装的包部署到远程存储库。

-----------

# 命令行调用

您应该选择与您的结果相匹配的阶段。 如果你想要你的<code>jar</code>，运行<code>package</code>。如果您想运行单元测试，请运行<code>test</code>。

如果您不确定自己想要什么，首选调用的阶段是:

```bash
mvn verify
```

在执行<code>verify</code>之前，该命令会依次执行每个默认生命周期阶段（<code>validate</code>, <code>compile</code>, <code>package</code>等）。在大多数情况下，效果与打包相同。不过，如果有集成测试，这些测试也会被执行。在验证阶段，还可以进行一些额外的检查，例如，如果代码是按照预定义的检查样式规则编写的。

在构建环境中，使用下面的调用即可将工程干净利落地构建并部署到共享存储库中。

```
mvn clean deploy
```

同一命令可用于多模块场景（即具有一个或多个子项目的项目）。 Maven遍历每个子项目并执行<code>clean</code>，然后执行<code>deploy</code>（包括之前的所有构建阶段步骤）。

# 构建阶段由插件目标组成

不过，尽管构建阶段负责构建生命周期中的特定步骤，但其履行职责的方式可能会有所不同。这可以通过声明与这些构建阶段绑定的插件目标来实现。

一个插件目标代表一个特定的任务（比构建阶段更细），有助于项目的构建和管理。它可以与零个或多个构建阶段绑定。未与任何构建阶段绑定的目标可通过直接调用在构建生命周期之外执行。执行顺序取决于目标和构建阶段的调用顺序。

例如，考虑下面的命令。<code>clean</code>和<code>package</code>是构建阶段，而<code>dependency:copy-dependencies</code>是（插件的）目标。

```
mvn clean dependency:copy-dependencies package
```

如果要执行此操作，则将首先执行<code>clean</code>阶段（这意味着它将运行<code>clean</code>生命周期的所有前置阶段，加上<code>clean</code>阶段本身），然后是<code>dependency:copy-dependencies</code>目标，最后执行<code>package</code>阶段（以及<code>package</code>默认生命周期的所有先前构建阶段）。

此外，如果一个目标与一个或多个构建阶段绑定，那么该目标将在所有这些阶段中被调用。

# 设置项目以使用构建生命周期

构建生命周期简单易用，但在为项目构建 Maven 构建时，如何为每个构建阶段分配任务？

## Packaging-打包

第一种，也是最常见的一种方法，是通过POM 元素<code>\<packaging></code>为项目设置打包。一些有效的<code>\<packaging></code>值包括 <code>jar</code>、<code>war</code>、<code>ear</code>和<code>pom</code>。如果没有指定包装值，则默认为<code>jar</code>。

每个<code>packaging</code>都包含一个目标列表，用于绑定到特定阶段。例如，<code>jar</code> <code>packaging</code>将把以下目标绑定到默认生命周期的构建阶段。

| 阶段                                | jar  packagin的插件目标              |
| ----------------------------------- | ------------------------------------ |
| <code>process-resources</code>      | <code>resources:resources</code>     |
| <code>compile</code>                | <code>compiler:compile</code>        |
| <code>process-test-resources</code> | <code>resources:testResources</code> |
| <code>test-compile</code>           | <code>compiler:testCompile</code>    |
| <code>test</code>                   | <code>surefire:test</code>           |
| <code>package</code>                | <code>jar:jar</code>                 |
| <code>install</code>                | <code>install:install</code>         |
| <code>deploy</code>                 | <code>deploy:deploy</code>           |

这几乎是一套标准的绑定。

## Plugins-插件

将目标添加到生命周期阶段的第二种方法是在项目中配置插件。插件是为 Maven 提供目标的工程。此外，一个插件可能有一个或多个目标，其中每个目标代表该插件的一种能力。例如，编译器插件有两个目标：<code>compile</code>和<code>testCompile</code>。前者编译你的主代码的源代码，而后者编译你的测试代码的源代码。

在后面的章节中您将看到，插件可以包含指示将目标绑定到哪个生命周期阶段的信息。请注意，仅添加插件本身的信息是不够的，还必须指定要在构建过程中运行的目标。

配置的目标将添加到已绑定到所选包的生命周期的目标中。 如果多个目标绑定到特定阶段，则使用的顺序是首先执行打包中的目标，然后执行 POM 中配置的目标。 请注意，您可以使用<code>\<executions></code>元素来更好地控制特定目标的顺序。

为阶段添加目标的第二种方法是在项目中配置插件。插件是向 Maven 提供目标的工件。此外，一个插件可能有一个或多个目标，其中每个目标代表该插件的一种能力。例如，编译器插件有两个目标：<code>compile</code>和<code>testCompile</code>。前者编译主代码的源代码，后者编译测试代码的源代码。

在后面的章节中您将看到，插件可以包含指示将目标绑定到哪个生命周期阶段的信息。请注意，仅添加插件本身的信息是不够的，还必须指定要在构建过程中运行的目标。

配置的目标将添加到已绑定到所选包装生命周期的目标中。如果一个特定阶段绑定了多个目标，则使用的顺序是先执行包装中的目标，然后再执行 POM 中配置的目标。请注意，您可以使用<code>\<executions></code>元素对特定目标的顺序进行更多控制。

例如，<code>Modello</code>插件默认将其目标<code>modello:java</code>绑定到<code>generate-sources</code>阶段（注：<code>modello:java</code>目标生成 Java 源代码）。因此，要使用 Modello 插件并让它从模型生成源代码并将其纳入构建，您需要在<code>\<build></code>的<code>\<plugins></code>部分的 POM 中添加以下内容：

```xml
<plugin>
   <groupId>org.codehaus.modello</groupId>
   <artifactId>modello-maven-plugin</artifactId>
   <version>1.8.1</version>
   <executions>
     <execution>
       <configuration>
         <models>
           <model>src/main/mdo/maven.mdo</model>
         </models>
         <version>4.0.0</version>
       </configuration>
       <goals>
         <goal>java</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
```

您可能想知道为什么<code>\<executions></code>元素在那里。 这样您就可以根据需要使用不同的配置多次运行相同的目标。还可以根据一个给定 ID单独的执行，以便在继承或应用配置文件期间，您可以控制是否合并目标配置或将其转变为附加执行。

当多个执行与特定阶段相匹配时，它们将按照 POM 中指定的顺序执行，先执行继承的执行。

现在，就<code>modello:java</code>而言，只有在生成源代码阶段才有意义。但有些目标可以在多个阶段中使用，因此可能没有合理的默认值。对于这些目标，你可以自己指定阶段。例如，假设有一个目标<code>display:time</code>，它能在命令行中回声显示当前时间，你希望它在<code>process-test-resources</code>阶段运行，以显示测试何时开始。可以这样配置:

```xml
<plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>process-test-resources</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
```


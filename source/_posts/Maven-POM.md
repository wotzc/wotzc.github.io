---
title: Maven POM
date: 2023-08-25 15:17:22
tags:
- Maven
- POM
- SpringBoot
categories: JavaWeb
---

# What is the POM?

POM 代表“项目对象模型”，即"Project Object Model"。它是 Maven 项目的 XML 表示形式，保存在名为<code> pom.xml </code>的文件中。当 Maven 人员在场时，谈论项目是在哲学意义上谈论项目，而不仅仅是包含代码的文件集合。一个项目包含配置文件、相关开发人员及其角色、缺陷、跟踪系统、组织和许可证、项目所在的 URL、项目的依赖关系，以及赋予代码生命的所有其他小部件。它是与项目有关的所有事项的一站式商店。事实上，在 Maven 的世界里，一个项目根本不需要包含任何代码，只需要一个<code> pom.xml </code>。

# Quick Overview

这是 `POM` 的项目元素下的直接元素列表。请注意，<code> modelVersion  </code>的版本是 <code>  4.0.0 </code>，这是目前唯一支持的 `POM` 版本，而且始终是必需的。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <!-- The Basics -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>
 
  <!-- Build Settings -->
  <build>...</build>
  <reporting>...</reporting>
 
  <!-- More Project Information -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>
 
  <!-- Environment Settings -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```

# POM文件基础信息

POM 包含项目的所有必要信息，以及构建过程中要使用的插件配置。它是 "who", "what",  "where"的声明式表现，而构建生命周期是 "when" and "how"。这并不是说 POM 不能影响应用生命周期的流程--它可以。例如，通过配置<code>  maven-antrun-plugin </code>，就可以在 POM 中嵌入 Apache Ant 任务。然而，这最终只是一个声明。 build.xml 准确地告诉 Ant 在运行时要做什么（过程式），而 POM 则说明其配置（声明式）。如果某种外力导致生命周期跳过 Ant 插件的执行，也不会阻止已执行的插件发挥其魔力。这与 build.xml 文件不同，在 build.xml 文件中，任务几乎总是依赖于在它之前执行的行。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0</version>
</project>
```

# Maven 坐标

上面定义的 POM 是 Maven 允许的最低限度。<code> groupId:artifactId:version </code>都是必填字段（不过，如果 groupId 和 version 是继承自父版本，则无需明确定义，有关继承的内容稍后详述）。这三个字段的作用很像地址和时间戳。这标记了版本库中的一个特定位置，就像 Maven 项目的坐标系：

- **groupId**：这通常在一个组织或项目中是唯一的。例如，所有核心 Maven 工程都（应该）使用的 `groupId` 是<code> org.apache.maven </code>。`groupId`不一定使用点符号，例如 junit 项目。请注意，点标记的 groupId 不必与项目包含的包结构相对应。 但是，这是一个值得遵循的好习惯。当存储在存储库中时，该组的行为与操作系统中的 Java 打包结构非常相似。 这些点被操作系统特定的目录分隔符（例如 Unix 中的“/”）替换，成为基础存储库的相对目录结构。 在给出的示例中，<code> org.codehaus.mojo </code>组位于目录<code> $M2_REPO/org/codehaus/mojo </code>中。
- **artifactId**：`artifactId` 通常是项目的名称。虽然 `groupId` 很重要，但小组内的人在讨论中很少会提到 groupId（他们通常都是同一个 ID，例如 MojoHaus 项目的 groupId：<code> org.codehaus.mojo）</code>。它与 `groupId` 一起创建了一个key，该key将此项目与世界上其他所有项目区分开来（至少应该如此：）。`artifactId` 配合 `groupId` ， 完全定义了工程在版本库中的唯一地址。在上述项目中，<code>my-project</code>位于 <code>$M2_REPO/org/codehaus/mojo/my-project</code>。
- **version**: groupId:artifactId 表示的是一个项目，但它们无法确定我们谈论的是该项目的哪个版本。我们想要 2018 年（4.12 版）的 junit:junit，还是 2007 年（3.8.2 版）的 junit:junit？简而言之：代码发生了变化，这些变化就应该进行版本化，而这个元素可以使这些版本保持一致。my-project 1.0 版本的文件存放在 $M2_REPO/org/codehaus/mojo/my-project/1.0 的目录结构中。

# Packaging-打包

现在，我们有了<code> groupId:artifactId:version <code>的地址结构，还需要一个标准标签来提供真正完整的内容：那就是项目的包装。在我们的例子中，上面定义的<code> org.codehaus.mojo:my-project:1.0<code> 的 POM 示例将打包为 <code>jar</code>。我们可以通过声明不同的打包方式将其变为 <code>war</code>：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <packaging>war</packaging>
  ...
</project>
```

如果没有声明<code>packaging</code>，Maven 默认的<code>packaging</code>为 jar。当前的核心打包方式有：<code>pom</code>、<code>jar</code>、<code>maven-plugin</code>、<code>ejb</code>、<code>war</code>、<code>ear</code>、<code>rar</code>。

# POM Relationships

## Dependencies

POM 的基石是其依赖列表。大多数项目都需要依赖其他项目才能正确构建和运行。如果 Maven 为你所做的一切就是管理这个列表，那么你已经收获很多。Maven 下载并链接编译以及需要它们的其他目标的依赖项。作为额外的好处，Maven 引入了这些依赖项的依赖项（传递依赖项），让您的列表只关注项目所需的依赖项。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <type>jar</type>
      <scope>test</scope>
      <optional>true</optional>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

- **groupId**, **artifactId**, **version**:

  您会经常看到这些元素。 这个三位一体用于及时计算特定项目的 Maven 坐标，将其划分为该项目的依赖项。 此计算的目的是选择与所有依赖项声明匹配的版本（由于传递依赖项，同一工程可能有多个依赖项声明）。

- **type**:

  对应于所选的依赖关系类型。 默认为<code> jar</code>。虽然它通常表示依赖项文件名的扩展名，但情况并非总是如此：类型可以映射到不同的扩展名和分类器。<code>type<code>通常与所使用的<code>packaging<code>相对应，但也并非总是如此。

- **scope**:

  该元素指的是当前任务（编译和运行时、测试等）的类路径，以及如何限制依赖关系的传递性。有五种作用域可供选择：

  - <code>compile</code> - 这是<code>scope</code>，在未指定任何作用域时使用。编译依赖项在所有类路径中都可用。 此外，这些依赖关系会传播到依赖项目。
  - <code>provided</code> - 这与`compile`很相似，但表示您希望 JDK 或容器在运行时提供它。它只能在编译和测试的`classpath` 上使用，而且不具有传递性。
  - <code>runtime</code> - 此作用域表示编译时不需要该依赖关系，但执行时需要。它在运行时和测试类路径中，但不在编译类路径中。
  - <code>test</code> - 此范围表示应用程序的正常使用不需要依赖项，并且仅适用于测试编译和执行阶段。 它不具有传递性。
  - <code>system</code> - 此范围与提供的`prvoide`类似，只是您必须显式提供包含它的 `JAR`。 该工件始终可用，无需在资源库中查找。

- **systemPath**:

  仅在 <code>\<scope></code>范围为<code>system</code>时才使用。否则，如果设置了该元素，编译将失败。路径必须是绝对路径，因此建议使用属性指定特定机器的路径，如<code> ${java.home}/lib</code>。它会假定系统依赖作用域具有优先权，因此 Maven 不会检查项目的远程资源库，而是检查系统路径该文件是否存在。如果不存在，Maven 会导致构建失败，并建议您手动下载和安装。

- **optional**:

  当项目本身是依赖项时，标记为<code>optional</code>。例如，假设项目<code>A</code>需要依赖项目<code>B</code>来编译一部分代码，而这部分代码在运行时可能用不到，那么我们的所有项目可能都不需要项目<code>B</code>。因此，如果项目 <code>X</code>将项目<code>A</code>作为自己的依赖项，那么 Maven 就根本不需要安装项目<code>B</code>。如果<code>=></code>表示必须依赖，而<code>--></code>表示可选，那么在构建<code>A</code>时会出现<code>A=>B</code>的情况，而构建<code>X</code>时可能会出现<code>X=>A-->B</code>的情况。

  简而言之，<code>optional</code>让其他项目知道，当您使用此项目时，不需要此依赖项即可正常工作。

## 依赖关系版本要求规范

依赖项的版本元素定义版本要求，用于计算依赖项版本。软性要求可以被依赖关系图中其他地方的同一工程的不同版本所替代。硬性要求规定了特定的一个或多个版本，并覆盖软性要求。如果没有任何依赖项版本可以满足该工程的所有硬要求，则构建会失败。

版本要求的语法如下：

- <code>1.0</code>: 1.0 的软要求。 如果依赖关系树中较早没有出现其他版本，则使用 1.0。
- <code>[1.0]</code>：1.0 的硬性要求。 使用 1.0 并且仅使用 1.0。
- <code>(,1.0]</code>: 任何 <= 1.0 版本的硬性要求。
- <code>[1.2,1.3]</code>: 1.2 和 1.3（含）之间任何版本的硬性要求。
- <code>[1.0,2.0)</code>: 1.0 <= x < 2.0；硬性要求在 1.0（含）和 2.0（不含）之间的任何版本。
- <code>[1.5,)</code>: 任何大于或等于 1.5 版本的硬性要求。
- `(,1.0],[1.2,)`: 小于或等于 1.0 或大于或等于 1.2 的任何版本的硬性要求，但不包括 1.1。多个要求之间用逗号分隔。
- <code>(,1.1),(1.1,)</code>: 除 1.1 之外的任何版本的硬性需求；例如，因为 1.1 存在关键漏洞。
  Maven 挑选每个项目中满足该项目依赖项所有硬性要求的最高版本。如果没有任何版本满足所有硬性要求，则构建失败。

## 版本顺序规范

如果版本字符串在语法上是正确的语义版本控制 1.0.0 版本号，则在几乎所有情况下版本比较都遵循该规范中概述的优先规则。 这些版本是常见的字母数字 ASCII 字符串，例如 2.15.2-alpha。 更准确地说，如果要比较的两个版本号都与语义版本控制规范中的 BNF 语法中的“有效 semver”产生式相匹配，则情况成立。 Maven 不考虑该规范隐含的任何语义。

重要：这只适用于语义版本控制1.0.0。 Maven的版本顺序算法与语义版本控制2.0.0不兼容。 特别是，Maven不对加号进行特殊处理，也不考虑构建标识符。

当版本字符串不遵循语义版本控制时，需要一组更复杂的规则。 Maven 坐标被分割为点（“.”）、连字符（“-”）以及数字和字符之间的转换之间的标记。 分隔符被记录并将对顺序产生影响。 数字和字符之间的转换相当于连字符。 空标记将替换为“0”。 这给出了一系列版本号（数字标记）和带有“.”的版本限定符（非数字标记）。 或“-”前缀。

拆分和替换示例：

- <code>1-1.foo-bar1baz-.1</code> -> <code>1-1.foo-bar-1-baz-0.1</code>

然后，从版本末尾开始，修剪尾随的“null”值（0、“”、“final”、“ga”）。 从头到尾对每个剩余的连字符重复此过程。

修剪示例：

- <code>1.0.0</code> -> <code>1</code>
- <code>1.g</code> -> <code>1</code>
- <code>1.fina</code> -> <code>1</code>
- <code>1.0</code> -> <code>1</code>
- <code>1.</code> -> <code>1</code>
- <code>1-</code> -> <code>1</code>
- <code>1.0.0-foo.0.0</code> -> <code>1-foo</code>
- <code>1.0.0-0.0.0</code> -> <code>1</code>

版本顺序是这一前缀标记序列的字典顺序，较短的标记填充有足够的“空”值和匹配的前缀，以与较长的标记具有相同的长度。 填充的“null”值取决于其他版本的前缀：0 表示“.”，“”表示“-”。 前缀令牌顺序为：

如果前缀相同，则比较 token：

- 数字标记具有自然顺序。
- 非数字标记（“限定符”）按字母顺序排列，但以下标记除外（按此顺序排在第一位）
  - "`alpha`" < "`beta`" < "`milestone`" < "`rc`" = "`cr`" < "`snapshot`" < "" = "`final`" = "`ga`" < "`sp`"
  - 当直接跟数字时，“alpha”、“beta”和“milestone”限定符可以分别缩写为“a”、“b”和“m”。
    否则“.qualifier”=“-qualifier”<“-number”<“.number”
  - alpha = a <<\<beta>> = b <<\<milestone>> = m <<\<rc>> = cr <<\<snapshot>> '<<<>>' = final = ga = release < sp

鼓励遵守 semver 规则，不鼓励使用某些限定词：

- 与<code>ea</code> and <code>preview</code>相比，更倾向使用<code>alpha</code>, <code>beta</code> 和 <code>milestone</code>
- 优先选择 <code>1.0.0-RC1</code>，而不是 <code>1.0.0.0.RC1</code>
- 不鼓励使用 <code>CR</code>修饰符。请使用<code>RC</code>
- 不鼓励使用 <code>final</code>、<code>ga</code>和 <code>release</code>限定符。请勿使用限定符。
- 不鼓励使用<code>SP</code>限定符。 而是增加补丁版本。

## Exclusions-排除项

限制依赖项的传递依赖关系有时很有用。依赖项可能指定了不正确的作用域，或与项目中的其他依赖项发生冲突。<code>Exclusions</code>会告诉 Maven 不要在类路径中包含指定的<code>artifact</code>，即使该<code>artifact</code>是本项目一个或多个依赖项的依赖项（传递依赖项）。例如，<code>maven-embedder</code>依赖于<code>maven-core</code>。假设您想依赖<code>maven-embedder</code>，但不想在 classpath 中包含<code>maven-core</code>或其依赖项。那么就在声明依赖<code>maven-embedder</code>的元素中添加<code>maven-core</code>作为排除项：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>3.9.4</version>
      <exclusions>
        <exclusion>
          <groupId>org.apache.maven</groupId>
          <artifactId>maven-core</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

这只会从<code>maven-embedder</code>依赖项中删除到<code>maven-core</code>的路径。 如果<code>maven-core</code>在 POM 中的其他位置显示为直接或传递依赖项，它仍然可以添加到类路径中。

通配符排除可以轻松排除依赖项的所有传递依赖项。 在下面的情况下，您可能正在使用 maven-embedder 并且想要管理您使用的依赖项，因此您排除所有传递依赖项：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>3.8.6</version>
      <exclusions>
        <exclusion>
          <groupId>*</groupId>
          <artifactId>*</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

- **exclusions**： Exclusions包含一个或多个<code>\<exclusion></code>，每个元素都包含表示要排除的依赖关系的<code>groupId</code>和<code>artifactId</code>。与<code>optional</code>不同的是，<code>exclusions</code>会主动将<code>artifacts</code>从依赖关系树中移除，而<code>optional</code>可能会也可能不会被安装和使用。

## Inheritance-继承

Maven 为构建管理带来的一个强大功能是项目继承概念。虽然在 Ant 等构建系统中可以模拟继承，但 Maven 在项目对象模型中明确了项目继承。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
</project>
```

父项目和聚合（多模块）项目的<code>packaging</code>类型需要为<code>pom</code>。这些类型定义了与一组生命周期阶段绑定的目标。例如，如果打包类型为<code>jar</code>，那么打包阶段将执行<code>jar:jar</code>目标。现在，我们可以为父 POM 添加值，这些值将由其子 POM 继承。父 POM 中的大多数元素都会被其子 POM 继承，其中包括:

- groupId
- version
- description
- url
- inceptionYear
- organization
- licenses
- developers
- contributors
- mailingLists
- scm
- issueManagement
- ciManagement
- properties
- dependencyManagement
- dependencies
- repositories
- pluginRepositories
- build
  - plugin executions with matching ids
  - plugin configuration
  - etc.
- reporting
- profiles

<code>不继承的重要元素包括:</code>

- artifactId
- name
- prerequisites

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>my-parent</artifactId>
    <version>2.0</version>
    <relativePath>../my-parent</relativePath>
  </parent>
 
  <artifactId>my-project</artifactId>
</project>
```

注意<code>relativePath</code>元素。它不是必需的，但可以用来指示 Maven 在搜索本地和远程版本库之前，首先搜索该项目父节点的路径。

要查看继承的实际效果，只需查看 ASF 或 Maven 父 POM 即可。

## The Super POM

与面向对象编程中的对象继承类似，扩展父 POM 的 POM 会从该父 POM 继承某些值。 此外，正如 Java 对象最终继承自 <code>java.lang.Object</code>一样，所有项目对象模型都继承自基础 Super POM。 下面的代码片段是 Maven 3.5.4 的 Super POM。

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>https://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
 
  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Central Repository</name>
      <url>https://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
  </pluginRepositories>
 
  <build>
    <directory>${project.basedir}/target</directory>
    <outputDirectory>${project.build.directory}/classes</outputDirectory>
    <finalName>${project.artifactId}-${project.version}</finalName>
    <testOutputDirectory>${project.build.directory}/test-classes</testOutputDirectory>
    <sourceDirectory>${project.basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>${project.basedir}/src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${project.basedir}/src/test/java</testSourceDirectory>
    <resources>
      <resource>
        <directory>${project.basedir}/src/main/resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>${project.basedir}/src/test/resources</directory>
      </testResource>
    </testResources>
    <pluginManagement>
      <!-- NOTE: These plugins will be removed from future versions of the super POM -->
      <!-- They are kept for the moment as they are very unlikely to conflict with lifecycle mappings (MNG-4453) -->
      <plugins>
        <plugin>
          <artifactId>maven-antrun-plugin</artifactId>
          <version>1.3</version>
        </plugin>
        <plugin>
          <artifactId>maven-assembly-plugin</artifactId>
          <version>2.2-beta-5</version>
        </plugin>
        <plugin>
          <artifactId>maven-dependency-plugin</artifactId>
          <version>2.8</version>
        </plugin>
        <plugin>
          <artifactId>maven-release-plugin</artifactId>
          <version>2.5.3</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
 
  <reporting>
    <outputDirectory>${project.build.directory}/site</outputDirectory>
  </reporting>
 
  <profiles>
    <!-- NOTE: The release profile will be removed from future versions of the super POM -->
    <profile>
      <id>release-profile</id>
 
      <activation>
        <property>
          <name>performRelease</name>
          <value>true</value>
        </property>
      </activation>
 
      <build>
        <plugins>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-source-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-sources</id>
                <goals>
                  <goal>jar-no-fork</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-javadoc-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-javadocs</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-deploy-plugin</artifactId>
            <configuration>
              <updateReleaseInfo>true</updateReleaseInfo>
            </configuration>
          </plugin>
        </plugins>
      </build>
    </profile>
  </profiles>
 
</project>
```

您可以创建一个最小的<code>pom.xml</code>，并在命令行中执行： <code>mvn help:effective-pom</code>来查看超级 POM 如何影响您的项目对象模型。

### Dependency Management-依赖管理

除了继承某些顶层元素外，父类还拥有为子 POM 和传递依赖关系配置值的元素。其中一个元素就是依赖关系管理<code>dependencyManagement</code>。

- **dependencyManagement**: POM 使用它来帮助管理其所有子项的依赖关系信息。如果<code>my-parent</code>项目使用<code>dependencyManagement</code>定义了对<code>junit:junit:4.12</code>的依赖项，则从该项目继承的 `POM` 设置其依赖项时，可以仅给出<code>groupId=junit</code>和<code>artifactId=junit</code>即可，Maven 将自动填写父依赖项设置的版本 。这种方法的好处显而易见。依赖关系的详细信息可以在一个中心位置设置，并传播到所有继承的 POM。

  需要注意的是，从传递依赖关系中合并进来的<code>artifacts</code>的版本和范围也受依赖关系管理部分中的版本规范控制。这可能会导致意想不到的后果。 考虑这样一种情况，您的项目使用两个依赖项：dep1 和 dep2。 dep2 反过来也使用 dep1，并且需要特定的最低版本才能运行。 如果您随后使用 dependencyManagement 指定旧版本，dep2 将被迫使用旧版本，并失败。 所以，你必须小心检查整个依赖树以避免这个问题； mvn dependency:tree 很有帮助。

------------

## 聚合多个模块

包含多个模块的项目称为多模块项目或聚合项目。模块是该 POM 列出的项目，并作为一个组执行。pom 打包项目可以通过将一组项目列为模块来聚合一组项目的构建，这些模块是这些项目的目录或 POM 文件的相对路径。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-project</module>
    <module>another-project</module>
    <module>third-project/pom-example.xml</module>
  </modules>
</project>
```

在列出模块时，您无需考虑模块间的依赖关系，即 POM 给出的模块顺序并不重要。Maven 会对模块进行拓扑排序，使依赖模块总是先于被依赖模块构建。

### 关于继承与聚合的最后一点说明

继承和聚合为通过单一的高级 POM 控制构建创造了良好的动态效果。你经常会看到既是父项目又是聚合项目的项目。例如，整个 Maven 核心通过单个基础 POM<code>org.apache.maven:maven</code>运行，因此构建 Maven 项目可以通过单个命令来执行：mvn compile。不过，聚合项目和父项目都是 POM 项目，它们并不相同，不能混为一谈。POM 项目可以从其聚合的任何模块继承，但不一定拥有这些模块。相反，一个 POM 项目可以聚合不从它继承的项目。

## Propertie-属性

属性是理解 POM 基础知识的最后一个必需部分。Maven 属性是值占位符，就像 Ant 中的属性一样。它们的值可以通过使用符号<code>${X}</code>在 POM 中的任何位置访问，其中<code>X</code>是属性。 或者它们可以被插件用作默认值，例如：

```xml
<project>
  ...
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
    <!-- Following project.-properties are reserved for Maven in will become elements in a future POM definition. -->
    <!-- Don't start your own properties properties with project. -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  </properties>
  ...
</project>
```

它们有五种不同的风格：

1. <code>env.X</code>：在变量前添加“env”前缀。 将返回 shell 的环境变量。 例如，${env.PATH} 包含 PATH 环境变量。注意：虽然 Windows 环境变量本身不区分大小写，但属性的查找却区分大小写。换句话说，Windows shell 会返回 %PATH% 和 %Path% 的相同值，而 Maven 则会区分 ${env.PATH} 和 ${env.Path}。为可靠起见，环境变量的名称统一为大写。
2. project.x：POM 中的点 (.) 表示的路径将包含相应元素的值。 例如：<code>\<project>\<version>1.0\</version>\</project></code> 可通过<code>${project.version}</code>访问。
3. settings.xml 中的点 (.) 符号路径将包含相应元素的值。例如 <code>\<settings>\<offline>false\</offline>\</settings></code>可通过<code>${settings.offline}</code>访问。
4. 所有通过 <code>java.lang.System.getProperties()</code>访问的属性都可作为 POM 属性使用，如<code>${java.home}</code>。
5. <code>x</code>: 设置在 POM 中的 <code>\<properties /></code>元素中。<code>\<properties>\<someVar>value\</someVar>\</properties></code>的值可用作<code>${someVar}</code>。

-------

# Build Settings

除了上面给出的 POM 基础知识之外，在声明 POM 的基本能力之前，还必须了解两个要素。它们分别是<code>build</code>元素和<code>reporting</code>元素，前者负责处理诸如声明项目目录结构和管理插件等事务，后者则在很大程度上反映了用于报告目的的构建元素。

## Build

根据 POM 4.0.0 XSD，<code>build</code>元素在概念上分为两部分：有一个<code>BaseBuild</code>类型，其中包含两个构建元素通用的元素集（项目下的顶级构建元素和配置文件下的构建元素） ，如下所述）； 还有<code>Build</code>类型，它包含<code>BaseBuild</code>集以及顶级定义的更多元素。 让我们首先分析两者之间的共同点。

注：这些不同的构建元素可分别称为 "项目构建 "和 "配置文件构建"。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <!-- "Project Build" contains more elements than just the BaseBuild set -->
  <build>...</build>
 
  <profiles>
    <profile>
      <!-- "Profile Build" contains a subset of "Project Build"s elements -->
      <build>...</build>
    </profile>
  </profiles>
</project>
```

### The BaseBuild Element Set

<code>BaseBuild</code>顾名思义：POM 中<code>\<build></code>与<code>\</build></code>之间的基本元素集。

```xml
<build>
  <defaultGoal>install</defaultGoal>
  <directory>${basedir}/target</directory>
  <finalName>${artifactId}-${version}</finalName>
  <filters>
    <filter>filters/filter1.properties</filter>
  </filters>
  ...
</build>
```

- **defaultGoal**:  如果没有给出，则执行的默认目标或阶段。如果给出了目标，则应按照命令行中的方式进行定义（例如<code>jar:jar</code>）。 如果定义了阶段（例如<code>install</code>），情况也是如此。
- **directory**: 这是项目构建后输出文件的目录，或者用 Maven 的话说，就是构建的目标。 它适当地默认为 <code>${basedir}/target</code>。
- **finalName**: 这是最终构建时项目的名称（不带文件扩展名，例如：<code>my-project-1.0.jar</code>）。 默认为 <code>${artifactId}-${version}</code>。不过，"finalName "这个术语有点名不副实，因为构建捆绑项目的插件完全有权忽略/修改这个名称（但它们通常不会这样做）。例如，如果<code>maven-jar-plugin</code>被配置为给 jar 提供一个 test 分类器，那么上面定义的实际 jar 将被构建为<code>my-project-1.0-test.jar</code>。
- **filter**: 定义 <code>*.properties</code>文件，其中包含一些列的属性值适用于资源文件列表。 换句话说，过滤器文件中定义的“<code>name=value</code>”对将替换构建时资源中的<code>${name}</code>字符串。上面的示例定义了<code>filters/</code>目录下的<code>filter1.properties</code>文件。 Maven的默认过滤器目录是<code>${basedir}/src/main/filters/</code>。

#### Resources

build元素的另一个功能是指定资源文件在项目中存在的位置。资源（通常）不是代码。 它们不会被编译，而是要捆绑在项目中或出于各种其他原因（例如代码生成）而使用的项目。

例如，Plexus 项目需要在 META-INF/plexus 目录中放置一个<code>configuration.xml</code>文件（该文件指定了容器的组件配置）。虽然我们可以很容易地将该文件放在<code>src/main/resources/META-INF/plexus</code>目录中，但我们还是想让 Plexus 拥有自己的<code>src/main/plexus</code>目录。为了让 JAR 插件正确地捆绑资源，您需要指定与下面类似的资源：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <resources>
      <resource>
        <targetPath>META-INF/plexus</targetPath>
        <filtering>false</filtering>
        <directory>${basedir}/src/main/plexus</directory>
        <includes>
          <include>configuration.xml</include>
        </includes>
        <excludes>
          <exclude>**/*.properties</exclude>
        </excludes>
      </resource>
    </resources>
    <testResources>
      ...
    </testResources>
    ...
  </build>
</project>
```

- **resources：**是资源元素的列表，每个元素都描述了与此项目相关的文件的包含内容和位置。
- **targetPath：**指定用于放置构建资源集的目录结构。目标路径默认为基本目录。对于将打包到 JAR 中的资源，通常指定的目标路径是 META-INF。
- **filtering**：<code>true</code>或<code>false</code>，表示是否对该资源启用过滤。请注意，不必定义过滤器<code>*.properties</code>文件即可进行过滤，资源还可以使用 POM 中默认定义的属性（例如 ${project.version}）。使用以下命令传递到命令行 “-D”标志（例如“-Dname=value”）或由属性元素显式定义。 过滤器文件已在上面介绍过。
- **directory：**该元素的值定义在哪里可以找到资源文件。 构建的默认目录是<code>${basedir}/src/main/resources</code>。
- **includes**: 包括：一组文件模式，指定要作为指定目录下的资源包含的文件，使用 * 作为通配符。
- **excludes**: 与<code>includes</code>相同的结构，指定要忽略哪些文件。 在<code>includes</code>和<code>excludes</code>发生冲突时，<code>excludes</code>获胜。
- **testResources**: 它们的定义与<code>resources</code>类似，但自然是在测试阶段使用。唯一的区别是项目的默认（Super POM 定义的）测试资源目录是<code>${basedir}/src/test/resources</code>。测试资源不会被部署。

#### Plugins

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.6</version>
        <extensions>false</extensions>
        <inherited>true</inherited>
        <configuration>
          <classifier>test</classifier>
        </configuration>
        <dependencies>...</dependencies>
        <executions>...</executions>
      </plugin>
    </plugins>
  </build>
</project>
```

除了<code>groupId:artifactId:version</code>的标准坐标外，还有一些元素用于配置插件或构建与插件的交互。

- **extensions:** <code>true</code>或<code>false</code>，是否加载该插件的扩展。 默认情况下为<code>false</code>。 本文档稍后将介绍扩展。
- **inherited**:<code>true</code>或<code>false</code>，表示此插件配置是否应用于继承自此插件的 POM。默认值为<code><true</code>。
- **configuration**: 这是单个插件的特定属性。无需太深入地了解插件如何工作的机制，只需说明插件 Mojo 可能期望的任何属性（这些是 Java Mojo bean 中的 getter 和 setter）都可以在这里指定。在上面示例中，我们在<code>maven-jar-plugin</code>中将<code>classifier</code>设置为<code>test</code>。值得注意的是，所有配置元素，无论它们位于 POM 中的什么位置，都是为了将值传递到另一个底层系统，例如插件。 换句话说：POM 模式永远不会明确要求配置元素中的值，但插件目标完全有权要求配置值。

如果您的 POM 声明了一个parent，它将从父类的<code>build/plugins</code>或<code>pluginManagement</code>部分继承插件配置。

**default configuration inheritance:**

为了说明这一点，请考虑父 POM 中的以下片段：

```xml
<plugin>
  <groupId>my.group</groupId>
  <artifactId>my-plugin</artifactId>
  <configuration>
    <items>
      <item>parent-1</item>
      <item>parent-2</item>
    </items>
    <properties>
      <parentKey>parent</parentKey>
    </properties>
  </configuration>
</plugin>
```

下面是使用该父级作为<code>parent</code>的项目中的插件配置：

```xml
<plugin>
  <groupId>my.group</groupId>
  <artifactId>my-plugin</artifactId>
  <configuration>
    <items>
      <item>child-1</item>
    </items>
    <properties>
      <childKey>child</childKey>
    </properties>
  </configuration>
</plugin>
```

默认行为是根据元素名称合并配置元素的内容。 如果子 POM 具有特定元素，则该值将成为有效值。 如果子 POM 没有元素，但父 POM 有，则父值将成为有效值。 请注意，这纯粹是对 XML 的操作； 不涉及插件本身的代码或配置。 仅涉及元素，而不涉及其值。

将这些规则应用到示例中，Maven 得出：

```xml
<plugin>
  <groupId>my.group</groupId>
  <artifactId>my-plugin</artifactId>
  <configuration>
    <items>
      <item>child-1</item>
    </items>
    <properties>
      <childKey>child</childKey>
      <parentKey>parent</parentKey>
    </properties>
  </configuration>
</plugin>
```

高级配置继承：<code>combine.child</code>和<code>combine.self</code>

你可以控制子POM如何从父POM继承配置通过向配置元素的子元素添加属性。这些属性是<code>combine.children</code>和 <code>combine.self</code>。在子 POM 中使用这些属性来控制 Maven 如何将父 POM 中的插件配置与子 POM 中的显式配置相结合。

以下是带有这两个属性说明的子配置：

```xml
<configuration>
  <items combine.children="append">
    <!-- combine.children="merge" is the default -->
    <item>child-1</item>
  </items>
  <properties combine.self="override">
    <!-- combine.self="merge" is the default -->
    <childKey>child</childKey>
  </properties>
</configuration>
```

现在，有效结果如下：

```xml
<configuration>
  <items combine.children="append">
    <item>parent-1</item>
    <item>parent-2</item>
    <item>child-1</item>
  </items>
  <properties combine.self="override">
    <childKey>child</childKey>
  </properties>
</configuration>
```

<code>combine.children="append "</code>的结果是按顺序连接父元素和子元素。而<code>combine.self="override"</code>则会完全抑制父元素的配置。您不能在一个<code>element</code>上同时使用<code>combine.self="override"</code>和<code>combine.children="append" </code>； 如果你尝试，<code>override</code>将会获胜。

请注意，这些属性只适用于它们所声明的配置元素，不会传播到嵌套元素。也就是说，如果子 POM 中项目元素的内容是复杂结构而非文本，那么其子元素仍将采用默认的合并策略，除非它们本身也标有属性。

<code>merge.*</code>属性从父 POM 继承到子 POM。 将这些属性添加到父 POM 时要小心，因为这可能会影响子 POM 或孙 POM。

- **dependencies:** <code>dependencies</code>在 POM 中经常出现，它是所有<code>plugins</code>元素块下的一个元素。<code>dependencies</code>有着与基础构建相同的结构和功能。这种情况下的主要区别在于依赖项不再作为项目的依赖项，而是作为其所在插件的依赖项。其强大之处在于可以更改插件的依赖项列表，可能是通过排除删除未使用的运行时依赖项，或者更改所需依赖项的版本。 有关更多信息，请参阅上面的依赖项。
- **executions:** 需要注意的是，一个<code>plugin</code>可能有多个<code>goals</code>。每个目标都可能有单独的配置，甚至可能将插件的目标完全绑定到不同的阶段。<code>executions</code>配置了一个插件<code>goals</code>的执行。

例如，假设要<code>antrun:run</code>目标绑定到<code>verify</code>阶段。我们希望该任务能呼应构建目录，并通过将<code>inherited</code>设置为<code>false</code>来避免将此配置传递给其子任务（假设它是父任务）。执行结果如下。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.1</version>
        <executions>
          <execution>
            <id>echodir</id>
            <goals>
              <goal>run</goal>
            </goals>
            <phase>verify</phase>
            <inherited>false</inherited>
            <configuration>
              <tasks>
                <echo>Build Dir: /home/jenkins/82467a7c/workspace/aven_maven-box_maven-site_master/target</echo>
              </tasks>
            </configuration>
          </execution>
        </executions>
 
      </plugin>
    </plugins>
  </build>
</project>
```

- **id**: 不言自明。它指定了所有其他执行块之间的这个执行块，该运行阶段时，将以下形式显示： <code>[plugin:goal execution: id]</code>。在此示例中：<code>antrun:run execution: echodir</code>
- **goals**: 它包含了一系列元素，在本例中，是该执行块指定的插件目标列表。
- **phase**: 这是一系列<code>goals</code>列表将执行的阶段。这是一个非常强大的选项，允许将任何目标绑定到构建生命周期中的任何阶段，从而改变 Maven 的默认行为。
- **inherited**: 与上面的继承元素一样，将其设置为 false 将禁止 Maven 将此执行传递给其子元素。 该元素仅对父 POM 有意义。
- **configuration**: 与上面相同，但将配置限制到这个特定的目标列表，而不是插件下的所有目标。

#### Plugin Management

<code>pluginManagement</code>是一个与<code>plugins</code>一起出现的元素。插件管理以大致相同的方式包含插件元素，不同之处在于它不是为特定项目构建配置插件信息，而是用于配置继承自此项目构建的项目。不过，这只能配置子项目或当前 POM 中插件元素实际引用的插件。子项目完全有权覆盖<code>pluginManagement</code>定义。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <executions>
            <execution>
              <id>pre-process-classes</id>
              <phase>compile</phase>
              <goals>
                <goal>jar</goal>
              </goals>
              <configuration>
                <classifier>pre-process</classifier>
              </configuration>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
    ...
  </build>
</project>
```

如果我们将这些规范添加到<code>plugins</code>元素中，它们将仅适用于单个POM。但是，如果我们在<code>pluginManagement</code>元素下应用它们，那么这个 POM 和所有在构建过程中继承 了<code>maven-jar-plugin</code>的POM也将获得预处理类的执行。因此，与其在每个子<code>pom.xml</code>中包含上述乱七八糟的内容，不如只需要以下内容：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
      </plugin>
    </plugins>
    ...
  </build>
</project>
```

-------

### The Build Element Set

XSD 中的 "构建 "类型表示那些只适用于 "项目构建 "的元素。尽管额外元素的数量很多（6 个），但项目构建所包含的元素中只有两组是配置文件构建所缺少的：<code>directories</code> and <code>extensions</code>。

#### Directories

目录元素集存在于父构建元素中，为整个 POM 设置了各种目录结构。由于它们不存在于配置文件构建中，因此不能被配置文件更改。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    <sourceDirectory>${basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>${basedir}/src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${basedir}/src/test/java</testSourceDirectory>
    <outputDirectory>${basedir}/target/classes</outputDirectory>
    <testOutputDirectory>${basedir}/target/test-classes</testOutputDirectory>
    ...
  </build>
</project>
```

如果上述 <code>*Directory</code> 元素的值被设置为绝对路径（在展开其属性时），则使用该目录。否则，它将相对于基本构建目录：<code>${basedir}</code>。请注意，脚本源目录<code>scriptSourceDirectory</code>在 Maven 中已不再使用，并已过时。

#### Extensions

扩展是用于工程构建的一系列artifacts列表。它们将被包含在正在运行的构建的类路径中。他们可以扩展构建过程（例如为 Wagon 传输机制添加 ftp provider），以及激活插件以更改构建生命周期。简而言之，扩展是在构建期间激活的artifacts。扩展不需要做任何实际工作，也不包含 Mojo。因此，扩展非常适合指定一个通用插件接口的多个实现中的一个。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <extensions>
      <extension>
        <groupId>org.apache.maven.wagon</groupId>
        <artifactId>wagon-ftp</artifactId>
        <version>1.0-alpha-3</version>
      </extension>
    </extensions>
    ...
  </build>
</project>
```

## Reporting

报告包含专门对应网站生成阶段的元素。定义和配置在<code>reporting</code>元素下相当多的Maven 插件可以生成报告。例如：生成 Javadoc 报告。 与构建元素配置插件的能力非常相似，与构建元素配置插件的功能一样，报告元素也具有同样的功能。明显的区别在于，明显的区别在于，报告不是在执行块中对插件目标进行精细控制，而是在 reportSet 元素中配置目标。更微妙的区别在于，报告元素下的插件配置可作为构建插件配置使用，尽管相反情况并非如此（构建插件配置不会影响报告插件）。

对于了解构建元素的人来说，报告元素下唯一不熟悉的项目可能是可能就是布尔排除默认值元素<code>Boolean excludeDefaults element</code>。该元素向站点生成器表示排除通常默认生成的报告。当通过站点构建周期生成站点时，项目信息部分会放置在左侧菜单中，其中充满了报告，例如项目团队报告或依赖项列表报告。这些报告目标由 maven-project-info-reports-plugin 生成。作为一个和其他插件一样的插件，它也可以通过以下更冗长的方式被抑制，从而有效地关闭项目信息报告。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <reporting>
    <outputDirectory>${basedir}/target/site</outputDirectory>
    <plugins>
      <plugin>
        <artifactId>maven-project-info-reports-plugin</artifactId>
        <version>2.0.1</version>
        <reportSets>
          <reportSet></reportSet>
        </reportSets>
      </plugin>
    </plugins>
  </reporting>
  ...
</project>
```

另一个区别是<code>plugin</code>下的<code>outputDirectory</code>元素。 在报告的情况下，输出目录默认为<code>${basedir}/target/site</code>。

### Report Sets

重要的是要记住，单个插件可能有多个目标。 每个目标可能有单独的配置。报告集配置报告插件目标的执行。 这听起来是不是很熟悉——似曾相识？关于构建的执行元素也说了同样的事情，但有一个区别：您不能将报告绑定到另一个阶段。 对不起。例如，假设您想将 javadoc:javadoc 目标配置为链接到 "http://java.sun.com/j2se/1.5.0/docs/api/"，但仅限于 javadoc 目标（而不是目标 maven-javadoc-plugin:jar）。我们还希望将此配置传递给其子级，并将inherited设置为 true。 reportSet类似于以下内容：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <reporting>
    <plugins>
      <plugin>
        ...
        <reportSets>
          <reportSet>
            <id>sunlink</id>
            <reports>
              <report>javadoc</report>
            </reports>
            <inherited>true</inherited>
            <configuration>
              <links>
                <link>http://java.sun.com/j2se/1.5.0/docs/api/</link>
              </links>
            </configuration>
          </reportSet>
        </reportSets>
      </plugin>
    </plugins>
  </reporting>
  ...
</project>
```

在build <code>executions</code> 和reporting <code>reportSets</code> 之间，现在应该清楚它们存在的原因了。最简单的意义上讲，它们是对配置的深入研究。它们赋予了 POM 在控制其构建命运方面的最终粒度。

---------

# More Project Information

有几个元素并不影响构建，而是为了方便开发人员而记录项目。在生成项目网站时，这些元素中有许多都用于填写项目细节。不过，与所有 POM 声明一样，插件也可以将它们用于任何用途。以下是最简单的元素：

- **name**: 项目往往都有一个会话式的名称，而不只是<code>artifactId</code>。Sun 公司的工程师们并没有把他们的项目称为 "java-1.5"，而是称之为 "Tiger"。这里是设置该值的位置。
- **description**:  简短、可读的项目描述。尽管这不应取代正式文档，但对 POM 的任何读者进行快速评语总是有帮助的。
- **url**: 项目主页。
- **inceptionYear**: 项目首次创建的年份。

## Licenses-许可证

```xml
<licenses>
  <license>
    <name>Apache-2.0</name>
    <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
    <distribution>repo</distribution>
    <comments>A business-friendly OSS license</comments>
  </license>
</licenses>
```

Licenses 是法律文件定义如何以及何时使用项目，项目应列出直接适用于本项目的许可证，而不应列出适用于本项目依赖关系的许可证。

- **name**, **url** and **comments**: 不言自明，以前在其他情况下也遇到过。建议使用 SPDX 标识符作为许可证名称。第四个许可证元素是
- **distribution**: 这描述了如何合法地分发项目。 两种指定的方法是repo（它们可以从Maven存储库下载）或manual（它们必须手动安装）。

## Organization-组织

大多数项目都是由某种组织（企业、私人团体等）运作的。这里设置了最基本的信息。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <organization>
    <name>Codehaus Mojo</name>
    <url>http://mojo.codehaus.org</url>
  </organization>
</project>
```

## Developers-开发者

所有项目都由某个人在某个时间创建的文件组成。 与围绕项目的其他系统一样，参与项目的人员也与项目息息相关。开发人员可能是项目的核心开发成员。需要注意的是，尽管一个组织可能有很多开发人员（程序员），但把他们都列为开发人员并不是好的做法，而只能是那些直接负责代码的人。一个好的经验法则是，如果不应该就项目与此人联系，就不需要在此列出。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <developers>
    <developer>
      <id>jdoe</id>
      <name>John Doe</name>
      <email>jdoe@example.com</email>
      <url>http://www.example.com/jdoe</url>
      <organization>ACME</organization>
      <organizationUrl>http://www.example.com</organizationUrl>
      <roles>
        <role>architect</role>
        <role>developer</role>
      </roles>
      <timezone>America/New_York</timezone>
      <properties>
        <picUrl>http://www.example.com/jdoe/pic</picUrl>
      </properties>
    </developer>
  </developers>
  ...
</project>
```

- **id**, **name**, **email**:  这些信息对应于开发者的 ID（可能是整个组织的唯一 ID）、开发者的姓名和电子邮件地址。
- **organization**, **organizationUrl**: 你可能猜到了，这分别是开发者的组织名称和 URL。
- **roles**: 角色应明确规定该人负责的标准行动。就像一个人可以身兼数职一样，一个人可以扮演多个角色。
- **timezone**: timezone：有效的时区 ID，例如 America/New_York 或 Europe/Berlin，或者是开发人员居住地与 UTC 的小时数（和分数）偏移量，例如 -5 或 +1。 时区 ID 是高度首选，因为它们不受 DST 和时区转换的影响。 有关官方时区数据库和维基百科中的列表，请参阅 IANA。
- **properties**: 该元素用于放置有关个人的其他属性。例如，指向个人图像或即时消息句柄的链接。不同的插件可能会使用这些属性，也可能只是给阅读 POM 的其他开发人员使用。

## Contributors-贡献者

贡献者就像开发人员一样，但在项目的生命周期中扮演辅助角色。 也许贡献者发送了错误修复，或者添加了一些重要的文档。 一个健康的开源项目可能会有比开发人员更多的贡献者。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <contributors>
    <contributor>
      <name>Noelle</name>
      <email>some.name@gmail.com</email>
      <url>http://noellemarie.com</url>
      <organization>Noelle Marie</organization>
      <organizationUrl>http://noellemarie.com</organizationUrl>
      <roles>
        <role>tester</role>
      </roles>
      <timezone>America/Vancouver</timezone>
      <properties>
        <gtalk>some.name@gmail.com</gtalk>
      </properties>
    </contributor>
  </contributors>
  ...
</project>
```

--------

# Environment Settings

## Issue Management-问题管理

这定义了所使用的bug跟踪系统（Bugzilla、TestTrack、ClearQuest 等）。尽管没有什么可以阻止插件使用此信息进行某些操作，但它主要用于生成项目文档。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <issueManagement>
    <system>Bugzilla</system>
    <url>http://127.0.0.1/bugzilla/</url>
  </issueManagement>
  ...
</project>
```

## Continuous Integration Management-持续集成管理

在过去几年中，基于触发器或定时（如每小时或每天）的持续集成构建系统比手动构建系统更受欢迎。随着构建系统越来越标准化，运行触发这些构建的系统也越来越标准化。虽然大部分配置取决于所使用的特定程序（Continuum、Cruise Control 等），但也有一些配置可能会在 POM 中进行。Maven 在通知器元素集中捕捉到了一些经常出现的重复设置。通知器是通知人们某些构建状态的方式。在以下示例中，此 POM 设置邮件类型的通知程序（即电子邮件），并配置要在指定触发器<code>sendOnError</code>、<code>sendOnFailure</code>上使用的电子邮件地址，而不是<code>sendOnSuccess</code>或<code>sendOnWarning</code>。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <ciManagement>
    <system>continuum</system>
    <url>http://127.0.0.1:8080/continuum</url>
    <notifiers>
      <notifier>
        <type>mail</type>
        <sendOnError>true</sendOnError>
        <sendOnFailure>true</sendOnFailure>
        <sendOnSuccess>false</sendOnSuccess>
        <sendOnWarning>false</sendOnWarning>
        <configuration><address>continuum@127.0.0.1</address></configuration>
      </notifier>
    </notifiers>
  </ciManagement>
  ...
</project>
```

## Mailing Lists-邮件列表

邮件列表是与项目相关人员保持联系的绝佳工具。大多数邮件列表都面向开发人员和用户。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <mailingLists>
    <mailingList>
      <name>User List</name>
      <subscribe>user-subscribe@127.0.0.1</subscribe>
      <unsubscribe>user-unsubscribe@127.0.0.1</unsubscribe>
      <post>user@127.0.0.1</post>
      <archive>http://127.0.0.1/user/</archive>
      <otherArchives>
        <otherArchive>http://base.google.com/base/1/127.0.0.1</otherArchive>
      </otherArchives>
    </mailingList>
  </mailingLists>
  ...
</project>
```

- **subscribe**, **unsubscribe**: 些元素指定用于执行相关操作的电子邮件地址。要订阅上面的用户列表，用户将向 user-subscribe@127.0.0.1 发送电子邮件。
- **archive**: 此元素指定旧邮件列表电子邮件的存档的 URL（如果存在）。 如果有镜像存档，可以在otherArchives下指定。
- **post**: 用于发布到邮件列表的电子邮件地址。 请注意，并非所有邮件列表都能够发帖（例如构建失败列表）。

## SCM-版本控制

SCM（软件配置管理，也称为源代码/控制管理，或者简单地说，版本控制）是任何健康项目不可或缺的一部分。 如果您的 Maven 项目使用 SCM 系统（确实如此，不是吗？），那么您可以在此处将该信息放入 POM 中。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <scm>
    <connection>scm:svn:http://127.0.0.1/svn/my-project</connection>
    <developerConnection>scm:svn:https://127.0.0.1/svn/my-project</developerConnection>
    <tag>HEAD</tag>
    <url>http://127.0.0.1/websvn/my-project</url>
  </scm>
  ...
</project>
```

- **connection**, **developerConnection**: 这两个连接元素传达了如何通过 Maven 连接到版本控制系统。连接需要 Maven 的读取访问权限才能找到源代码（例如更新），而developerConnection 则需要提供写入访问权限的连接。Maven 项目衍生了另一个名为 Maven SCM 的项目，该项目为任何希望实现它的 SCM 创建了一个通用 API。 最流行的是 CVS 和 Subversion，但是，其他受支持的 SCM 的列表也在不断增加。 所有 SCM 连接都是通过通用 URL 结构建立的。

  ```
  scm:[provider]:[provider_specific]
  ```

- 其中provider是SCM系统的类型。 例如，连接到 CVS 存储库可能如下所示：

  ```
  scm:cvs:pserver:127.0.0.1:/cvs/root:my-project
  ```

- **tag**: 指定该项目所在的标签。 HEAD（即 SCM root）是默认值。

- **url**: 可公开浏览的存储库。 例如，通过 ViewCVS。

## Prerequisites-先决条件

POM 可能需要某些先决条件才能正确执行。在 POM 4.0.0 中，唯一作为先决条件存在的元素是<code>maven</code>元素，它需要一个最低版本号。

使用 Maven Enforcer Plugin 的<code>requireMavenVersion</code>规则或其他规则作为构建时的先决条件。对于打包的<code>maven-plugin</code>，运行时仍会使用该规则，以确保满足插件的最低 Maven 版本要求（但仅限于引用插件的 pom.xml）。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <prerequisites>
    <maven>2.0.6</maven>
  </prerequisites>
  ...
</project>
```

## Repositories-存储库

存储库是遵循 Maven 存储库目录布局的工程集合。要成为 Maven 资源库工程，POM 文件必须位于 <code>$BASE_REPO/groupId/artifactId/version/artifactId-version.pom</code>结构中。<code>$BASE_REPO</code>可以是本地的（文件结构），也可以是远程的（基本 URL）；其余布局将保持不变。资源库是收集和存储<code>artifacts</code>的地方。每当项目依赖于某个artifact时，Maven 将首先尝试使用指定工件的本地副本。 如果本地存储库中不存在该工件，它将尝试从远程存储库下载。POM 中的版本库元素指定了要搜索的备用版本库。

存储库是 Maven 社区最强大的功能之一。 默认情况下，Maven 搜索 https://repo.maven.apache.org/maven2/ 的中央存储库。 可以在 pom.xml `repositories` 元素中配置其他存储库。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <repositories>
    <repository>
      <releases>
        <enabled>false</enabled>
        <updatePolicy>always</updatePolicy>
        <checksumPolicy>warn</checksumPolicy>
      </releases>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>never</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
      </snapshots>
      <name>Nexus Snapshots</name>
      <id>snapshots-repo</id>
      <url>https://oss.sonatype.org/content/repositories/snapshots</url>
      <layout>default</layout>
    </repository>
  </repositories>
  <pluginRepositories>
    ...
  </pluginRepositories>
  ...
</project>
```

- **releases**, **snapshots**: 这些是针对每种类型的工件（<code>Release</code>或者<code>snapshot</code>）的策略。有了这两套策略，POM 就能在单个版本库中改变每种类型的策略，而不受另一种策略的影响。例如，可能出于开发目的，有人会决定只启用快照下载。
- **enabled**: <code>true</code>或<code>false</code>，表示该版本库是否为相应类型（<code>Release</code>或者<code>snapshot</code>）启用。
- **updatePolicy**: 该元素指定了尝试更新的频率。Maven 将比较本地 POM 的时间戳（存储在版本库的 maven-metadata 文件中）和远程 POM 的时间戳。可选项有：<code>always</code>、<code>daily</code>（默认）、<code>interval:X</code>（X 是以分钟为单位的整数）或<code>never</code>（仅在本地版本库中不存在时下载）。
- **checksumPolicy**: 当Maven将文件部署到存储库时，它也会部署相应的校验和文件。当校验和缺失或者不正确您的可选项是 <code>ignore</code>,<code>fail</code>, or <code>warn</code>
- **layout**: 在上文对资源库的描述中，提到它们都遵循一个共同的布局。这基本上是正确的。Maven 2 引入的布局是 Maven 2 和 3 使用的版本库默认布局。不过，Maven 1.x 有不同的布局。使用此元素指定它是默认的还是旧的。

## Plugin Repositories-插件存储库

存储库是两种主要类型artifacts的所在地。第一种类型是用作其他工程的依赖项，另一种类型的工程是插件。Maven 插件本身就是一种特殊类型的工程。因此，插件存储库可能与其他存储库分开（尽管，我还没有听到这样做的令人信服的论据）。 无论如何，pluginRepositories 元素块的结构与 repositories 元素类似。 每个pluginRepository 元素都指定Maven 可以找到新插件的远程位置。

## Distribution Management-分发管理

分发管理的作用正如其听起来一样：它管理整个构建过程中生成的工件和支持文件的分发。 首先从最后一个元素开始：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    ...
    <downloadUrl>http://mojo.codehaus.org/my-project</downloadUrl>
    <status>deployed</status>
  </distributionManagement>
  ...
</project>
```

- **downloadUrl**: 是另一个 POM 可以指向的存储库的 URL，以便获取此 POM 的工件。 用最简单的话来说，我们告诉 POM 如何上传它（通过<code>repository/url</code>），但是公众可以从哪里下载它呢？ 这个元素回答了这个问题。
- **status**: 警告！就像巢中的雏鸟一样，status永远不应该被人类的手触碰！这样做的原因是，当项目被传输到存储库时，Maven 会自动设置项目的状态。 其有效类型如下。
  - **none**: 没有特殊的状态。 这是 POM 的默认设置。
  - **converted**: 存储库的管理员将此 POM 从早期版本转换为 Maven 2。
  - **partner**: 该artifact已与合作伙伴资源库同步。
  - **deployed**: ：迄今为止最常见的状态，这意味着该工件是从 Maven 2 或 3 实例部署的。 这是使用命令行<code>deploy</code>手动部署时得到的结果。
  - **verified**: 该项目已经过验证，应视为已完成。

### Repository-存储库

repositories 元素在 POM 中指定了 Maven 下载远程工件供当前项目使用的位置和方式，而 distributionManagement 则指定了该项目在部署时从哪里（以及如何）进入远程版本库。如果未定义快照存储库（snapshotRepository），存储库元素将用于快照分发。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    <repository>
      <uniqueVersion>false</uniqueVersion>
      <id>corp1</id>
      <name>Corporate Repository</name>
      <url>scp://repo/maven2</url>
      <layout>default</layout>
    </repository>
    <snapshotRepository>
      <uniqueVersion>true</uniqueVersion>
      <id>propSnap</id>
      <name>Propellors Snapshots</name>
      <url>sftp://propellers.net/maven</url>
      <layout>legacy</layout>
    </snapshotRepository>
    ...
  </distributionManagement>
  ...
</project>
```

- **id**, **name**: <code>id</code>用于在众多资源库中唯一标识该资源库，<code>name</code>是人类可读的形式。
- **uniqueVersion**: 唯一版本，取值为<code>true</code>或<code>false</code>，表示部署到该版本库的工件是应该获得唯一生成的版本号，还是使用定义为地址一部分的版本号。
- **url**: 这是版本库元素的核心。它指定了用于将构建的工件（以及 POM 文件和校验和数据）传输到版本库的位置和传输协议。
- **layout**: 这些与存储库元素中定义的布局元素具有相同的类型和用途。 

### Site Distribution-站点发布

分发管理<code>distributionManagement</code>不仅负责向资源库分发，还负责定义如何部署项目网站和文档。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    ...
    <site>
      <id>mojo.website</id>
      <name>Mojo Website</name>
      <url>scp://beaver.codehaus.org/home/projects/mojo/public_html/</url>
    </site>
    ...
  </distributionManagement>
  ...
</project>
```

- **id**, **name**, **url**: 这些元素与上面的 distributionManagement 资源库元素中的对应元素类似。

### Relocation-搬迁

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"1 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <distributionManagement>
    ...
    <relocation>
      <groupId>org.apache</groupId>
      <artifactId>my-project</artifactId>
      <version>1.0</version>
      <message>We have moved the Project under Apache</message>
    </relocation>
    ...
  </distributionManagement>
  ...
</project>
```

项目不是一成不变的；它们是有生命的东西（或垂死的东西，视情况而定）。随着项目的发展壮大，一个常见的情况是项目被迫迁往更合适的地方。例如，当你的下一个大获成功的开源项目转移到 Apache 旗下时，最好能给用户提个醒，告诉他们项目将更名为<code>org.apache:my-project:1.0</code>。除了指明新地址外，提供一条解释原因的信息也是很好的形式。

## Profiles

POM 4.0 的一个新功能是项目可以根据构建环境改变设置。一个<code>profile</code>元素既包含可选激活（配置文件触发器），也包含激活该配置文件后对 POM 所做的一系列更改。例如，为测试环境构建的项目可能与最终部署环境有着不同的数据库。或者，根据所使用的 JDK 版本不同，依赖关系也可能来自不同的资源库。The elements of profiles are as follows:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <profiles>
    <profile>
      <id>test</id>
      <activation>...</activation>
      <build>...</build>
      <modules>...</modules>
      <repositories>...</repositories>
      <pluginRepositories>...</pluginRepositories>
      <dependencies>...</dependencies>
      <reporting>...</reporting>
      <dependencyManagement>...</dependencyManagement>
      <distributionManagement>...</distributionManagement>
    </profile>
  </profiles>
</project>
```

### Activation

<code>Activations</code>是<code>Profiles</code>的关键。只有在特定情况下，配置文件才能修改基本 POM。这些情况通过激活一个activation元素来指定。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <profiles>
    <profile>
      <id>test</id>
      <activation>
        <activeByDefault>false</activeByDefault>
        <jdk>1.5</jdk>
        <os>
          <name>Windows XP</name>
          <family>Windows</family>
          <arch>x86</arch>
          <version>5.1.2600</version>
        </os>
        <property>
          <name>sparrow-type</name>
          <value>African</value>
        </property>
        <file>
          <exists>${basedir}/file2.properties</exists>
          <missing>${basedir}/file1.properties</missing>
        </file>
      </activation>
      ...
    </profile>
  </profiles>
</project>
```

自 Maven 3.2.2 以来，当满足所有指定条件时才会激活。

- **jdk**: <code>activation</code>在 jdk 元素中具有内置的、以 Java 为中心的检查。 该值为以下三种类型之一:
  - 根据 maven-enforcer-plugin 定义的版本范围，<code>[</code>或<code>（,</code>
  - 如果值以<code> ! <code>开头，则为否定前缀
  - 一个（无负数）前缀，用于所有其他情况

- 如果运行 Maven 所用的 JDK 版本以给定的前缀开始/不以给定的前缀开始（不包括潜在的前导 !），则前缀（否定）值匹配。如果运行 Maven 所用的 JDK 版本介于下限和上限之间（包含或排除），则值范围匹配。

- **os**: os 元素可以定义一些特定于操作系统的属性，如上图所示。有关操作系统值的更多详情，请参阅 maven-enforcer-plugins RequireOS Rule。
- **property**: 如果 Maven 检测到相应<code>name=value</code>对的系统属性或 CLI 用户属性（可以在 POM 中通过 ${name} 解除引用）与给定值（如果给定）匹配，则<code>profile</code>将激活。
- **file**: 最后，给定的文件名可以通过文件存在或丢失来激活配置文件。 注意：此元素的插值仅限于 ${basedir}、系统属性和请求属性。

# Final

Maven POM 体积庞大。不过，它的大小也证明了它的多功能性。

至少可以说，将项目的所有方面抽象为单个工件的能力是强大的。 每个项目都有数十个不同的构建脚本和分散的文档的日子已经一去不复返了。

与 Maven 一起组成 Maven 星系的其他明星---定义明确的构建生命周期、易于编写和维护的插件、集中式存储库、系统范围和基于用户的配置，以及越来越多的工具来完成开发人员的工作 更容易维护复杂的项目 - POM 是大而明亮的中心。

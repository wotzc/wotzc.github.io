---
title: Spring Cloud Alibaba Nacos
date: 2021-08-25 14:53:33
tags:
- Spring Cloud Alibaba
- Nacos 
- 注册中心
- 配置中心
categories: Spring Cloud Alibaba
---

#  Nacos能做什么?

Nacos（官网：nacos.io）是一个易于使用的平台，专为动态服务发现和配置以及服务管理而设计。它可以帮助您轻松构建云原生应用程序和微服务平台。 服务是Nacos的关键。 Nacos 支持几乎所有类型的服务，例如，Dubbo/gRPC 服务、Spring Cloud RESTFul 服务或 Kubernetes 服务。

Nacos 提供了四大主要功能。

- **服务发现和服务健康检查**

  Nacos 使服务通过 DNS 或 HTTP 接口注册自己和发现其他服务变得简单。 Nacos 还提供服务的实时健康检查，以防止向不健康的主机或服务实例发送请求。

- **动态配置管理**

  动态配置服务允许您在所有环境中以集中和动态的方式管理所有服务的配置。 Nacos 无需在更新配置时重新部署应用程序和服务，这使得配置更改更加高效和敏捷。

- **动态 DNS 服务**

  Nacos 支持加权路由，让您更容易在数据中心内的生产环境中实现中层负载均衡、灵活的路由策略、流量控制和简单的 DNS 解析服务。它可以帮助您轻松实现基于 DNS 的服务发现，并防止应用程序耦合到特定于供应商的服务发现 API。

- **服务和元数据管理**

  Nacos 提供了一个易于使用的服务仪表板，帮助您管理您的服务元数据、配置、kubernetes DNS、服务健康和指标统计。 

**本文主要讲讲Nacos作为注册中心和Nacos作为配置中心的使用**

-----------------------

# Nacos 地图

一图看懂 Nacos

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosMap.png)

-------------------

# Nacos 快速开始

## 预备环境准备

Nacos 依赖 [Java](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 环境来运行。如果您是从代码开始构建并运行Nacos，还需要为此配置 [Maven](https://maven.apache.org/index.html)环境，请确保是在以下版本环境中安装使用:

1. 64 bit OS，支持 Linux/Unix/Mac/Windows，推荐选用 Linux/Unix/Mac。
2. 64 bit JDK 1.8+；[下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) & [配置](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)。
3. Maven 3.2.x+；[下载](https://maven.apache.org/download.cgi) & [配置](https://maven.apache.org/settings.html)。

## 下载

您可以从 [最新稳定版本](https://github.com/alibaba/nacos/releases) 下载 `nacos-server-$version.zip` 包。

```bash
  unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
  cd nacos/bin
```

## 启动服务器

### Linux/Unix/Mac

启动命令(standalone代表着单机模式运行，非集群模式):

```
sh startup.sh -m standalone
```

### Windows

启动命令(standalone代表着单机模式运行，非集群模式):

```
startup.cmd -m standalone
```

或双击bin中的startup.cmd 文件

访问http://localhost:8848/nacos/
 账户密码使用默认的nacos/nacos 进行登录

------------

## SpringCloud Alibaba-Nacos[作为注册中心]

### 首先，修改pom.xml 文件，引入Nacos Discovery Starter。

如果要在您的项目中使用 Nacos 来实现服务注册/发现，使用 group ID 为 `com.alibaba.cloud` 和 artifact ID 为 `spring-cloud-starter-alibaba-nacos-discovery` 的 starter。

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 修改application.yml配置

- 在应用application.yml配置文件中配置Nacos Server 地址

```properties
# nacos服务地址
spring:
  cloud:
	nacos:
	  discovery:
		server-addr: 127.0.0.1:8848
```

- 在应用application.yml配置文件中配置服务的名称

```properties
# nacos服务地址
spring:
  application:
  	name: product
```

### 通过Spring Cloud原生注解 `@EnableDiscoveryClient` 开启服务注册发现功能

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }

}
```

### 启动应用

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/startApplication.png)

启动成功后再去nacos服务列表里，观察nacos 服务列表是否已经注册上服务，如下图所示已经注册上了

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosservices.png)

------------------

## SpringCloud Alibaba-Nacos[作为配置中心]

我们先了解一下 Spring Cloud 应用如何接入 Nacos Config，步骤如下

1. 首先，修改 pom.xml 文件，引入 Nacos Config Starter。

   ```xml
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>
   ```

   

2. 在应用的 /src/main/resources/bootstrap.properties 配置文件中配置 Nacos Config 元数据

   ```java
    spring.application.name=nacos-config-example
    spring.cloud.nacos.config.server-addr=127.0.0.1:8848
   ```

3. 需要给配置中心默认添加一个叫数据集（Data Id）gulimall-coupon.properties。默认规则，应用名.properties

4. 给应用名.properties中添加配置

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosconfig.png)

5. 完成上述步骤后，应用会从 Nacos Config 中获取相应的配置，并添加在 Spring Environment 的 PropertySources 中。这里我们使用 @Value 注解来将对应的配置注入到 SampleController 的 userName 和 age 字段，并添加 @RefreshScope 打开动态刷新功能

```java
 @RefreshScope
 class SampleController {

 	@Value("${user.name}")
 	String userName;

 	@Value("${user.age}")
 	int age;
 }

```

> ```ABAP
> 如果配置中心和当前应用的配置文件中都配置了相同的项，优先使用配置中心的配置
> ```

### 细节：

**1）命名空间**

用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的Group 或Data ID 的配置。Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。

默认：public（保留空间）；默认新增的所有配置都在public

- 开发，测试，生产：利用命名空间来做环境隔离

- 我们需要在我们的项目的bootstrap.properties配置上我们需要使用的命名空间配置。**namespace一定要写命名空间的唯一id**

  ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/bootstrapproperties1.png)

每一个微服务之间相互隔离配置，每一个微服务都创建自己的命名空间，只加载自己命名空间下的所有配置。

**2）配置集**

一组相关或不相关的配置项的集合称为配置集。在系统中一个配置文件通常就是一个配置集，包含了系统各个方面的配置。例如，一个配置集可能包含了数据源、线程池、日志级别等配置项。

**3）配置集ID**

Data ID：类似于文件名

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosconfigid.png)

**4）配置分组**

默认所有的配置集都属于：DEFAULT_GROUP

我们也可以创建自己的分组例如1111,618,1212

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosconfiggroup.png)

然后在项目的bootstrap.properties文件中指定分组

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosconfiggroup1.png)

-------------------

### **实际项目中的应用**

每个微服务创建自己的命名空间，使用配置分组来区分开发环境，如dev，test，prod。

----

### 同时加载多个配置集

微服务的任何配置信息，任何配置文件都可以放在配置中心中

例如我们要读取配置中心的datasource.yml、mybatis.yml、 others.yml三个配置文件

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosconfigfile.png)

在项目中进行读取，如下图所示

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/nacosreadfile.png)
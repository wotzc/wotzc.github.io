---

title: SpringCloud Gateway
date: 2021-08-28 10:36:42
tags:
- SpringCloud
- Gateway
- 网关
- 路由
categories: SpringCloud
---

#  简介

网关作为流量的入口，常用功能包括路由转发、权限校验、限流控制等。而springcloud gateway作为SpringCloud 官方推出的第二代网关框架，取代了Zuul 网关。

网关提供API 全托管服务，丰富的API 管理功能，辅助企业管理大规模的API，以降低管理成本和安全风险，包括协议适配、协议转发、安全策略、防刷、流量、监控日志等功能。
Spring Cloud Gateway 旨在提供一种简单而有效的方式来对API 进行路由，并为他们提供切面，例如：安全性，监控/指标和弹性等。

Spring Cloud Gateway 特点:

- 基于Spring5，支持响应式编程和SpringBoot2.0
- 支持使用任何请求属性进行路由匹配
- 特定于路由的断言和过滤器
- 集成Hystrix 进行断路保护
- 集成服务发现功能
- 易于编写Predicates 和Filters
- 支持请求速率限制
- 支持路径重写

------

# 核心概念

- **Route**: 路由是网关最基础的部分，路由信息有一个ID、一个目的URL、一组断言和一组Filter 组成。如果断言路由为真，则说明请求的URL 和配置匹配
- **Predicate**: 断言。Java8 中的断言函数。Spring Cloud Gateway 中的断言函数输入类型是Spring5.0 框架中的ServerWebExchange。Spring Cloud Gateway 中的断言函数允许开发者去定义**匹配**来自于http request 中的任何信息，比如请求头和参数等。
- **Filter**: 过滤器。一个标准的Spring webFilter。Spring cloud gateway 中的filter 分为两种类型的Filter，分别是Gateway Filter 和Global Filter。过滤器Filter 将会对请求和响应进行**修改处理**。
- **通俗的讲，断言Predicate是判断当前请求是否向下继续传递，而过滤器Filter可以对请求和响应进行修复和处理**

-------------

# 工作原理

​	下图提供了 Spring Cloud Gateway 工作原理的高级概述：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/gateway/spring_cloud_gateway_diagram.png)

​	客户端发送请求给网关，弯管HandlerMapping 判断是否请求满足某个路由，满足就发给网关的WebHandler。这个WebHandler 将请求交给一个过滤器链，请求到达目标服务之前，会执行所有过滤器的pre 方法。请求到达目标服务处理之后再依次执行所有过滤器的post 方法。

{% note no-icon primary %}

在没有端口的路由中定义的 URI 分别获得 HTTP 和 HTTPS URI 的默认端口值 80 和 443。 

{% endnote %}

-------------

# 使用

## 引入gateway

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

**application.yml**

```yaml
spring:
  cloud:
	gateway:
	  routes:
	  - id: add_request_parameter_route
        uri: https://example.org
        predicates:
        - Query=baz
        filters:
        - AddRequestParameter=foo, bar
```

各字段含义如下：

- <code>id</code>：我们自定义的路由 ID，保持唯一
- <code>uri</code>：目标服务地址
- <code>predicates</code>：路由条件，Predicate 接受一个输入参数，返回一个布尔值结果。该接口包含多种默认方法来将 Predicate 组合成其他复杂的逻辑（比如：与，或，非）。
- <code>filters</code>：过滤规则。

{% note no-icon info %}

各种Predicates 同时存在于同一个路由时，请求必须同时满足所有的条件才被这个路由匹配。
一个请求满足多个路由的谓词条件时，请求只会被首个成功匹配的路由转发

{% endnote %}

-------------

# Predicate 断言条件

Predicate 来源于 Java 8，是 Java 8 中引入的一个函数，Predicate 接受一个输入参数，返回一个布尔值结果。该接口包含多种默认方法来将 Predicate 组合成其他复杂的逻辑（比如：与，或，非）。可以用于接口请求参数校验、判断新老数据是否有变化需要进行更新操作。

在 Spring Cloud Gateway 中 Spring 利用 Predicate 的特性实现了各种路由匹配规则，有通过 Header、请求参数等不同的条件来进行作为条件匹配到对应的路由。网上有一张图总结了 Spring Cloud 内置的几种 Predicate 的实现。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/gateway/RoutePredicateFactories.png)

## 通过请求时间匹配

### After Route Predicate Factory

The following example configures an after route predicate:`After` `datetime` `ZonedDateTime`

Example 1. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - After=2017-01-20T17:42:00
```

此路由匹配 2017 年 1 月 20 日 17:42Mountain Time (Denver) 之后提出的任何请求。 

###  Before Route Predicate Factory

The following example configures a before route predicate:`Before` `datetime` `ZonedDateTime` `datetime`

Example 2. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: before_route
        uri: https://example.org
        predicates:
        - Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```

此路由匹配 2017 年 1 月 20 日  17:42 Mountain Time (Denver)之前提出的任何请求。

### Between Route Predicate Factory

The following example configures a between route predicate:`Between` `datetime1` `datetime2` `ZonedDateTime` `datetime1` `datetime2` `datetime2` `datetime1`

Example 3. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: between_route
        uri: https://example.org
        predicates:
        - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
```

此路由匹配 任何在2017 年 1 月 20 日 17:42 Mountain Time (Denver) 之后并在2017 年 1 月 20 日 17:42 Mountain Time (Denver) 之前的请求。

-------------------

## 通过Cookie匹配

### The Cookie Route Predicate Factory

此路由谓词工厂接受两个参数，cookie 和 Java 正则表达式。 此谓词匹配具有给定名称且其值与正则表达式匹配的 cookie。 

The following example configures a cookie route predicate factory:`Cookie` `name` `regexp`

Example 4. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: cookie_route
        uri: https://example.org
        predicates:
        - Cookie=chocolate, ch.p
```

此路由匹配 请求的Cookie中有“chocolate”且其值等于“ch.p”

-----------

## 通过Header匹配

### Header Route Predicate Factory

Header 路由谓词工厂接受两个参数，Header名称和一个 regexp（这是一个 Java 正则表达式）。 此谓词与具有给定名称的Header标头匹配，并且其值与正则表达式匹配。 以下示例配置标头路由谓词： 

Example 5. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: header_route
        uri: https://example.org
        predicates:
        - Header=X-Request-Id, \d+
```

如果请求具有名为 X-Request-Id 的标头，其值与 <code>\d+</code>正则表达式匹配（即，它具有一个或多个数字的值），则此路由匹配。 

---

## 通过Host匹配

### Host Route Predicate Factory

Host路由谓词工厂采用一个参数：host名模式列表。 该模式是 Ant 风格的模式，带有 . 作为分隔符。 此谓词匹配与模式匹配的 Host 标头。 以下示例配置主机路由谓词： 

Example 6. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: host_route
        uri: https://example.org
        predicates:
        - Host=**.somehost.org,**.anotherhost.org
```

还支持 URI 模板变量（例如 {sub}.myhost.org）。 

如果请求具有值为 www.somehost.org 或 beta.somehost.org 或 www.anotherhost.org 的 Host 标头，则此路由匹配。

 此谓词提取 URI 模板变量（例如 sub，在前面的示例中定义）作为名称和值的映射，并将其放置在<code>ServerWebExchange.getAttributes()</code>中，并使用 <code>ServerWebExchangeUtils.URI_TEMPLATE_VARIABLES_ATTRIBUTE</code>中定义的键。 然后这些值可供<code>GatewayFilter</code>工厂使用 

-----

## 通过请求方式匹配

### Method Route Predicate Factory

方法路由谓词工厂采用一个方法参数，它是一个或多个参数：要匹配的 HTTP 方法。 以下示例配置方法路由谓词： 

Example 7. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: method_route
        uri: https://example.org
        predicates:
        - Method=GET,POST
```

如果请求方法是 GET 或 POST，则路由匹配。 

----

## 通过请求路径匹配

### Path Route Predicate Factory

Path Route Predicate Factory 接受两个参数：一个 Spring PathMatcher 模式列表和一个名为 matchTrailingSlash 的可选标志（默认为 true）。 以下示例配置路径路由谓词： 

Example 8. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: path_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment},/blue/{segment}
```

如果请求路径是例如：<code>/red/1</code>或<code>/red/1/</code>或<code>/red/blue</code>或<code>/blue/green</code>，则此路由匹配。 

如果<code>matchTrailingSlash</code>（匹配反斜杠）设置为<code>false</code>，则不会匹配请求路径<code>/red/1/</code>。

此谓词提取 URI 模板变量（例如在前面的示例中定义的段）作为名称和值的映射，并将其放置在 <code>ServerWebExchange.getAttributes()</code>中，键是在 <code>ServerWebExchangeUtils.URI_TEMPLATE_VARIABLES_ATTRIBUTE</code>中定义的。

 然后这些值可供 GatewayFilter 工厂使用 可以使用实用方法（称为 get）来更轻松地访问这些变量。 以下示例显示了如何使用 get 方法： 

```java
Map<String, String> uriVariables = ServerWebExchangeUtils.getPathPredicateVariables(exchange);

String segment = uriVariables.get("segment");
```

------

## 通过查询匹配

### Query Route Predicate Factory

查询路由谓词工厂有两个参数：一个必需的参数和一个可选的正则表达式（这是一个 Java 正则表达式）。 以下示例配置查询路由谓词：

Example 9. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: query_route
        uri: https://example.org
        predicates:
        - Query=green
```

如果请求包含参数**green**，则路由匹配。

application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: query_route
        uri: https://example.org
        predicates:
        - Query=red, gree.
```

如果请求包含参数red，并且其对应的值满足正则表达式gree.，则路由匹配。所以 green 和 greet 会匹配。 

-----

## 通过远程ip地址匹配

### RemoteAddr Route Predicate Factory

RemoteAddr 路由谓词工厂采用源列表（最小大小 1），这些源是 CIDR 表示法（IPv4 或 IPv6）字符串，例如 192.168.0.1/16（其中 192.168.0.1 是 IP 地址，16 是子网掩码 ）。 以下示例配置<code>RemoteAddr</code>路由谓词： 

Example 10. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: remoteaddr_route
        uri: https://example.org
        predicates:
        - RemoteAddr=192.168.1.1/24
```

如果请求的远程地址是例如 192.168.1.10，则此路由匹配。 

--------------------

#  过滤器规则（Filter）

路由过滤器允许以某种方式修改传入的 HTTP 请求或传出的 HTTP 响应。 路由过滤器的范围是特定的路由。 Spring Cloud Gateway 包括许多内置的<code>GatewayFilter</code>工厂。 

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/gateway/GatewayFilterFactories.png)

## AddRequestHeade GatewayFilter Factory

The `AddRequestHeader` `GatewayFilter` factory takes a `name` and `value` parameter. The following example configures an `AddRequestHeader` `GatewayFilter`:

Example 13. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: add_request_header_route
        uri: https://example.org
        filters:
        - AddRequestHeader=X-Request-red, blue
```

**会给所有匹配的请求头的请求集合添加上`X-Request-red:blue` header 。**

## AddRequestParameter GatewayFilter Factory

The `AddRequestParameter` `GatewayFilter` Factory takes a `name` and `value` parameter. The following example configures an `AddRequestParameter` `GatewayFilter`:

Example 15. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: add_request_parameter_route
        uri: https://example.org
        filters:
        - AddRequestParameter=red, blue
```

**会给所有匹配的请求添加上 `red=blue` 的请求参数。**

## The AddResponseHeader GatewayFilter Factory

The `AddResponseHeader` `GatewayFilter` Factory takes a `name` and `value` parameter. The following example configures an `AddResponseHeader` `GatewayFilter`:

Example 17. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: add_response_header_route
        uri: https://example.org
        filters:
        - AddResponseHeader=X-Response-Red, Blue
```

会给所有的匹配的请求添加上 `X-Response-Foo:Bar` 的响应头。

## PrefixPath GatewayFilter Factory

The `PrefixPath` `GatewayFilter` factory takes a single <code>prefix</code> parameter. The following example configures a `PrefixPath` `GatewayFilter`:

Example 28. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: prefixpath_route
        uri: https://example.org
        filters:
        - PrefixPath=/mypath
```

这会为所有匹配请求的路径加上/mypath 前缀。 因此，对 /hello 的请求将被发送到 /mypath/hello。 

## PreserveHostHeader GatewayFilter Factory

PreserveHostHeader GatewayFilter 工厂没有参数。 此过滤器设置路由过滤器检查的请求属性，以确定是否应发送原始主机标头，而不是由 HTTP 客户端确定的主机标头。 以下示例配置 PreserveHostHeader GatewayFilter： 

Example 29. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: preserve_host_route
        uri: https://example.org
        filters:
        - PreserveHostHeader
```

## RedirectTo GatewayFilter Factory

RedirectTo GatewayFilter 工厂接受两个参数， `status` 和 `url`。 `status` 参数应该是一个 300 系列的重定向 HTTP 代码，比如 301。`url`参数应该是一个有效的 URL。 这是 Location 标头的值。 对于相对重定向，您应该使用 `uri: no://op` 作为路由定义的 uri。 以下清单配置了一个 RedirectTo GatewayFilter： 

Example 35. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: prefixpath_route
        uri: https://example.org
        filters:
        - RedirectTo=302, https://acme.org
```

这将发送带有 Location:https://acme.org 标头的状态 302 以执行重定向。



## RemoveRequestHeader GatewayFilter Factory

RemoveRequestHeader GatewayFilter 工厂只有一个 <code>name</code>参数。 它是要删除的header的名称。 以下清单配置了 RemoveRequestHeader GatewayFilter： 

Example 36. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: removerequestheader_route
        uri: https://example.org
        filters:
        - RemoveRequestHeader=X-Request-Foo
```

请求在向下转发之前会删除 header `X-Request-Foo`。

## RemoveResponseHeader GatewayFilter Factory

RemoveResponseHeader GatewayFilter工厂只有一个 <code>name</code>参数。 它是要删除的header的名称。 以下清单配置了 RemoveResponseHeader GatewayFilter：

The following listing configures a `RemoveResponseHeader` `GatewayFilter`:

Example 37. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: removeresponseheader_route
        uri: https://example.org
        filters:
        - RemoveResponseHeader=X-Response-Foo
```

这将在响应返回到网关客户端之前从响应中删除 X-Response-Foo 标头。

要删除任何类型的敏感标头，您应该为您可能想要这样做的任何路由配置此过滤器。 此外，您可以使用 spring.cloud.gateway.default-filters 配置一次此过滤器，并将其应用于所有路由。

## RemoveRequestParameter GatewayFilter Factory

RemoveRequestParameter GatewayFilter 只有一个<code>name</code>参数。 它是要删除的查询参数的名称。 以下示例配置 RemoveRequestParameter GatewayFilter：

Example 38. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: removerequestparameter_route
        uri: https://example.org
        filters:
        - RemoveRequestParameter=red
```

请求在向下转发之前会删除red参数。

## RewritePath GatewayFilter Factory

RewritePath GatewayFilter 工厂带有两个参数，一个是正则表达式的路径参数，一个是替换参数。这使用 Java 正则表达式来灵活地重写请求路径。 以下清单配置了 RewritePath GatewayFilter： 

Example 39. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: rewritepath_route
        uri: https://example.org
        predicates:
        - Path=/red/**
        filters:
        - RewritePath=/red/?(?<segment>.*), /$\{segment}
```

对于 /red/blue 的请求路径，这会在发出下游请求之前将路径设置为 /blue。 请注意，由于 YAML 规范，$ 应替换为 $\。

## SetPath GatewayFilter Factory

SetPath GatewayFilter 工厂采用路径模板参数。 它提供了一种通过允许路径的模板化段来操作请求路径的简单方法。 这使用了 Spring Framework 中的 URI 模板。 允许多个匹配段。 以下示例配置 SetPath GatewayFilter：

Example 43. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: setpath_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment}
        filters:
        - SetPath=/{segment}
```

对于 /red/blue 的请求路径，这会在发出下游请求之前将路径设置为 /blue。

## SetStatus GatewayFilter Factory

SetStatus GatewayFilter 工厂只有一个参数 status。 它必须是有效的 Spring `HttpStatus`。 它可能是整数值 `404` 或枚举的字符串表示形式：`NOT_FOUND`。 以下清单配置了 SetStatus GatewayFilter： 

Example 48. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: setstatusstring_route
        uri: https://example.org
        filters:
        - SetStatus=BAD_REQUEST
      - id: setstatusint_route
        uri: https://example.org
        filters:
        - SetStatus=401
```

无论哪种情况，响应的 HTTP 状态都设置为 401。

## StripPrefix GatewayFilter Factory

StripPrefix GatewayFilter 工厂只有一个参数，`parts`。 `parts`部数指在将请求发送到下游之前要从请求中剥离的路径中的部分数。 以下清单配置了 StripPrefix GatewayFilter： 

Example 50. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: nameRoot
        uri: https://nameservice
        predicates:
        - Path=/name/**
        filters:
        - StripPrefix=2
```

当通过网关向 /name/blue/red 发出请求时，对 nameservice/name/blue/red 发出的请求看起来像 nameservice/red。 

----------

# Global Filters(全局过滤器)

GlobalFilter 接口与 GatewayFilter 具有相同的签名。 这些是有条件地应用于所有路由的特殊过滤器。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/gateway/%20GlobalFilters.png)

## 组合全局过滤器和网关过滤器排序

当请求与路由匹配时，过滤 Web 处理程序会将 GlobalFilter 的所有实例和 GatewayFilter 的所有特定于路由的实例添加到过滤器链中。 这个组合过滤器链由 org.springframework.core.Ordered 接口排序，您可以通过实现 getOrder() 方法设置该接口。

由于 Spring Cloud Gateway 区分过滤器逻辑执行的“pre”和“post”阶段（参见 How it Works），具有最高优先级的过滤器是“pre”阶段的第一个，“post”阶段的最后一个—— 阶段。

以下例子配置了过滤器链：

Example 55. ExampleConfiguration.java

```java
@Bean
public GlobalFilter customFilter() {
    return new CustomGlobalFilter();
}

public class CustomGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("custom global filter");
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```

-----------------

## ReactiveLoadBalancerClientFilter

ReactiveLoadBalancerClientFilter 在名为 ServerWebExchangeUtils.GATEWAY_REQUEST_URL_ATTR 的交换属性中查找 URI。 如果 URL 具有 lb 方案（例如 lb://myservice），则它使用 Spring Cloud ReactorLoadBalancer 将名称（在此示例中为 myservice）解析为实际主机和端口，并替换同一属性中的 URI。 未修改的原始 URL 将附加到 ServerWebExchangeUtils.GATEWAY_ORIGINAL_REQUEST_URL_ATTR 属性中的列表中。 过滤器还会查看 ServerWebExchangeUtils.GATEWAY_SCHEME_PREFIX_ATTR 属性以查看它是否等于 lb。如果是，则应用相同的规则。 以下清单配置了一个 ReactiveLoadBalancerClientFilter：

Example 56. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: myRoute
        uri: lb://service
        predicates:
        - Path=/service/**
```

> ```ABAP
> 默认情况下，当 ReactorLoadBalancer 找不到服务实例时，会返回 503。 您可以通过设置 spring.cloud.gateway.loadbalancer.use404=true 将网关配置为返回 404。
> ```

> ```ABAP
> 从 ReactiveLoadBalancerClientFilter 返回的 ServiceInstance 的 isSecure 值会覆盖向网关发出的请求中指定的方案。 例如，如果请求通过 HTTPS 进入网关，但 ServiceInstance 指示它不安全，则通过 HTTP 发出下游请求。 相反的情况也可以适用。 但是，如果在网关配置中为路由指定了 GATEWAY_SCHEME_PREFIX_ATTR，则前缀将被剥离，并且来自路由 URL 的结果方案将覆盖 ServiceInstance 配置。
> ```

## Netty Routing Filter

如果位于 ServerWebExchangeUtils.GATEWAY_REQUEST_URL_ATTR 交换属性中的 URL 具有 http 或 https 方案，则 Netty 路由过滤器运行。 它使用 Netty HttpClient 发出下游代理请求。 响应放在 ServerWebExchangeUtils.CLIENT_RESPONSE_ATTR 交换属性中，以供稍后过滤器使用。 （还有一个实验性的 WebClientHttpRoutingFilter 执行相同的功能但不需要 Netty。）

## Netty Write Response Filter

如果 ServerWebExchangeUtils.CLIENT_RESPONSE_ATTR 交换属性中存在 Netty HttpClientResponse，则 NettyWriteResponseFilter 运行。 它在所有其他过滤器完成后运行，并将代理响应写回网关客户端响应。 （还有一个实验性的 WebClientWriteResponseFilter 可以执行相同的功能，但不需要 Netty。）

## RouteToRequestUrl Filter

如果 ServerWebExchangeUtils.GATEWAY_ROUTE_ATTR 交换属性中存在 Route 对象，则 RouteToRequestUrlFilter 运行。 它基于请求 URI 创建一个新的 URI，但使用 Route 对象的 URI 属性进行更新。 新 URI 放置在 ServerWebExchangeUtils.GATEWAY_REQUEST_URL_ATTR 交换属性中。

如果 URI 具有方案前缀，例如 lb:ws://serviceid，则 lb 方案将从 URI 中剥离并放置在 ServerWebExchangeUtils.GATEWAY_SCHEME_PREFIX_ATTR 中，以便稍后在过滤器链中使用。

## Websocket Routing Filter

如果位于 ServerWebExchangeUtils.GATEWAY_REQUEST_URL_ATTR 交换属性中的 URL 具有 ws 或 wss 方案，则 websocket 路由过滤器运行。 它使用 Spring WebSocket 基础结构向下游转发 websocket 请求。

您可以通过在 URI 前加上 lb 来对 websockets 进行负载平衡，例如 lb:ws://serviceid。

> ```ABAP
> 如果你使用 SockJS 作为普通 HTTP 的后备，你应该配置一个普通的 HTTP 路由以及 websocket 路由。
> ```

The following listing configures a websocket routing filter:

Example 57. application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
      # SockJS route
      - id: websocket_sockjs_route
        uri: http://localhost:3001
        predicates:
        - Path=/websocket/info/**
      # Normal Websocket route
      - id: websocket_route
        uri: ws://localhost:3001
        predicates:
        - Path=/websocket/**
```

## Gateway Metrics Filter

要启用网关指标，请将 spring-boot-starter-actuator 添加为项目依赖项。 然后，默认情况下，只要属性 spring.cloud.gateway.metrics.enabled 未设置为 false，网关指标过滤器就会运行。 此过滤器添加了一个名为 gateway.requests 的计时器指标，并带有以下标签：

routeId：路由ID。

routeUri：API 路由到的 URI。

结果：按 HttpStatus.Series 分类的结果。

status：返回给客户端的请求的 HTTP 状态。

httpStatusCode：返回给客户端的请求的 HTTP 状态。

httpMethod：用于请求的 HTTP 方法。

然后可以从 /actuator/metrics/gateway.requests 抓取这些指标，并且可以轻松地与 Prometheus 集成以创建 Grafana 仪表板。

> ```ABAP
> 要启用 prometheus 端点，请将 micrometer-registry-prometheus 添加为项目依赖项。
> ```


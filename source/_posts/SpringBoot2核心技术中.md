---
title: SpringBoot2核心技术中
date: 2021-07-20 15:51:44
tags: [Java,SpringBoot,spring]
categories: SpringBoot
---

# 配置文件

## properties

- SpringBoot自动加载特地目录下的application.properties配置文件，在实际的使用中，一般放在resources文件夹下。
- application.properties采用`key=value`配置形式

SpringBoot的设计思想是**约定大于配置**，让开发人员从Spring繁琐的XML配置中解放出来。当然springboot 还通过Java Config。`properties`和`yml`是SpringBoot项目常用的两种文件配置方式。SpringBoot项目默认加载以下目录下的application.properties文件。在实际的使用中，一般放在resources文件夹下。

```
参考资料
SpringApplication loads properties from application.properties files in the following locations and adds them to the Spring Environment:
A /config subdirectory of the current directory The current directory A classpath /config package The classpath root
```

## yaml

YAML 是 "YAML Ain't Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。 

非常适合用来做以数据为中心的配置文件。

### 基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，''与""表示字符串内容 会被 转义/不转义

### 数据类型

●字面量：单个的、不可再分的值。date、boolean、string、number、null

```yaml
k: v
```

●对象：键值对的集合。map、hash、set、object 

```
行内写法：  k: {k1:v1,k2:v2,k3:v3}
#或
k: 
  k1: v1
  k2: v2
  k3: v3
```

●数组：一组按次序排列的值。array、list、queue

```
行内写法：  k: [v1,v2,v3]
#或者
k:
 - v1
 - v2
 - v3
```

```java
@Data
public class Person {
  
  private String userName;
  private Boolean boss;
  private Date birth;
  private Integer age;
  private Pet pet;
  private String[] interests;
  private List<String> animal;
  private Map<String, Object> score;
  private Set<Double> salarys;
  private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
  private String name;
  private Double weight;
}
```

```yaml
# yaml表示以上对象
person:
  userName: zhangsan
  boss: false
  birth: 2019/12/12 20:12:33
  age: 18
  pet: 
    name: tomcat
    weight: 23.4
  interests: [篮球,游泳]
  animal: 
    - jerry
    - mario
  score:
    english: 
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: {first: 128,second: 136}
  salarys: [3999,4999.98,5999.99]
  allPets:
    sick:
      - {name: tom}
      - {name: jerry,weight: 47}
    health: [{name: mario,weight: 47}]
```

### 配置提示



自定义的类和配置文件绑定一般没有提示。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-configuration-processor</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

--------------------

# WEB开发

## SpringMVC自动配置概览

Spring Boot provides auto-configuration for Spring MVC that works well with most applications.(大多场景我们都无需自定义配置)
The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of ContentNegotiatingViewResolver and BeanNameViewResolver beans.

  ○内容协商视图解析器和BeanName视图解析器

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).
  ○静态资源（包括webjars）

- Automatic registration of Converter, GenericConverter, and Formatter beans.
  ○自动注册 Converter，GenericConverter，Formatter 

- Support for HttpMessageConverters (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).
  ○支持 HttpMessageConverters （后来我们配合内容协商理解原理）

- Automatic registration of MessageCodesResolver (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).
  ○自动注册 MessageCodesResolver （国际化用）

- Static index.html support.
  ○静态index.html 页支持

- Custom Favicon support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).
  ○自定义 Favicon 

-  Automatic use of a ConfigurableWebBindingInitializer bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).
  ○自动使用 ConfigurableWebBindingInitializer ，（DataBinder负责将请求数据绑定到JavaBean上）

> ```
> If you want to keep those Spring Boot MVC customizations and make more MVC customizations (interceptors, formatters, view controllers, and other features), you can add your own @Configuration class of type WebMvcConfigurer but without @EnableWebMvc.
> 不用@EnableWebMvc注解。使用 @Configuration + WebMvcConfigurer 自定义规则
> ```

> ```
> If you want to provide custom instances of RequestMappingHandlerMapping, RequestMappingHandlerAdapter, or ExceptionHandlerExceptionResolver, and still keep the Spring Boot MVC customizations, you can declare a bean of type WebMvcRegistrations and use it to provide custom instances of those components.
> 声明 WebMvcRegistrations 改变默认底层组件
> ```

> ```
> If you want to take complete control of Spring MVC, you can add your own @Configuration annotated with @EnableWebMvc, or alternatively add your own @Configuration-annotated DelegatingWebMvcConfiguration as described in the Javadoc of @EnableWebMvc.
> 使用 @EnableWebMvc+@Configuration+DelegatingWebMvcConfiguration 全面接管SpringMVC
> ```

-----------

## **静态资源访问**

### 静态资源访问

> ```
> By default, Spring Boot serves static content from a directory called /static (or /public or
> /resources or /META-INF/resources) in the classpath.
> ```

只要静态资源放在类路径下： called /static (or /public or /resources or /META-INF/resources

访问 ： 当前项目根路径/ + 静态资源名 

原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面

改变默认的静态资源路径

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
#改变默认的静态资源路径
  resources:
    static-locations: [classpath:/haha/]
```

### 静态资源访问前缀

默认无前缀

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + res + 静态资源名 = 静态资源文件夹下找

## 欢迎页

> ```
> Spring Boot supports both static and templated welcome pages. It first looks for an index.html file in
> the configured static content locations. If one is not found, it then looks for an index template. If
> either is found, it is automatically used as the welcome page of the application.
> ```

静态资源路径下  index.html

- 可以配置静态资源路径
- 但是不可以配置静态资源的访问前缀。否则导致 index.html不能被默认访问

## 自定义Favicon

favicon.ico 放在静态资源目录下即可。

## 请求参数处理

### 普通参数与基本注解

注解：
@PathVariable、@RequestHeader、@RequestParam、@CookieValue、@RequestBody

| 注解           | 作用                                                         |
| :------------- | :----------------------------------------------------------- |
| @PathVariable  | 通过 @PathVariable 可以将URL中占位符参数{xxx}绑定到处理器类的方法形参中@PathVariable(“xxx“) |
| @RequestHeader | 是获取请求头中的数据，通过指定参数 value 的值来获取请求头中指定的参数值。** |
| @RequestParam  | @RequestParam主要用于将请求参数区域的数据映射到控制层方法的参数上 |
| @CookieValue   | @CookieValue注解主要是将请求的Cookie数据，映射到功能处理方法的参数上。 |
| @RequestBody   | 主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的)；GET方式无请求体，所以使用@RequestBody接收数据时，前端不能使用GET方式提交数据，而是用POST方式进行提交。 |

#### @PathVariable

```java
@GetMapping("/car/{id}/owner/{username}")
public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                 @PathVariable("username") String name,
                                 @PathVariable Map<String,String> pv,
                                 @RequestHeader("User-Agent") String userAgent,
                                 @RequestHeader Map<String,String> header,
                                 @RequestParam("age") Integer age,
                                 @RequestParam("inters") List<String> inters,
                                 @RequestParam Map<String,String> params,
                                 @CookieValue("_ga") String _ga,
                                 @CookieValue("_ga") Cookie cookie){

    Map<String,Object> map = new HashMap<>();

//        map.put("id",id);
//        map.put("name",name);
//        map.put("pv",pv);
//        map.put("userAgent",userAgent);
//        map.put("headers",header);
    map.put("age",age);
    map.put("inters",inters);
    map.put("params",params);
    map.put("_ga",_ga);
    System.out.println(cookie.getName()+"===>"+cookie.getValue());
    return map;
}
```


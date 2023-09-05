---
title: Spring核心基础
date: 2021-10-22 10:52:05
tags: 
- JavaWeb
- Spring
- Spring基础
categories: Spring
---

# Spring Bean命名策略

##  概述

当我们有多个相同类型的实现时，命名<code>Spring bean</code>非常有用。这是因为如果我们的 bean 没有唯一的名称，Spring 将不明确注入 bean。

通过控制 bean 的命名，我们可以告诉 Spring 我们要将哪个 bean 注入目标对象。

在本文中，我们将讨论 Spring bean 命名策略，并探讨如何为单一类型的 bean 赋予多个名称。

## 默认 Bean 命名策略

Spring为创建 bean提供了多个注解。我们可以在不同级别使用这些注解。例如，我们可以在 bean 类上放置一些注解，而在创建 bean 的方法上放置其他注解。

首先，让我们看看 Spring 的默认命名策略。当我们只指定注解而没有任何值时，Spring 如何命名我们的 bean？

### 类级注解

让我们从在类级别使用的注解的默认命名策略开始。为了给 bean 命名，<span style="color:#b30049;font-weight: bold">Spring 使用类名并将第一个字母转换为小写</span>。

我们来看一个例子：

```java
@Service
public class LoggingService {
}
```

在这里，Spring 为<code>LoggingService</code>类创建了一个 bean，并使用名称<code>loggingService</code>注册它。

这个相同的默认命名策略适用于用于创建 Spring bean 的所有类级注解，例如<span style="color:blue;font-weight:bold">@Component</span>、<span style="color:blue;font-weight:bold">@Service</span>和<span style="color:blue;font-weight:bold">@Controller</span>。

### 方法级注解

Spring 提供了诸如<code>@Bean</code>和<code>@Qualifier</code>之类的注解，用于在方法上创建 bean 。

让我们看一个例子来理解<code>@Bean</code>注解的默认命名策略：

```java
@Configuration
public class AuditConfiguration {
    @Bean
    public AuditService audit() {
          return new AuditService();
    }
}
```

在这个配置类中，Spring在名称“ *audit* ”下注册了一个<code>AuditService</code>类型的 bean，**因为当我们在方法上使用@Bean注解时，Spring 使用方法名称作为 bean 名称。**

我们也可以在方法上使用<code>@Qualifier</code>注解。

## Bean 的自定义命名

当我们需要在同一个 Spring 上下文中创建多个相同类型的 bean 时，我们可以为 bean 指定自定义名称并使用这些名称引用它们。

那么，让我们看看如何为我们的 Spring bean 指定一个自定义名称：

### 类级注解

```java
@Component("myBean")
public class MyCustomComponent {
}
```

这一次，Spring 将创建名为“ *myBean* ”的*MyCustomComponent*类型的 bean 。

由于我们明确地为 bean 指定名称，Spring 将使用此名称，然后可以使用该名称来引用或访问 bean。

与<code>@Component(“myBean”)</code>类似，我们可以使用<code>@Service(“myService”)</code>、<code>@Controller(“myController”)</code>和<code>@Bean(“myCustomBean”)</code>等其他注解指定名称，然后Spring 会注册具有给定名称的那个 bean。

### 方法级注解

正如我们之前看到的，<code>@Bean</code>注解是在方法级别应用的，默认情况下，Spring 使用方法名称作为 bean 名称。

这个默认的 bean 名称可以被覆盖——我们可以使用<code>@Bean</code>注解指定值：

```
@Configuration
public class MyConfiguration {
    @Bean("beanComponent")
    public MyCustomComponent myComponent() {
        return new MyCustomComponent();
    }
}
```

在这种情况下，当我们想要获取一个*MyCustomComponent*类型的 bean 时，我们可以使用名称“ *beanComponent* ”来引用这个 bean 。

Spring <code>@Bean</code>注解通常在配置类方法中声明。它可以通过直接调用来引用同一个类中的其他<code>@Bean</code>方法。

## 总结

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring%E6%A0%B8%E5%BF%83%E5%9F%BA%E7%A1%80/beannamestrategy.png)

------------------

# Spring ApplicationContext

## *ApplicationContext*接口

Spring 框架的主要特性之一是<code>IoC</code>（控制反转）容器。在Spring <code>IoC</code>容器负责管理应用程序的对象。它使用依赖注入来实现控制反转。

<code>BeanFactory</code>和<code>ApplicationContext</code> 接口**代表 Spring IoC 容器**。在这里，<code>BeanFactory</code>是访问 Spring 容器的根接口。它提供了管理 bean 的基本功能。

另一方面，<code>ApplicationContext</code> 是<code>BeanFactory</code>的子接口。因此，它提供了<code>BeanFactory</code>的*所有功能*。

此外，它还**提供了** **更多特定于企业的功能**。<code>ApplicationContext</code>的重要特性是**解析消息、支持国际化、发布事件和应用层特定上下文**。这就是我们将其用作默认 Spring 容器的原因。

## 在容器中配置Bean

正如我们所知，<code>ApplicationContext</code>的主要工作是管理 bean。

因此，应用程序必须向<code>ApplicationContext容器</code>提供 bean 配置。一个 Spring bean 配置由一个或多个 bean 定义组成。此外，Spring 支持不同的 bean 配置方式。

### 基于 Java 的配置

首先，我们将从基于 Java 的配置开始，因为它是最新和最受欢迎的 bean 配置方式。它从 Spring 3.0 开始可用。

Java 配置通常在<code>@Configuration</code>类中使用带有 @Bean 注解的方法*。方法上的*@Bean*注解表明该方法创建了一个 Spring bean。此外，用*<code>@Configuration</code>注解的类表示它包含Spring bean 配置。

现在让我们创建一个配置类来将我们的<code>AccountService</code>类定义为 Spring bean：

```java
@Configuration
public class AccountConfig {

  @Bean
  public AccountService accountService() {
    return new AccountService(accountRepository());
  }

  @Bean
  public AccountRepository accountRepository() {
    return new AccountRepository();
  }
}
```

### 基于注解的配置

Spring 2.5 引入了基于注解的配置，作为在 Java 中启用 bean 配置的第一步。

在这种方法中，我们首先通过<code>XML</code>配置启用基于注解的配置。然后我们在 Java 类、方法、构造函数或字段上使用一组注解来配置 bean。这些注解的一些示例是<code>@Component</code>、<code>@Controller</code>、<code>@Service</code>、<code>@Repository</code>、<code>@Autowired</code>和<code>@Qualifier</code>。

值得注意的是，我们也将这些注解用于基于 Java 的配置。另外值得一提的是，Spring 会在每个版本中不断为这些注解添加更多功能。

现在让我们看一个这个配置的简单例子。

首先，我们将创建 XML 配置<code>user-bean-config.xml</code>以启用注解：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
  
  <context:annotation-config/>
  <context:component-scan base-package="com.baeldung.applicationcontext"/>

</beans>
```

在这里，该注解的配置标签启用基于注解的映射。该*组件扫描*标签也告诉Spring到哪里寻找注解类。

其次，我们将创建*UserService*类并使用<code>@Component</code>注解将其定义为 Spring bean ：

```java
@Component
public class UserService {
  // user service code
}
```

### 基于 XML 的配置

最后，让我们看一下基于 XML 的配置。这是在 Spring 中配置 bean 的传统方式。

显然，在这种方法中，我们**在一个 XML 配置文件中**完成所有**bean 映射**。

因此，让我们创建一个 XML 配置文件<code>account-bean-config.xml</code>，并为我们的*AccountService*类定义 bean ：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="
    http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">
	  
  <bean id="accountService" class="com.baeldung.applicationcontext.AccountService">
    <constructor-arg name="accountRepository" ref="accountRepository" />
  </bean>
	
  <bean id="accountRepository" class="com.baeldung.applicationcontext.AccountRepository" />
</beans>
```

## *ApplicationContext 的*类型

Spring 提供了适合不同需求的不同类型的<code>ApplicationContext</code>容器。这些是<code>ApplicationContext</code>接口的实现。那么让我们来看看<code>ApplicationContext</code> 的一些常见类型。

### *AnnotationConfigApplicationContext*

首先我们来看一下Spring 3.0中引入的<code>AnnotationConfigApplicationContext</code>类。它可以采取与注解的类<code>@Configuration</code>，<code>@Component</code>，和JSR-330的元数据作为输入。从Java的配置类中加载上下文定义。

因此，让我们看一个使用<code>AnnotationConfigApplicationContext</code>容器和基于 Java 的配置的简单示例：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AccountConfig.class);
AccountService accountService = context.getBean(AccountService.class);
```

### *AnnotationConfigWebApplicationContext*

适用于 Web 应用下xml文件中加载上下文，使用注解方式加载上下文

当我们 在<code>web.xml</code>文件中配置 Spring 的<code>ContextLoaderListener servlet</code>侦听器或<code>Spring MVC DispatcherServlet</code>时，我们可能会使用这个类。

此外，从 Spring 3.0 开始，我们还可以通过编程方式配置这个应用程序上下文容器。我们需要做的就是实现<code>WebApplicationInitializer</code>接口：

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

  public void onStartup(ServletContext container) throws ServletException {
    AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
    context.register(AccountConfig.class);
    context.setServletContext(container);

    // servlet configuration
  }
}
```

### *XmlWebApplicationContext*

如果我们**在 Web 应用程序中**使用**基于 XML 的配置**，我们可以使用<code>XmlWebApplicationContext</code>类。

事实上，配置这个容器就像<code>AnnotationConfigWebApplicationContext</code>类，也就是说我们可以在<code>web.xml</code>中*配置它*， 或者实现<code>WebApplicationInitializer</code>接口：

```java
public class MyXmlWebApplicationInitializer implements WebApplicationInitializer {

  public void onStartup(ServletContext container) throws ServletException {
    XmlWebApplicationContext context = new XmlWebApplicationContext();
    context.setConfigLocation("/WEB-INF/spring/applicationContext.xml");
    context.setServletContext(container);

    // Servlet configuration
  }
}
```

### *FileSystemXMLApplicationContext*

我们使用<code>FileSystemXMLApplicationContext</code>类**从文件系统**或 URL**加载基于 XML 的 Spring 配置文件**。当我们需要以编程方式加载<code>ApplicationContext</code>时，这个类很有用。一般来说，测试工具和独立应用程序是一些可能的用例。

例如，让我们看看如何创建这个 Spring 容器并为基于 XML 的配置加载 bean：

```java
String path = "C:/myProject/src/main/resources/applicationcontext/account-bean-config.xml";

ApplicationContext context = new FileSystemXmlApplicationContext(path);
AccountService accountService = context.getBean("accountService", AccountService.class);
```

### *ClassPathXmlApplicationContext*

如果我们想**从 classpath 加载 XML 配置文件**，我们可以使用<code>ClassPathXmlApplicationContext</code>类。与*FileSystemXMLApplicationContext*类似*，*它对于测试工具以及嵌入在 JAR 中的应用程序上下文很有用。

那么让我们看一个使用这个类的例子：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationcontext/account-bean-config.xml");
AccountService accountService = context.getBean("accountService", AccountService.class);
```

----------

#  BeanFactory 和 ApplicationContext 的区别

Spring 框架带有两个 IOC 容器—— <code>BeanFactory</code>和<code>ApplicationContext</code>。该*Bean工厂*是IOC容器的最基本的版本，以及*ApplicationContext的*扩展的功能*Bean工厂*。

我们将通过实际示例了解这两个 IOC 容器之间的显着差异。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring%E6%A0%B8%E5%BF%83%E5%9F%BA%E7%A1%80/beanfactoryand%20context.png)

该<code>ApplicationContext</code>的带有先进的功能，包括正朝着企业应用面向好几个，而*Bean工厂*仅预装了基本功能。因此，通常建议使用<code>ApplicationContext</code>的，并且**只有当内存消耗是至关重要的时候，我们才应该使用Bean工厂**。

# @ComponentScan

在这篇文章中，我们将看到<code>@ComponentScan</code>注解可用的不同类型的过滤器选项 。

## @ *ComponentScan*过滤器

默认情况下，使用<code>@Component</code>、<code>@Repository</code>、<code>@Service</code>、<code>@Controller</code>注解的*类*被注册为Spring beans。对于使用<code>@Component</code>注解的自定义注解的*类也是如此*。我们可以通过使用<code>@ComponentScan</code>注解的<code>includeFilters</code>和 <code>excludeFilters</code>参数 来扩展此行为。

**ComponentScan.Filter有五种类型的过滤器：**

- <code>ANNOTATION</code>   按照注解过滤
- <code>ASSIGNABLE_TYPE</code>  按照给定的类型
- <code>ASPECTJ</code>  使用ASPECTJ表达式
- <code>REGEX</code>  正则表达式
- <code>CUSTOM</code>  自定义规则

## *FilterType.ANNOTATION*

例如，假设我们有一个<code>@Animal</code>注解：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Animal { }
```

现在，让我们定义一个 使用*@Animal*的*Elephant*类：

```java
@Animal
public class Elephant { }
```

最后，让我们使用<code>FilterType.ANNOTATION</code>告诉 Spring 扫描 <code>@Animal</code> 注解的*类：扫描仪很好地拾取了我们的*Elephant

```java
@Configuration
@ComponentScan(includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION,
        classes = Animal.class))
public class ComponentScanAnnotationFilterApp { }
```

## *FilterType.ASSIGNABLE_TYPE*

首先，让我们声明*Animal* 接口：

```java
public interface Animal { }
```

再一次，让我们声明我们的*Elephant*类，这次实现*Animal*接口*：*

```java
public class Elephant implements Animal { }
```

让我们声明我们的*Cat*类也实现了*Animal：*

```java
public class Cat implements Animal { }
```

现在，让我们使用*ASSIGNABLE_TYPE*来引导 Spring 扫描*Animal*实现类：

```java
@Configuration
@ComponentScan(includeFilters = @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,
        classes = Animal.class))
public class ComponentScanAssignableTypeFilterApp { }
```

*Cat*和*Elephant 都*被扫描到。

## *FilterType.REGEX*

再一次，让我们声明我们的*Elephant*类。这次没有实现任何接口或使用任何注解进行注解*：*

```java
public class Elephant { }
```

让我们再声明一个类*Cat*：

```java
public class Cat { }
```

现在，让我们声明*Loin*类：

```java
public class Loin { }
```

让我们使用*FilterType*。*REGEX*指示 Spring 扫描与正则表达式<code>.\*[nt]</code>匹配的类*。*我们的正则表达式计算所有包含*nt 的内容：*

```java
@Configuration
@ComponentScan(includeFilters = @ComponentScan.Filter(type = FilterType.REGEX,
        pattern = ".*[nt]"))
public class ComponentScanRegexFilterApp { }
```

这次在我们的测试中，我们将看到 Spring 扫描*Elephant*，而不是*Lion* *：*

## *FilterType.ASPECTJ*

对于这个用例，我们可以重用与上一节相同的三个类。

让我们使用<code>FilterType.ASPECTJ</code>来指示 Spring 扫描与我们的*AspectJ*表达式匹配的类：

```java
@Configuration
@ComponentScan(includeFilters = @ComponentScan.Filter(type = FilterType.ASPECTJ,
  pattern = "com.baeldung.componentscan.filter.aspectj.* "
  + "&& !(com.baeldung.componentscan.filter.aspectj.L* "
  + "|| com.baeldung.componentscan.filter.aspectj.C*)"))
public class ComponentScanAspectJFilterApp { }
```

虽然有点复杂，但我们这里的逻辑希望 bean 的类名中既不以“L”也不以“C”开头，因此我们又得到了*Elephant* s

---

#  @Qualifier

## Autowire 消除歧义的需要

当需要自动注入特定精确的Bean时，<code>@Autowire</code>是一种很好的方式，尽管它很有用，但在某些用例中，仅此注解不足以让 Spring 了解要注入哪个 bean。

**默认情况下，Spring 按类型解析自动装配的条目。**

**如果容器中有多个相同类型的 bean，则框架将抛出NoUniqueBeanDefinitionException**， 表明有多个 bean 可用于自动装配。

让我们想象这样一种情况，其中 Spring 存在两个可能的候选者作为 bean 协作者注入给定实例：

```java
@Component("fooFormatter")
public class FooFormatter implements Formatter {
 
    public String format() {
        return "foo";
    }
}

@Component("barFormatter")
public class BarFormatter implements Formatter {
 
    public String format() {
        return "bar";
    }
}

@Component
public class FooService {
     
    @Autowired
    private Formatter formatter;
}
```

如果我们尝试将*FooService*加载到我们的上下文中，Spring 框架将抛出一个<code>NoUniqueBeanDefinitionException</code>。这是因为**Spring 不知道要注入哪个 bean**。为了避免这个问题，有几种解决方案；该<code>@Qualifier</code>注解就是其中之一。

## @ *Qualifier*注解

通过使用<code>@Qualifier</code>注解，我们可以**消除需要注入哪个bean的问题**。

让我们回顾一下之前的示例，看看我们如何通过包含*@Qualifier*注解来指示我们要使用哪个 bean 来解决问题：

```java
public class FooService {
     
    @Autowired
    @Qualifier("fooFormatter")
    private Formatter formatter;
}
```

通过包含*@Qualifier*注解，连同我们想要使用的具体实现的名称，在这个例子中为*Foo，*我们可以避免当 Spring 找到多个相同类型的 bean 时产生歧义。

我们需要**考虑到要使用的限定符名称是@Component注解中声明的名称。**

请注意，我们也可以在*Formatter*实现类上使用<code>@Qualifier</code>注解，而不是在它们的<code>@Component</code>注解中指定名称，以获得相同的效果：

```java
@Component
@Qualifier("fooFormatter")
public class FooFormatter implements Formatter {
    //...
}

@Component
@Qualifier("barFormatter")
public class BarFormatter implements Formatter {
    //...
}
```

##  *@Qualifier* VS *@Primary*

还有另一个名为<code>@Primary</code>的注解 ，当依赖注入存在歧义时，我们可以使用它来决定注入哪个 bean。

**当存在多个相同类型的 bean 时，**此注解**定义了一个首选项**。除非另有说明，否则将使用与@Primary注解关联的 bean 。

让我们看一个例子：

```java
@Configuration
public class Config {
 
    @Bean
    public Employee johnEmployee() {
        return new Employee("John");
    }
 
    @Bean
    @Primary
    public Employee tonyEmployee() {
        return new Employee("Tony");
    }
}
```

在此示例中，两种方法都返回相同的*Employee*类型。Spring 将注入的 bean 是*tonyEmployee*方法返回的*bean*。这是因为它包含<code>@Primary</code>注解。当我们想要**指定默认情况下应该注入哪个特定类型的 bean**时，此注解很有用。

如果我们在某个注入时需要另一个 bean，我们需要特别指出它。我们可以通过*@Qualifier*注解来做到这一点。例如，我们可以通过使用*@Qualifier*注解来指定我们要使用*johnEmployee*方法返回的 bean 。

值得注意的是，**如果@Qualifier和@Primary注解都存在，那么@Qualifier注解将具有优先权。**基本上，<code>@Primary</code>定义了一个默认值，而<code>@Qualifier</code>则非常具体。

--------------

# 理解 Spring 中的 getBean()

我们将介绍<code>BeanFactory.getBean()</code>方法的不同变体。

简而言之，正如该方法的名称所暗示的那样，**它负责从 Spring 容器中检索 bean 实例**。

## Spring Beans 设置

首先，让我们定义几个 Spring bean 进行测试。我们可以通过多种方式为 Spring 容器提供 bean 定义，但在我们的示例中，我们将使用基于注解的 Java 配置：

```java
@Configuration
class AnnotationConfig {

    @Bean(name = {"tiger", "kitty"})
    @Scope(value = "prototype")
    Tiger getTiger(String name) {
        return new Tiger(name);
    }

    @Bean(name = "lion")
    Lion getLion() {
        return new Lion("Hardcoded lion name");
    }

    interface Animal {}
}
```

我们已经创建了两个 bean。*Lion*具有默认的单例范围。*Tiger*明确设置为原型范围。此外，请注意，我们为将在进一步请求中使用的每个 bean 定义了名称。

## *getBean()* API

*BeanFactory*提供了<code>getBean()</code>方法的<code>五种</code>不同签名

### 按名称检索 Bean

让我们看看如何使用名称检索*Lion* bean 实例：

```java
Object lion = context.getBean("lion");

assertEquals(Lion.class, lion.getClass());
```

在这个变体中，我们提供一个名称，作为回报，如果应用程序上下文中存在具有给定名称的 bean ，我们将获得一个*Object* 类的实例。否则，如果 bean 查找失败，则此实现和所有其他实现都将抛出<code>NoSuchBeanDefinitionException</code>。

主要的缺点是**在检索 bean 后，我们必须将其强制转换为所需的类型**如果返回的 bean 的类型与我们预期的不同，这可能会产生另一个异常。

### 按名称和类型检索 Bean

这里我们需要指定请求的 bean 的名称和类型：

```java
Lion lion = context.getBean("lion", Lion.class);
```

与前一种方法相比，这种方法更安全，因为我们可以立即获取有关类型不匹配的信息。

### 按类型检索 Bean

使用<code>getBean()</code>的第三个变体， 仅指定 bean 类型就足够了：

```java
Lion lion = context.getBean(Lion.class);
```

在这种情况下，我们需要**特别注意一个潜在的模棱两可的结果**

在上面的示例中，由于*Lion*和*Tiger 都*实现了*Animal*接口，因此仅指定类型不足以明确确定结果。因此，我们得到一个<code>NoUniqueBeanDefinitionException</code>。

### 使用构造函数参数按名称检索 Bean

除了 bean 名称，我们还可以传递构造函数参数：

```java
Tiger tiger = (Tiger) context.getBean("tiger", "Siberian");
```

**这个方法有点不同，因为它只适用于具有原型作用域的 bean**。

在单例的情况下，我们将得到一个*BeanDefinitionStoreException*

因为原型 bean 每次从应用程序容器请求时都会返回一个新创建的实例，所以我们可以在调用<code>getBean()</code>时 **即时提供构造函数参数**：

```java
Tiger tiger = (Tiger) context.getBean("tiger", "Siberian");
Tiger secondTiger = (Tiger) context.getBean("tiger", "Striped");

assertEquals("Siberian", tiger.getName());
assertEquals("Striped", secondTiger.getName());
```

正如我们所见，根据我们在请求 bean 时指定为第二个参数的内容，每个*Tiger*获得不同的名称。

### 使用构造函数参数按类型检索 Bean

这个方法类似于最后一个，但我们需要传递类型而不是名称作为第一个参数：

```java
Tiger tiger = context.getBean(Tiger.class, "Shere Khan");

assertEquals("Shere Khan", tiger.getName());
```

与使用构造函数参数按名称检索 bean 类似，**此方法仅适用于具有原型范围的 bean**。

## 使用注意事项

{% note no-icon danger %}

尽管在*BeanFactory*接口中定义了*getBean()*方法，但它最常通过*ApplicationContext*访问*。*通常，我们不想在我们的程序中直接使用getBean()方法。

Bean 应该由容器管理。如果我们想使用其中之一，我们应该依赖依赖注入而不是直接调用*ApplicationContext.getBean()* *。*这样，我们就可以避免将应用程序逻辑与框架相关的细节混合在一起。

{% endnote %}

-----

# @RequestParam 

**简单地说，我们可以使用@RequestParam从请求中提取查询参数、表单参数甚至文件。**

## **一个简单的映射**

假设我们有一个端点`/api/foos`，它接受一个名为`id`的查询参数 ：

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam String id) {
    return "ID: " + id;
}
```

在这个例子中，我们使用<code>@RequestParam</code>来提取`id`查询参数。

一个简单的 GET 请求将调用*getFoos*：

```bash
http://localhost:8080/api/foos?id=abc
----
ID: abc
```

接下来，**让我们看看注解的属性：name、 value、required和defaultValue。**

## **指定请求参数名称**

幸运的是，**我们可以使用name属性配置@RequestParam名称**：

```java
@PostMapping("/api/foos")
@ResponseBody
public String addFoo(@RequestParam(name = "id") String fooId, @RequestParam String name) { 
    return "ID: " + fooId + " Name: " + name;
}
```

我们也可以做 <code>@RequestParam(value = “id”)</code>或者只是<code>@RequestParam(“id”)</code>。

## **可选的请求参数**

默认用<code>@RequestParam</code>注解的方法参数 是必须的。

这意味着如果请求中不存在该参数，我们将收到错误消息：

```bash
GET /api/foos HTTP/1.1
-----
400 Bad Request
Required String parameter 'id' is not present
```

**使用required 属性将@RequestParam配置为可选：**

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam(required = false) String id) { 
    return "ID: " + id;
}
```

在这种情况下，两者：

```bash
http://localhost:8080/api/foos?id=abc
----
ID: abc
```

和

```bash
http://localhost:8080/api/foos
----
ID: null
```

将正确调用该方法。

**当未指定参数时，方法参数绑定到null。**

## **请求参数的默认值**

我们还可以 使用*defaultValue*属性为`@RequestParam`设置默认值：

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam(defaultValue = "test") String id) {
    return "ID: " + id;
}
```

**这就像required=false， 因为用户不再需要提供参数**：

```bash
http://localhost:8080/api/foos
----
ID: test
```

尽管如此，我们仍然可以提供它：

```bash
http://localhost:8080/api/foos?id=abc
----
ID: abc
```

请注意，当我们设置 *defaultValue* 属性时， *required*确实设置为*false*。

##  **映射所有参数**

**我们也可以有多个参数，而无需定义它们的名称**或计数，只需使用*Map*：

```java
@PostMapping("/api/foos")
@ResponseBody
public String updateFoos(@RequestParam Map<String,String> allParams) {
    return "Parameters are " + allParams.entrySet();
}
```

然后将反映发送的任何参数：

```bash
curl -X POST -F 'name=abc' -F 'id=123' http://localhost:8080/api/foos
-----
Parameters are {[name=abc], [id=123]}
```

## **映射多值参数**

单个`@RequestParam`可以有多个值：

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam List<String> id) {
    return "IDs are " + id;
}
```

**Spring MVC 将映射一个逗号分隔的 id 参数**：

```bash
http://localhost:8080/api/foos?id=1,2,3
----
IDs are [1,2,3]
```

**或单独的id参数列表**：

```bash
http://localhost:8080/api/foos?id=1&id=2
----
IDs are [1,2]
```

-------

# @Component vs @Repository 和 @Service 在 Spring 中区别

在大多数典型的应用程序中，我们有不同的层，如数据访问、表示、服务、业务等。

此外，在每一层中，我们都有不同的 bean。为了自动检测这些 bean，**Spring 使用类路径扫描注释**。

然后它在*ApplicationContext 中*注册每个 bean 。

以下是其中一些注释的快速概览：

- <code>@Component</code>是任何 Spring 管理的组件的通用*构造*型。
- <code>@Service</code>在服务层注释类。
- <code>@Repository</code>在持久层注释类，它将充当数据库存储库。

## 有什么不同？

**这些刻板印象之间的主要区别在于它们用于不同的分类。**当我们为自动检测注释一个类时，我们应该使用相应的构造型。

现在让我们更详细地了解它们。

### **@Component**

**我们可以在整个应用程序中使用 @Component 将 bean 标记为 Spring 的托管组件**。`@Component`是任何 Spring 管理的组件的通用构造型。Spring 只会使用`@Component`获取和注册 bean ，一般不会查找<code>@Service</code> 和 <code>@Repository</code>。

它们在*ApplicationContext*中注册，因为它们用<code>@Component</code>作为元注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

}


@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

}
```

{% note success %}

@Service 和 @Repository是@Component的特例，相当于给@Component 起的别名。它们在技术上是相同的，但我们将它们用于不同的目的。
{% endnote %}



### **@Repository**

**@Repository的工作是捕获特定于持久性的异常并将它们作为 Spring 的统一未检查异常之一重新抛出**。

为此，Spring 提供了*PersistenceExceptionTranslationPostProcessor*，我们需要将其添加到我们的应用程序上下文中（如果我们使用 Spring Boot，则已包含）：

```xml
<bean class=
  "org.springframework.dao.annotation.PersistenceExceptionTranslationPostProcessor"/>
```

这个 bean 后处理器向任何用*@Repository*注释的 bean 添加了一个顾问 *。*

### **@Service**

**我们用 @Service 标记 bean 以表明它们持有业务逻辑**。除了在服务层使用之外，这个注解没有任何其他特殊用途。

------------

# Spring 中基于 XML 的注入

让我们从在*pom.xml 中*添加 Spring 的库依赖开始：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.4.RELEASE</version>         
</dependency>
```

##  **依赖注入——概述**

[依赖注入](https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)是一种技术，其中对象的依赖项由外部容器提供。

假设我们有一个依赖于实际处理业务逻辑的服务的应用程序类：

```java
public class IndexApp {
    private IService service;
    // standard constructors/getters/setters
}
```

现在让我们说<code>IService</code>是一个接口：

```java
public interface IService {
    public String serve();
}
```

这个接口可以有多个实现。

让我们快速看一下一个潜在的实现：

```java
public class IndexService implements IService {
    @Override
    public String serve() {
        return "Hello World";
    }
}
```

这里，*IndexApp*是一个依赖于名为*IService*的低级组件的高级组件。

从本质上讲，我们将*IndexApp*与*IService*的特定实现分离，该实现可能因各种因素而异。

## 依赖注入

### **使用属性注入**

让我们看看如何使用基于 XML 的配置将依赖项连接在一起：

```xml
<bean 
  id="indexService" 
  class="com.baeldung.di.spring.IndexService" />    
<bean 
  id="indexApp" 
  class="com.baeldung.di.spring.IndexApp" >
    <property name="service" ref="indexService" />
</bean>    
```

可以看出，我们正在创建一个*IndexService*实例并为其分配一个 id。默认情况下，bean 是单例。此外，我们正在创建*IndexApp*的实例。

在这个 bean 中，我们使用 setter 方法注入另一个 bean。

### **使用构造函数注入**

我们可以使用构造函数注入依赖项，而不是通过 setter 方法注入 bean：

```xml
<bean 
  id="indexApp" 
  class="com.baeldung.di.spring.IndexApp">
    <constructor-arg ref="indexService" />
</bean>    
```

###  **使用静态工厂**

我们也可以注入一个由工厂返回的 bean。让我们创建一个简单的工厂，根据提供的数字返回*IService*的实例：

```java
public class StaticServiceFactory {
    public static IService getNumber(int number) {
        // ...
    }
}
```

现在让我们看看我们如何使用上述实现使用基于 XML 的配置将 bean 注入到*IndexApp 中*：

```xml
<bean id="messageService"
  class="com.baeldung.di.spring.StaticServiceFactory"
  factory-method="getService">
    <constructor-arg value="1" />
</bean>   
  
<bean id="indexApp" class="com.baeldung.di.spring.IndexApp">
    <property name="service" ref="messageService" />
</bean>
```

在上面的例子中，我们使用*factory-method*调用静态*getService*方法来创建一个带有 id *messageService*的 bean ，我们将其注入*IndexApp。*

### **使用工厂方法**

让我们考虑一个实例工厂，它根据提供的数字返回一个*IService*实例。这一次，该方法不是静态的：

```java
public class InstanceServiceFactory {
    public IService getNumber(int number) {
        // ...
    }
}
```

现在让我们看看我们如何使用上面的实现来使用 XML 配置将 bean 注入到*IndexApp 中*：

```xml
<bean id="indexServiceFactory" 
  class="com.baeldung.di.spring.InstanceServiceFactory" />
<bean id="messageService"
  class="com.baeldung.di.spring.InstanceServiceFactory"
  factory-method="getService" factory-bean="indexServiceFactory">
    <constructor-arg value="1" />
</bean>  
<bean id="indexApp" class="com.baeldung.di.spring.IndexApp">
    <property name="service" ref="messageService" />
</bean>
```

在上面的示例中，我们使用*factory-method*调用*InstanceServiceFactory 实例*上的*getService*方法来创建一个带有 id *messageService*的 bean ，我们将其注入*IndexApp*。

## 访问配置的bean：

这是我们如何访问配置的bean：

```java
@Test
public void whenGetBeans_returnsBean() {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("...");
    IndexApp indexApp = applicationContext.getBean("indexApp", IndexApp.class);
    assertNotNull(indexApp);
}
```

-----------------

# 使用带有默认值的 Spring @Value

## **字符串默认值**

让我们看一下为*String*属性设置默认值的基本语法：

```java
@Value("${some.key:my default value}")
private String stringWithDefaultValue;
```

如果<code>some.key</code>无法解析，<code>stringWithDefaultValue</code>将设置为<code>my default value</code>的默认值。

同样，我们可以设置一个零长度的*字符串*作为默认值：

```java
@Value("${some.key:})"
private String stringWithBlankDefaultValue;
```

**基本类型默认值**

要为基本类型（例如*boolean*和*int ）*设置默认值，我们使用文字值：

```java
@Value("${some.key:true}")
private boolean booleanWithDefaultValue;
@Value("${some.key:42}")
private int intWithDefaultValue;
```

如果我们愿意，我们可以通过将类型更改为*Boolean*和*Integer*来使用原始包装器。

## **数组**

我们还可以将逗号分隔的值列表注入数组：

```java
@Value("${some.key:one,two,three}")
private String[] stringArrayWithDefaults;

@Value("${some.key:1,2,3}")
private int[] intArrayWithDefaults;
```

在上面的第一个示例中，值*one*、*two* 和*three作为默认值注入到*stringArrayWithDefaults 中。

## 使用 SpEL 的高级示例

我们还可以使用 SpEL 表达式来获取值。

如果我们有一个名为*priority*的系统属性*，*那么它的值将应用于该字段：

```java
@Value("#{systemProperties['priority']}")
private String spelValue;
```

如果我们还没有定义系统属性，那么将分配*空*值。

为了防止这种情况，我们可以在 SpEL 表达式中提供一个默认值。如果未定义系统属性，我们会为该字段获取*一些默认*值：

```java
@Value("#{systemProperties['unknown'] ?: 'some default'}")
private String spelSomeDefault;
```

此外，我们可以使用来自其他 bean 的字段值。假设我们有一个名为*someBean*的 bean ，其字段*someValue*等于*10*。然后，将*10*分配给该字段：

```java
@Value("#{someBean.someValue}")
private Integer someBeanValue;
```

我们可以操作属性来获取值*列表*，这里是字符串值 A、B 和 C 的列表：

```java
@Value("#{'${listOfValues}'.split(',')}")
private List<String> valuesList;
```

## @Value("${}")和@Value("#{}")的区别

- <code>@Value("${}")</code>主要获取的是配置文件<code> application.yml /application.proterties</code>中的配置信息。
- <code>@Value("#{}")</code> 表示SpEl表达式通常用来获取<code>bean的属性</code>，或者调用bean的某个方法或属性。当然还有可以表示常量

在下面的示例中，我们希望将*some.system.key*设置为系统属性，如果未设置，我们希望使用*我的默认系统属性值* 作为默认值：

```java
@Value("#{systemProperties['some.key'] ?: 'my default system property value'}")
private String spelWithDefaultValue;
```

---------------

# @Autowired vs @Resource vs @Inject 的区别

为了实现依赖注入 DI 而引入，Java 提供<code>javax.annotation.Resource</code> , <code>javax.inject.Inject</code> 注解，Spring 框架提供了 <code>org.springframework.beans.factory.annotation.Autowired</code>。依赖注入（Denpendency Injection，DI）， 控制反转（Inversion of Control, IoC），主要的目的是去除代码耦合。

## 具体解释

| Annotation | Package                                               | Source          |
| :--------- | :---------------------------------------------------- | :-------------- |
| @Autowired | org.springframework.beans.factory.annotation.Autowire | Spring          |
| @Resource  | javax.annotation.Resource                             | Java            |
| @Inject    | javax.inject.Inject                                   | Java 需额外依赖 |

<code>@Autowired</code>: Spring 特有的注解，@Autowired 通过**类型**来注入，比如通过类的类型，或者类的接口来注解 field 或者 constructor。为了防止在项目中实现同一个接口，或者一系列子类，可以使用 @Qualifier 注解来避免歧义。默认情况下 bean 的名字就是 qualifier 的值。 尽管你可以按照约定通过名字来使用 @Autowired 注解，@Autowired 根本上还是**类型驱动**的注入，并且附带可选的语义上的 qualifiers.

<code>@Inject</code>: 该注解基于 [JSR-330](https://jcp.org/en/jsr/detail?id=330), @Inject 注解是 Spring @Autowired 注解的代替品。所以使用 Spring 独有的 @Autowired 注解时，可以考虑选择使用 @Inject. @Autowired 和 @Inject 的不同之处在于是否有<code>required</code>属性，@Inject 没有 required 属性，因此在找不到合适的依赖对象时 inject 会失败，而 @Autowired 可以使用 required=false 来允许 null 注入。

使用 @Inject 需要添加如下依赖：

```xml
<dependency>
	<groupId>javax.inject</groupId>
	<artifactId>javax.inject</artifactId>
	<version>1</version>
</dependency>
```

Advantage of @Inject annotation is that rather than inject a reference directly, you could ask @Inject to inject a Provider. The Provider interface enables, among other things, lazy injection of bean references and injection of multiple instances of a bean. In case we have few implementation of an interface or a subclass we can narrow down the selection using the @Named annotation to avoid ambiguity. @Named annotation works much like Spring’s @Qualifier

<code>@Resource</code>: JDK 1.6 支持注解，[JSR-250](https://jcp.org/en/jsr/detail?id=250) 引入。@Resource 和 @Autowired @Inject 类似，最主要的区别在于寻找存在的 Bean 注入的路径不同。<code>@Resource</code>寻找的优先顺序为

- 1）优先通过名字 (by name)
- 2）其次是类型 (by type)
- 3）再次是 qualifier(by qualifier)

而 <code>@Autowired</code> and <code>@Inject</code> 寻找的顺序为

1. 通过类型寻找
2. 通过 qualifier
3. 最后通过名字寻找

@Resource 如果没有指定 name 属性，当注解标注在 field 上，默认取字段名称作为 bean 名称寻找依赖对象；当标注在属性 setter 方法上，默认取属性名作为 bean 名称寻找依赖。如果没有指定 name 属性，并且按照默认名称找不到依赖对象时，回退到类型装配。

------------------------

# @Autowired

从 Spring 2.5 开始，该框架引入了注解驱动的*依赖注入*。此功能的主要注释是<code>@Autowired</code> *。* **它允许 Spring 解析并将协作 bean 注入到我们的 bean 中。**

## 启用@Autowired注解

Spring 框架支持自动依赖注入。换句话说，**通过在 Spring 配置文件中声明所有 bean 依赖项，Spring 容器可以自动装配协作 bean 之间的关系**。这称为**Spring bean 自动装配**。

要在我们的应用程序中使用基于 Java 的配置，让我们启用注解驱动注入 来加载我们的 Spring 配置：

```java
@Configuration
@ComponentScan("com.baeldung.autowire.sample")
public class AppConfig {}
```

或者，注解主要用于激活 Spring XML 文件中的依赖注入注解。

此外，**Spring Boot 引入了@SpringBootApplication注解**。这个单一的注解等效于使用<code>@Configuration</code>、<code>@EnableAutoConfiguration</code>和 <code>@ComponentScan</code>。

让我们在应用程序的主类中使用这个注解：

```java
@SpringBootApplication
class VehicleFactoryApplication {
    public static void main(String[] args) {
        SpringApplication.run(VehicleFactoryApplication.class, args);
    }
}
```

因此，当我们运行这个 Spring Boot 应用程序时，**它会自动扫描当前包及其子包中的组件**。因此，它将在 Spring 的应用程序上下文中注册它们，并允许我们使用<code>@Autowired</code>注入 bean 。

## 使用@Autowired

启用注解注入后，**我们可以在属性、设置器和构造函数上使用自动装配**。

### **@Autowired用在属性上**

让我们看看如何使用<code>@Autowired</code>注释属性。这消除了对 getter 和 setter 的需要。

首先，让我们定义一个*fooFormatter* bean：

```java
@Component("fooFormatter")
public class FooFormatter {
    public String format() {
        return "foo";
    }
}
```

然后，我们将在字段定义上使用*@Autowired*将此 bean 注入*FooService* bean ：

```java
@Component
public class FooService {  
    @Autowired
    private FooFormatter fooFormatter;
}
```

其结果是，Spring注入*fooFormatter*时*FooService接口*被创建。

### **@Autowired在 Setter 上**

现在让我们尝试在 setter 方法上添加 @Autowired 注释。

在以下示例中，在创建*FooService*时使用*FooFormatter*实例调用 setter 方法：

```java
public class FooService {
    private FooFormatter fooFormatter;
    @Autowired
    public void setFooFormatter(FooFormatter fooFormatter) {
        this.fooFormatter = fooFormatter;
    }
}
```

### **@Autowired在构造函数上**

最后，让我们在构造函数上使用 @Autowired 。

我们将看到Spring 注入了一个*FooFormatter*实例作为*FooService*构造函数的参数：

```java
public class FooService {
    private FooFormatter fooFormatter;
    @Autowired
    public FooService(FooFormatter fooFormatter) {
        this.fooFormatter = fooFormatter;
    }
}
```

## *@Autowired* 可选注入

在构建 bean 时，@ *Autowired*依赖项应该可用。否则，**如果 Spring 无法解析用于连接的 bean，它将抛出异常**。

因此，它会阻止 Spring 容器成功启动，但以下形式除外：

```java
Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: 
No qualifying bean of type [com.autowire.sample.FooDAO] found for dependency: 
expected at least 1 bean which qualifies as autowire candidate for this dependency. 
Dependency annotations: 
{@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

为了解决这个问题，我们需要required解决

```java
public class FooService {
    @Autowired(required = false)
    private FooDAO dataAccessor; 
}
```

--------------------------

# @PropertySource注解

<code>@PropertySource</code>注解是Spring 3.1开始引入的配置类注解。通过<code>@PropertySource</code>注解将<code>properties</code>配置文件中的值存储到Spring的<code>Environment</code>中，<code>Environment</code>接口提供方法去读取配置文件中的值，参数是<code>properties</code>文件中定义的key值。也可以使用<code>@Value</code>注解用<code>${}</code>占位符注入属性

<code>@PropertySource</code>注解的源代码如下所示。

```java
package org.springframework.context.annotation;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.io.support.PropertySourceFactory;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Repeatable(PropertySources.class)
public @interface PropertySource {
	String name() default "";
	String[] value();
	boolean ignoreResourceNotFound() default false;
	String encoding() default "";
	Class<? extends PropertySourceFactory> factory() default PropertySourceFactory.class;
}
```

从<code>@PropertySource</code>的源码可以看出，我们可以通过<code>@PropertySource</code>注解指定多个<code>properties</code>文件，可以使用如下形式进行指定。

```java
@PropertySource(value={"classpath:xxx.properties", "classpath:yyy.properties"})
```

细心的读者可以看到，在<code>@PropertySource</code>注解类的上面标注了如下的注解信息。

```java
@Repeatable(PropertySources.class)
```

看到这里，小伙伴们是不是有种恍然大悟的感觉呢？没错，我们也可以使用<code>@PropertySources</code>注解来指定<code>properties</code>配置文件。

## 通过注解注册一个配置文件

我们可以将此注解与<code>@Configuration</code>注解结合使用：

```java
@Configuration
@PropertySource("classpath:foo.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

另一种注册新属性文件的非常有用的方法是使用占位符，它允许我们**在运行时动态选择正确的文件**：

```java
@PropertySource({ 
  "classpath:persistence-${envTarget:mysql}.properties"
})
...
```

## 定义多个属性位置

根据 Java 8 约定，<code>@PropertySource</code>注释是可重复的。因此，如果我们使用 Java 8 或更高版本，我们可以使用此注释来定义多个属性位置：

```java
@PropertySource("classpath:foo.properties")
@PropertySource("classpath:bar.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

当然，**我们也可以使用@PropertySources注解，指定一个@PropertySource数组。**这适用于任何受支持的 Java 版本，而不仅仅是 Java 8 或更高版本：

```java
@PropertySources({
    @PropertySource("classpath:foo.properties"),
    @PropertySource("classpath:bar.properties")
})
public class PropertiesWithJavaConfig {
    //...
}
```

在任何一种情况下，值得注意的是，在发生属性名称冲突的情况下，最后一个源读取优先。

## 使用/注入属性

**使用@Value注释**注入属性很简单：

```java
@Value( "${jdbc.url}" )
private String jdbcUrl;
```

**我们还可以为属性指定一个默认值：**

```java
@Value( "${jdbc.url:aDefaultUrl}" )
private String jdbcUrl;
```

Spring 3.1 中添加的新*PropertySourcesPlaceholderConfigurer*解析了 bean 定义属性值和@Value注释中的<code>${…}</code> 占位符。

最后，我们可以 **使用Environment API获取属性的值** ：

```java
@Autowired
private Environment env;
...
dataSource.setUrl(env.getProperty("jdbc.url"));
```

## Spring Boot 的属性

在我们进入更高级的属性配置选项之前，让我们花点时间看看 Spring Boot 中的新属性支持。

一般来说，**与标准 Spring 相比**，**这种新支持涉及的配置更少**，这当然是 Boot 的主要目标之一。

### application.properties：默认属性文件

Boot 将其典型的约定应用于属性文件的配置方法。这意味着**我们可以简单地将application.properties文件放在我们的src/main/resources 目录中，它将被自动检测**。然后我们可以像往常一样从它注入任何加载的属性。

因此，通过使用此默认文件，我们不必显式注册*PropertySource* ，甚至不必提供属性文件的路径。

从*2.4.0*版本开始，**Spring Boot 支持使用多文档属性文件**，类似于[YAML](https://yaml.org/spec/1.2/spec.html#id2760395)的设计：

```plaintext
baeldung.customProperty=defaultValue
#---
baeldung.customProperty=overriddenValue
```

请注意，对于属性文件，三个破折号符号前面有一个注释字符 ( *#* )。

### **特定于环境的属性文件**

如果我们需要针对不同的环境，Boot 中有一个内置的机制。

**我们可以简单地在src/main/resources目录下定义一个application-environment.properties文件，然后设置一个具有相同环境名称的 Spring 配置文件。**

例如，如果我们定义一个“暂存”环境，这意味着我们必须定义一个*暂存*配置文件，然后是*application-staging.properties*。

此 env 文件将被加载并**优先于默认属性文件。**注意，默认文件还是会被加载，只是当发生属性冲突时，特定于环境的属性文件优先。**替代方案：YAML 文件**

Spring 还支持 YAML 文件。

所有相同的命名规则都适用于特定于测试、特定于环境和默认属性文件。唯一的区别是文件扩展名和对我们类路径上的[SnakeYAML](https://bitbucket.org/asomov/snakeyaml)库的依赖。

**YAML 特别适合分层属性存储**；以下属性文件：

```plaintext
database.url=jdbc:postgresql:/localhost:5432/instance
database.username=foo
database.password=bar
secret: foo
```

与以下 YAML 文件同义：

```plaintext
database:
  url: jdbc:postgresql:/localhost:5432/instance
  username: foo
  password: bar
secret: foo
```

还值得一提的是，YAML 文件不支持*@PropertySource*注解，所以如果我们需要使用这个注解，它会限制我们使用属性文件。

另一个值得注意的地方是，在 2.4.0 版本中，Spring Boot 改变了从多文档 YAML 文件加载属性的方式。以前，它们的添加顺序基于配置文件激活顺序。然而，在新版本中，框架遵循我们之前为*.properties*文件指出的相同排序规则；在文件中声明较低的属性将简单地覆盖那些较高的属性。

此外，在此版本中，配置文件无法再从配置文件特定文档中激活，从而使结果更清晰、更可预测。
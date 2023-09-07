---
title: SpringBoot-自动装配
date: 2023-09-06 17:35:52
tags:
- JavaWeb
- Spring
- SpringBoot
categories: SpringBoot
---

{% note no-icon default %}

早期的Spring项目需要添加需要配置繁琐的xml,比如MVC、事务、数据库连接等繁琐的配置。Spring Boot的出现就无需这些繁琐的配置，因为Spring Boot基于**约定大于配置**的理念，在项目启动时候，将约定的配置类自动配置到IOC容器里。这些都因为Spring Boot有自动配置的特性。

{% endnote %}

# main方法

<code>Spring Boot</code>都需要创建一个<code>main</code>启动类，而启动类都含有<code>@SpringBootApplication</code>注解，使用<code>Spring Boot</code>零配置就可以运行起来，这就是 <code>Spring Boot</code> 自动装配的能力了。

看看<code>Spring Boot</code>入口——<code>main</code>方法：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class, args);
    }
}
```

# 核心注解

## @SpringBootApplication 注解

`Spring Boot` 启动类上都有一个 `@SpringBootApplication`注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

我们需要关注的有以下几个：

- `@ComponentScan` 组件扫描指令
- `@SpringBootConfiguration`
- `@EnableAutoConfiguration`

从源码声明可以看出，<code>@SpringBootApplication</code>相当于<code>@SpringBootConfiguration</code> + <code>@EnableAutoConfiguration</code> + <code>@ComponentScan</code>，因此我们直接拆开来分析。

## @SpringBootConfiguration

<code>@SpringBootConfiguration</code>是继承自<code>Spring</code>的<code>@Configuration</code>注解，<code>@SpringBootConfiguration</code>作用相当于<code>@Configuration</code>。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
@Indexed
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

Spring 3.0中增加了<code>@Configuration</code>，<code>@Bean</code>。可基<quote>JavaConfig</quote>形式对<quote>Spring</quote>容器中的<code>bean</code>进行更直观的配置。<code>SpringBoot</code>推荐使用基于<code>JavaConfig</code>的配置形式。

基于<quote>xml</quote>配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans  http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
       default-lazy-init="true">
    <bean id="myService" class="..myServiceImpl">
    ...
    </bean>
</beans>
```

基于<code>JavaConfig</code>配置：

```java
public class MyConfiguration {
    @Bean
    public MyService mockService(){
        return new MyServiceImpl();
    }
}
```

总结，<code>@Configuration</code>相当于一个<quote>spring</quote>的<code>xml</code>文件，配合<quote>@Bean</quote>注解，可以在里面配置需要<quote>Spring</quote>容器管理的<code>bean</code>。

------

## @ComponentScan

源码：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    @AliasFor("basePackages")
    String[] value() default {};

    @AliasFor("value")
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};

    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;

    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;

    String resourcePattern() default "**/ *.class";

    boolean useDefaultFilters() default true;

    Filter[] includeFilters() default {};

    Filter[] excludeFilters() default {};

    boolean lazyInit() default false;

    @Retention(RetentionPolicy.RUNTIME)
    @Target({})
    public @interface Filter {
        FilterType type() default FilterType.ANNOTATION;

        @AliasFor("classes")
        Class<?>[] value() default {};

        @AliasFor("value")
        Class<?>[] classes() default {};

        String[] pattern() default {};
    }
}
```

相关属性解释:

- <code>basePackages</code>与<code>value</code>: 指定扫描的包或包数组
- `basePackageClasses`: 指定具体的要扫描的类
- `nameGenerator`: 对应的bean名称的生成器 默认的是`BeanNameGenerator`
- `useDefaultFilters`: 是否对带有`@Component` `@Repository` `@Service` `@Controller`注解的类开启检测，默认是开启的
- `includeFilters`: 通过`includeFilters`加入扫描路径下没有以上注解的类加入spring容器
- `excludeFilters`: 通过`excludeFilters`过滤出不用加入spring容器的类
- `lazyInit`: 扫描到的类是都开启懒加载 ，默认是不开启的

`@ComponentScan`通常与`@Configuration`一起配合使用，用来告诉Spring需要扫描哪些包或类。如果不设值的话默认扫描`@ComponentScan`注解所在类所在的包及其所有子包，所以对于一个Spring Boot项目，一般会把入口类放在顶层目录中，这样就能够保证源码目录下的所有类都能够被扫描到。

----

## @EnableAutoConfiguration 

`@EnableAutoConfiguration`是<quote>SpringBoot</quote>自动配置的核心注解

源码如下：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

其中`@Import({AutoConfigurationImportSelector.class})` 就是自动配置的核心入口。

`@Import({AutoConfigurationImportSelector.class})`可以自定义导入规则，主要就是`AutoConfigurationImportSelector`类的`selectImports`方法来选择需要自动配置的类进行配置。

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
      return NO_IMPORTS;
    } else {
      AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(annotationMetadata);
      return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```

`selectImports`方法中主要是`getAutoConfigurationEntry`这个方法，而`getAutoConfigurationEntry`方法中最主要是`getCandidateConfigurations`这个方法。

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = new ArrayList(SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(),       this.getBeanClassLoader()));
    ImportCandidates.load(AutoConfiguration.class,  this.getBeanClassLoader()).forEach(configurations::add);
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories nor in META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoader == null) {
    classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }

    String factoryTypeName = factoryType.getName();
    return (List)loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());	
}
```

```java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
        Map<String, List<String>> result = (Map)cache.get(classLoader);
        if (result != null) {
            return result;
        } else {
            Map<String, List<String>> result = new HashMap();

            try {
                Enumeration<URL> urls = classLoader.getResources("META-INF/spring.factories");
......
}
```

`SpringFactoriesLoader.loadFactoryNames()`方法主要作用是读取`spring-boot-autoconfigure.jar`包下的`META-INF/spring.factories`中配置可以进行自动配置的类，这些类都是以`JavaConfig`形式进行导入的，在满足了`@Conditional`中定义的加载条件后，Spring会将这些类加载到`IOC`容器中。

`spring.factories`内容如下:

```
# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer

# Auto Configuration Import Listeners
org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener

# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnClassCondition

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\

.... 省略了很多
```

`Spring.factories`定义了哪些类可以被自动配置，每个配置类定了在什么条件下可以被自动配置，并将这些类实例化被Spring容器管理。

这里以`AopAutoConfiguration`为例子，当类路径下存在`EnableAspectJAutoProxy.class`，`Aspect.class`，`Advice.class`等类时才会注入`AopAutoConfiguration`，并且默认是创建`CglibAutoProxyConfiguration`配置。

总的来说：

从上面查看的源码，可以知道`Spring Boot`自动配置主要是`@EnableAutoConfiguration`实现的，`@EnableAutoConfiguration`注解导入`AutoConfigurationImportSelector`类,通过`selectImports`方法调用`SpringFactoriesLoader.loadFactoryNames()`扫描所有含有`META-INF/spring.factories`文件的`jar`包，将所有`jar`包里面的`spring.factories`文件中`@EnableAutoConfiguration`对应的类注入到`IOC`容器中。

`Spring Boot`基于**约定大于配置**的理念，配置如果没有额外的配置的话，就给按照默认的配置使用约定的默认值，按照约定配置到`IOC`容器中，无需开发人员手动添加配置，加快开发效率。


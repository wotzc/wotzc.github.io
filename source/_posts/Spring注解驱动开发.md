---
title: Spring注解驱动开发
date: 2021-07-23 18:44:28
tags:
- JavaWeb
- Spring
- SpringBoot
categories: Spring注解
---

# 容器

## 组件添加

### @ComponentScan 

在配置类上添加<code style="color:#b30049;background-color:#fdf5f5">@ComponentScan</code>注解。该注解默认会扫描该类所在的包下所有的配置类，相当于之前的 <context:component-scan>。<code style="color:#b30049;background-color:#fdf5f5">@ComponentScan</code>注解默认就会装配标识了<code style="color:#b30049;background-color:#fdf5f5">@Controller</code>，<code style="color:#b30049;background-color:#fdf5f5">@Service</code>，<code style="color:#b30049;background-color:#fdf5f5">@Repository</code>，<code style="color:#b30049;background-color:#fdf5f5">@Component</code>注解的类到spring容器中。

basePackages与value: 用于指定包的路径，进行扫描

basePackageClasses：指定扫描类

<code style="color:#b30049;background-color:#fdf5f5">includeFilters</code>：指定某些定义Filter满足条件的组件 FilterType有5种类型如：

- <code style="color:#b30049;background-color:#fdf5f5">ANNOTATION</code>，注解类型(默认)
- <code style="color:#b30049;background-color:#fdf5f5">ASSIGNABLE_TYPE</code>，指定固定类
- <code style="color:#b30049;background-color:#fdf5f5">ASPECTJ</code>， ASPECTJ类型
- <code style="color:#b30049;background-color:#fdf5f5">REGEX</code>，正则表达式
- <code style="color:#b30049;background-color:#fdf5f5">CUSTOM</code>，自定义类型

<code style="color:#b30049;background-color:#fdf5f5">excludeFilters</code>：过滤器，和includeFilters作用刚好相反，用来对扫描的类进行排除的，被排除的类不会被注册到容器中

```java
@ComponentScan(value="com.wotzc",useDefaultFilters=true,
    includeFilters={
        @Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
        @Filter(type=FilterType.ASSIGNABLE_TYPE,classes={UserService2.class})
    })
@Configuration
public class MainScanConfig {
}
```

### @Scope

<code style="color:#b30049;background-color:#fdf5f5">@Scope</code>注解是springIoc容器中的一个作用域，在 Spring IoC 容器中具有以下几种作用域：基本作用域<code style="color:#b30049;background-color:#fdf5f5">singleton</code>（单例）默认值、<code style="color:#b30049;background-color:#fdf5f5">prototype</code>(多例)，<code style="color:#b30049;background-color:#fdf5f5">Web 作用域</code>（reqeust、session、globalsession），<code style="color:#b30049;background-color:#fdf5f5">自定义作用域</code>

```java
@Scope("prototype")//多实例，IOC容器启动创建的时候，并不会创建对象放在容器在容器当中，当你需要的时候，需要从容器当中取该对象的时候，就会创建。@Scope("singleton")//单实例 IOC容器启动的时候就会调用方法创建对象，以后每次获取都是从容器当中拿同一个对象（map当中）。
@Scope("request")//同一个请求创建一个实例
@Scope("session")//同一个session创建一个实例
```

### @Lazy

Spring IoC （ApplicationContext） 容器一般都会在启动的时候实例化所有单实例 bean 。如果我们想要 Spring 在启动的时候延迟加载 bean，即在调用某个 bean 的时候再去初始化，那么就可以使用<code style="color:#b30049;background-color:#fdf5f5">@Lazy</code>注解。

```java
@Lazy
@Bean
public Person person() {
    return new Person("李四", 55);
}
```

### @Conditional

<code style="color:#b30049;background-color:#fdf5f5">@Conditional</code>是Spring4新提供的注解，它的作用是按照一定的条件进行判断，满足条件就给容器注册bean。

<code style="color:#b30049;background-color:#fdf5f5">@Conditional</code>的定义：

```java
//此注解可以标注在类和方法上
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME) 
@Documented
public @interface Conditional {
    
    Class<? extends Condition>[] value();
    
}
```

从代码中可以看到，需要传入一个Class数组，并且需要继承Condition接口：

```java
public interface Condition {

    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);

}
```

Condition是个接口，需要实现<code style="color:#b30049;background-color:#fdf5f5">matches</code>方法，返回true则注入bean，false则不注入。

首先，创建一个WindowsCondition类：

```java
public class WindowsCondition implements Condition {

    /**
     * @param conditionContext:判断条件能使用的上下文环境
     * @param annotatedTypeMetadata:注解所在位置的注释信息
     *
     */
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {

        //获取ioc使用的beanFactory
        ConfigurableListableBeanFactory beanFactory = conditionContext.getBeanFactory();
        //获取类加载器
        ClassLoader classLoader = conditionContext.getClassLoader();
        //获取当前环境信息
        Environment environment = conditionContext.getEnvironment();
        //获取bean定义的注册类
        BeanDefinitionRegistry registry = conditionContext.getRegistry();
        //获得当前系统名
        String property = environment.getProperty("os.name");

        //包含Windows则说明是windows系统，返回true
        if (property.contains("Windows")){
            return true;
        }
        return false;
    }

}
```

matches方法的两个参数的意思在注释中讲述了，值得一提的是，conditionContext提供了多种方法，方便获取各种信息，也是SpringBoot中<code style="color:#b30049;background-color:#fdf5f5">@ConditonalOnXX</code>注解多样扩展的基础。

接着，创建LinuxCondition类：

```java
public class LinuxCondition implements Condition {

    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {

        Environment environment = conditionContext.getEnvironment();
        String property = environment.getProperty("os.name");
        if (property.contains("Linux")){
            return true;
        }
        return false;
    }

}
```

接着就是使用这两个类了，因为此注解可以标注在方法上和类上，

修改BeanConfig

```java
@Configuration
public class BeanConfig {

    //只有一个类时，大括号可以省略
    //如果WindowsCondition的实现方法返回true，则注入这个bean    
    @Conditional({WindowsCondition.class})
    @Bean(name = "bill")
    public Person person1(){
        return new Person("Bill Gates",62);
    }

    //如果LinuxCondition的实现方法返回true，则注入这个bean
    @Conditional({LinuxCondition.class})
    @Bean("linus")
	public Person person2(){
        return new Person("Linus",48);
    }

}
```

### @Bean

{% note no-icon primary %}

Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。 产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。@Bean明确地指示了一种方法，什么方法呢？产生一个bean的方法，并且交给Spring容器管理；从这我们就明白了为啥@Bean是放在方法的注释上了，因为它很明确地告诉被注释的方法，你给我产生一个Bean，然后交给Spring容器，剩下的你就别管了。记住，@Bean就放在方法上，就是让方法去产生一个Bean，然后交给Spring容器。

{% endnote %}

**为什么要有@Bean注解？**

不知道大家有没有想过，用于注册Bean的注解的有那么多个，为何还要出现@Bean注解？

原因很简单：类似<code style="color:#b30049;background-color:#fdf5f5">@Component</code> , <code style="color:#b30049;background-color:#fdf5f5">@Repository</code> , <code style="color:#b30049;background-color:#fdf5f5">@ Controller</code> ,<code style="color:#b30049;background-color:#fdf5f5"> @Service</code> 这些注册Bean的注解存在局限性，只能局限作用于自己编写的类，如果是一个jar包第三方库要加入<code style="color:#b30049;background-color:#fdf5f5">IOC容器</code>的话，这些注解就手无缚鸡之力了，是的，<code style="color:#b30049;background-color:#fdf5f5">@Bean</code>注解就可以做到这一点！当然除了@Bean注解能做到还有<code style="color:#b30049;background-color:#fdf5f5">@Import</code>也能把第三方库中的类实例交给spring管理，而且<code style="color:#b30049;background-color:#fdf5f5">@Import</code>更加方便快捷

@Bean 基本构成：

```java

@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Bean {
 
	@AliasFor("name")
	String[] value() default {};
 
	@AliasFor("value")
	String[] name() default {};
 
	Autowire autowire() default Autowire.NO;
 
	String initMethod() default "";
 
	String destroyMethod() default AbstractBeanDefinition.INFER_METHOD;
 
}
```

**value**： name属性的别名，在不需要其他属性时使用，也就是说value 就是默认值

**name**： 此bean 的名称，或多个名称，主要的bean的名称加别名。如果未指定，则bean的名称是带注解方法的名称。如果指定了，方法的名称就会忽略，如果没有其他属性声明的话，bean的名称和别名可能通过value属性配置

**autowire** ： 此注解的方法表示自动装配的类型，返回一个<code style="color:#b30049;background-color:#fdf5f5">Autowire</code>类型的枚举，装配方式 有三个选项
<code style="color:#b30049;background-color:#fdf5f5">Autowire.NO</code>(默认设置)
<code style="color:#b30049;background-color:#fdf5f5">Autowire.BY_NAME</code>
<code style="color:#b30049;background-color:#fdf5f5">Autowire.BY_TYPE</code>
指定 bean 的装配方式， 根据**名称**和**根据类型**装配， 一般不设置，采用默认即可。autowire的默认值为`No`，默认表示不通过自动装配。

**bean** 的初始化方法， 直接指定方法名称即可，不用带括号

**destroyMethod**: bean 的销毁方法， 在调用 IoC 容器的<code style="color:#b30049;background-color:#fdf5f5">close()</code>方法时，会执行到该属性指定的方法。不过，只是单实例的 bean 才会调用该方法，如果是多实例的情况下，不会调用该方法

----

### @Import

**@Import只能用在类上** ，<code style="color:#b30049;background-color:#fdf5f5">@Import</code>通过快速导入的方式实现把实例加入spring的IOC容器中

@Import的三种用法主要包括：

{% note no-icon primary %}

1、直接填class数组方式
2、ImportSelector方式【重点】
3、ImportBeanDefinitionRegistrar方式

{% endnote %}

**第一种用法：直接填class数组**

语法如下：

```java
@Import({ 类名.class , 类名.class... })
public class TestDemo {

}
```

对应的import的bean都将加入到spring容器中，这些在容器中bean名称是该类的**全类名** 

**第二种用法：ImportSelector方式**

这种方式的前提就是一个类要实现ImportSelector接口，假如我要用这种方法，目标对象是Myclass这个类，分析具体如下：

创建Myclass类并实现ImportSelector接口

```java
public class Myclass implements ImportSelector {

    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
    	// 方法不要返回null，否则会报空指针异常！
        return new String[0];
    }
}
```

分析实现接口的selectImports方法：

{% note success %}

- 1、返回值： 就是我们实际上要导入到容器中的组件全类名
- 2、参数： AnnotationMetadata表示当前被@Import注解给标注的所有注解信息

{% endnote %}

```java
public class Myclass implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        return new String[]{"com.wotzc.Test.TestDemo3"};
    }
}
```

**第三种用法：ImportBeanDefinitionRegistrar方式**

同样是一个接口，类似于第二种ImportSelector用法，相似度80%，只不过这种用法比较自定义化注册，具体如下：

第一步：创建Myclass2类并实现ImportBeanDefinitionRegistrar接口

```java
public class Myclass2 implements ImportBeanDefinitionRegistrar {
//该实现方法默认为空
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
      
    }
}
```

参数分析：

- 第一个参数：annotationMetadata 和之前的ImportSelector参数一样都是表示当前被@Import注解给标注的所有注解信息
- 第二个参数表示用于注册定义一个bean

----

### @FactoryBean

{% note no-icon info %}

该类是SpringIOC容器是创建Bean的一种形式，这种方式创建Bean会有加成方式，融合了简单的工厂设计模式于装饰器模式
Interface to be implemented by objects used within a {@link BeanFactory} which  are themselves factories for individual objects. If a bean implements this  interface, it is used as a factory for an object to expose, not directly as a  bean instance that will be exposed itself.

{% endnote %}

在某些情况下，实例化Bean过程比较复杂，如果按照传统的方式，则需要在<code style="color:#b30049;background-color:#fdf5f5">\<bean></code>中提供大量的配置信息。配置方式的灵活性是受限的，这时采用编码的方式可能会得到一个简单的方案。Spring为此提供了一个<code style="color:#b30049;background-color:#fdf5f5">org.springframework.bean.factory.FactoryBean</code>的工厂类接口，用户可以通过实现该接口定制实例化Bean的逻辑。<code style="color:#b30049;background-color:#fdf5f5">FactoryBean</code>接口对于Spring框架来说占有重要的地位。

```java
public interface FactoryBean<T> {

    //返回的对象实例
    T getObject() throws Exception;
    //Bean的类型
    Class<?> getObjectType();
    //true是单例，false是非单例  在Spring5.0中此方法利用了JDK1.8的新特性变成了default方法，返回true
    boolean isSingleton();
}
```

### @PostConstruct

在bean创建完成并且属性赋值完成，来执行初始化方法

```java

@PostConstruct
public void someMethod(){

}
```

### @PreDestroy

容器移除对象之前调用，<code style="color:#b30049;background-color:#fdf5f5">@PostConstruct</code>和<code style="color:#b30049;background-color:#fdf5f5">@PreDestroy</code>都只能用在方法上

### BeanPostProcessor

- <code style="color:#b30049;background-color:#fdf5f5">BeanPostProcessor</code>也称为Bean后置处理器，它是Spring中定义的接口，在Spring容器的创建过程中（具体为Bean初始化前后）会回调<code style="color:#b30049;background-color:#fdf5f5">BeanPostProcessor</code>中定义的两个方法。BeanPostProcessor的源码如下

```java
public interface BeanPostProcessor {
    Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;

    Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;

}
```

- 其中<code style="color:#b30049;background-color:#fdf5f5">postProcessBeforeInitialization</code>方法会在每一个bean对象的初始化方法调用之前回调；<code style="color:#b30049;background-color:#fdf5f5">postProcessAfterInitialization</code>方法会在每个bean对象的初始化方法调用之后被回调。

## 组件赋值

### @Value

不通过配置文件的注入属性的情况，通过<code style="color:#b30049;background-color:#fdf5f5">@Value</code>将外部的值动态注入到Bean中

使用的情况有：

1. 基本数值

   ```java
   @Value("normal")
   private String normal; // 注入普通字符串
   ```

   

2. 可以写SpEL获取某个bean的属性：#{}

   ```java
   @Value("#{20-2}")
   private Integer age; 
   ```

3. 可以写${}注入配置文件的值

通过@Value将外部配置文件的值动态注入到Bean中。配置文件主要有两类：

- <code style="color:#b30049;background-color:#fdf5f5">application.properties</code>。application.properties在spring boot启动时默认加载此文件
- 自定义属性文件。自定义属性文件通过<code style="color:#b30049;background-color:#fdf5f5">@PropertySource</code>加载。@PropertySource可以同时加载多个文件，也可以加载单个文件。

```
# 自己配置的参数
savePath : /Users/a/Desktop/test998/
libraryPath : /opt/local/share/OpenCV/java/libopencv_java347.dylib
```

```java
@Service
public class TesseractOrcServiceImpl implements TesseractOrcService {

    @Value("${savePath}")
    private  String savePath ;
}
```

### @Autowired

<code style="color:#b30049;background-color:#fdf5f5">@autowired</code>注释来源于英文单词 autowire,这个单词的意思是自动装配的意思。自动装配又是什么意思？这个词语本来的意思是指的一些工业上的用机器代替人口，自动将一些需要完成的组装任务，或者别的一些任务完成。而在 Spring 的世界当中，自动装配指的就是使用将 Spring 容器中的 bean 自动的和我们需要这个 bean 的类组装在一起。

<code style="color:#b30049;background-color:#fdf5f5">@Autowired</code>是用在JavaBean中的注解，通过byType形式，用来给指定的字段或方法注入所需的外部资源

自动装备一定要将属性赋值好，没有就会报错。

默认情况下，<code style="color:#b30049;background-color:#fdf5f5">@Autowired</code>注释意味着依赖是必须的，它类似于<code style="color:#b30049;background-color:#fdf5f5">@Required</code>注释，然而，你可以使用<code style="color:#b30049;background-color:#fdf5f5">@Autowired</code>的 **（required=false）** 选项关闭默认行为。

即使你不为 age 属性传递任何参数，下面的示例也会成功运行，

```java
package com.tutorialspoint;
import org.springframework.beans.factory.annotation.Autowired;
public class Student {
   private Integer age;
   private String name;
   @Autowired(required=false)
   public void setAge(Integer age) {
      this.age = age;
   }  
   public Integer getAge() {
      return age;
   }
   @Autowired
   public void setName(String name) {
      this.name = name;
   }   
   public String getName() {
      return name;
   }
}
```

**@Autowired**可以标注在方法、参数、构造器、属性上，都是从容器中获取参数组件的值

标在构造器上，如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略

@Bean标注的方法创建对象的时候，方法参数的值从容器中获取

### @Qualifier

可能会有这样一种情况，当你创建多个具有相同类型的 bean 时，并且想要用一个属性只为它们其中的一个进行装配，在这种情况下，你可以使用 **@Qualifier** 注释和 **@Autowired** 注释通过指定哪一个真正的 bean 将会被装配来消除混乱。

### @Primary

当一个接口有2个不同实现时,使用@Autowired注解时会报<code style="color:#b30049;background-color:#fdf5f5">org.springframework.beans.factory.NoUniqueBeanDefinitionException</code>异常信息。

Primary可以理解为默认优先选择,不可以同时设置多个,内部实质是设置BeanDefinition的primary属性

| 注解         | 备注                                                         |
| :----------- | :----------------------------------------------------------- |
| `@Primary`   | 优先方案，被注解的实现，优先被注入                           |
| `@Qualifier` | 先声明后使用，相当于多个实现起多个不同的名字，注入时候告诉我你要注入哪个 |

### @Resource

可以和@Autowired一样实现自动装配，默认是按组件名称进行装配的

@Resource 注释使用一个 ‘name’ 属性，该属性以一个 bean 名称的形式被注入。你可以说，它遵循 **by-name** 自动连接语义

如果没有明确地指定一个 ‘name’，默认名称源于字段名或者 setter 方法。在字段的情况下，它使用的是字段名；在一个 setter 方法情况下，它使用的是 bean 属性名称。

### @Inject

- @Inject是JSR330 (Dependency Injection for Java)中的规范，需要导入<code style="color:#b30049;background-color:#fdf5f5">javax.inject.Inject</code>;实现注入。
- @Inject是根据**类型**进行自动装配的，如果需要按名称进行装配，则需要配合@Named；
- @Inject可以作用在变量、setter方法、构造函数上。

<code style="color:#b30049;background-color:#fdf5f5">@Resource</code>,<code style="color:#b30049;background-color:#fdf5f5">@Autowired</code>,<code style="color:#b30049;background-color:#fdf5f5">@Inject</code>这3种都是用来注入bean的，它们属于不同的程序中。

![img](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring%E6%B3%A8%E8%A7%A3%E9%A9%B1%E5%8A%A8/autowiredpng.png)

JSR是Java Specification Requests的缩写，意思是Java 规范提案。是指向JCP(Java Community Process)提出新增一个标准化技术规范的正式请求。任何人都可以提交JSR，以向Java平台增添新的API和服务。JSR已成为Java界的一个重要标准。

**@Resource** 没有支持@Primary功能；没有支持@Autowired(required = false)；

**@Inject** 需要导入javax.inject的包；没有支持@Autowired(required = false)；

### @Profile

指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

 1) 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境

 2) 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效

```java
	@Value("${db.user}")
	private String user;
	
	private String driverClass;
	
	@Profile("default")
	@Bean("test")
	public DataSource testDataSource(@Value("${db.password}")String password) throws PropertyVetoException {
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(password);
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	
	@Profile("dev")
	@Bean("dev")
	public DataSource devDataSource(@Value("${db.password}")String password) throws PropertyVetoException {
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(password);
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	
	@Profile("master")
	@Bean("master")
	public DataSource masterDataSource(@Value("${db.password}")String password) throws PropertyVetoException {
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(password);
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}

```

切换运行环境的方式：

```java

public class IOCTestProfile {
	//1. 使用命令行动态参数：在虚拟机参数位置加载 -Dspring.profiles.active=test
	//2. 使用代码的方式激活某种环境；
	@Test
	public void test01() {
		AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfigOfProfile.class);
		//1. 创建一个applicationContext
		//2. 设置需要激活的环境
		applicationContext.getEnvironment().setActiveProfiles("dev","master");
		//3. 注册主配置类
		applicationContext.register(MainConfigOfProfile.class);
		//4. 启动刷新容器
		applicationContext.refresh();
		
		String[] beanNamesForType = applicationContext.getBeanNamesForType(DataSource.class);
		System.out.println(Arrays.toString(beanNamesForType));
		
		applicationContext.close();
	}

```

------

## AOP

### @EnableAspectJAutoProxy

开启AOP功能

### @Pointcut

通过<code style="color:#b30049;background-color:#fdf5f5">@Pointcut</code>定义切入点

格式：<code style="color:#b30049;background-color:#fdf5f5">@Pointcut(value=“表达标签(表达式格式) ”)</code>
如：<code style="color:#b30049;background-color:#fdf5f5">@Pointcut (value=“execution(* com.cn.spring.aspectj.NotVeryUsefulAspectService.*(…))”)</code>

**表达式标签**

- <code style="color:#b30049;background-color:#fdf5f5">execution()</code>：用于匹配方法执行的连接点
- <code style="color:#b30049;background-color:#fdf5f5">args()</code>: 用于匹配当前执行的方法传入的参数为指定类型的执行方法
- <code style="color:#b30049;background-color:#fdf5f5">this()</code>: 用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配；
- <code style="color:#b30049;background-color:#fdf5f5">target()</code>: 用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；
- <code style="color:#b30049;background-color:#fdf5f5">within()</code>: 用于匹配指定类型内的方法执行；
- <code style="color:#b30049;background-color:#fdf5f5">@args()</code>:于匹配当前执行的方法传入的参数持有指定注解的执行；
- <code style="color:#b30049;background-color:#fdf5f5">@target()</code>:用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；
- <code style="color:#b30049;background-color:#fdf5f5">@within()</code>:用于匹配所以持有指定注解类型内的方法；
- <code style="color:#b30049;background-color:#fdf5f5">@annotation</code>:用于匹配当前执行方法持有指定注解的方法；

其中<code style="color:#b30049;background-color:#fdf5f5">execution</code>是用的最多的，<code style="color:#b30049;background-color:#fdf5f5">execution</code>格式：

<code style="color:#b30049;background-color:#fdf5f5">execution(modifier-pattern?  ret-type-pattern  declaring-type-pattern?  name-pattern(param-pattern)  throws-pattern?)</code>
其中带<code style="color:#b30049;background-color:#fdf5f5">?</code>号的<code style="color:#b30049;background-color:#fdf5f5">modifiers-pattern?</code>，<code style="color:#b30049;background-color:#fdf5f5">declaring-type-pattern?</code>，<code style="color:#b30049;background-color:#fdf5f5">hrows-pattern?</code>是可选项
<code style="color:#b30049;background-color:#fdf5f5">ret-type-pattern</code>，<code style="color:#b30049;background-color:#fdf5f5">name-pattern</code>， <code style="color:#b30049;background-color:#fdf5f5">parameters-pattern</code>是必选项；

- <code style="color:#b30049;background-color:#fdf5f5">modifier-pattern?</code> **修饰符匹配**，如public 表示匹配公有方法
- <code style="color:#b30049;background-color:#fdf5f5">ret-type-pattern</code> **返回值匹配**，<code style="color:#b30049;background-color:#fdf5f5">*</code> 表示任何返回值，全路径的类名等
- <code style="color:#b30049;background-color:#fdf5f5">declaring-type-pattern?</code> 类路径匹配
- <code style="color:#b30049;background-color:#fdf5f5">name-pattern</code> 方法名匹配，<code style="color:#b30049;background-color:#fdf5f5">\*</code>代表所有,<code style="color:#b30049;background-color:#fdf5f5">set*</code>代表以set开头的所有方法
- <code style="color:#b30049;background-color:#fdf5f5">(param-pattern)</code>参数匹配，指定方法参数(声明的类型),
  <code style="color:#b30049;background-color:#fdf5f5">(..)</code>代表所有参数,
  <code style="color:#b30049;background-color:#fdf5f5">(\*)</code>代表一个参数,
  <code style="color:#b30049;background-color:#fdf5f5">(*,String)</code>代表第一个参数为任何值,第二个为String类型.
- <code style="color:#b30049;background-color:#fdf5f5">throws-pattern?</code> 异常类型匹配

**例子**

- execution(public * *(..)) 定义任意公共方法的执行
- execution(* set*(..)) 定义任何一个以"set"开始的方法的执行
- execution(* com.xyz.service.AccountService.*(..)) 定义AccountService 接口的任意方法的执行
- execution(* com.xyz.service.*.*(..)) 定义在service包里的任意方法的执行
- execution(* com.xyz.service ..*.*(..)) 定义在service包和所有子包里的任意类的任意方法的执行
- execution(* com.test.spring.aop.pointcutexp…JoinPointObjP2.*(…)) 定义在pointcutexp包和所有子包里的JoinPointObjP2类的任意方法的执行：

**AspectJ类型匹配的通配符：**

- <code style="color:#b30049;background-color:#fdf5f5">\*</code>匹配任何数量字符； 
- <code style="color:#b30049;background-color:#fdf5f5">..</code>匹配任何数量字符的重复，如在类型模式中匹配任何数量子包；而在方法参数模式中匹配任何数量参数。
- <code style="color:#b30049;background-color:#fdf5f5">+</code>匹配指定类型的子类型；仅能作为后缀放在类型模式后边 。

如：

- <code style="color:#b30049;background-color:#fdf5f5">java.lang.String</code>匹配String类型；
- <code style="color:#b30049;background-color:#fdf5f5">java.*.String</code>匹配java包下的任何“一级子包”下的String类型；如匹配`java.lang.String`，但不匹配`java.lang.ss.String`
- <code style="color:#b30049;background-color:#fdf5f5">java..*</code> 匹配java包及任何子包下的任何类型; 如匹配`java.lang.String`、`java.lang.annotation.Annotation`
- <code style="color:#b30049;background-color:#fdf5f5">java.lang.*ing</code>匹配任何java.lang包下的以ing结尾的类型；
- <code style="color:#b30049;background-color:#fdf5f5">java.lang.Number+</code>匹配java.lang包下的任何Number的自类型；如匹配java.lang.Integer，也匹配java.math.BigIntege

### @Aspect

 定义切面类

### @Before

用@Before标识的方法为前置方法，在目标方法的执行之前执行，即在连接点之前进行执行。

```java
@Before(value = "pointcut1()")
public void before(JoinPoint joinPoint) {
    //输出连接点的信息
    System.out.println("前置通知，" + joinPoint);
}
```

### @After

后置方法在连接点方法完成之后执行，无论连接点方法执行成功还是出现异常，都将执行后置方法。

### @AfterReturning

当连接点方法成功执行后，返回通知方法才会执行，如果连接点方法出现异常，则返回通知方法不执行。返回通知方法在目标方法执行成功后才会执行，所以，返回通知方法可以拿到目标方法(连接点方法)执行后的结果。

### @AfterThrowing

异常通知方法只在连接点方法出现异常后才会执行，否则不执行。在异常通知方法中可以获取连接点方法出现的异常。在切面类中异常通知方法

```java
@AfterThrowing(value = "pointcut1()", throwing = "e")
public void afterThrowing(JoinPoint joinPoint, Exception e) {
    //发生异常之后输出异常信息
    System.out.println(joinPoint + ",发生异常：" + e.getMessage());
}
```

### @Around

环绕通知方法可以包含上面四种通知方法，环绕通知的功能最全面。环绕通知需要携带<code style="color:#b30049;background-color:#fdf5f5">ProceedingJoinPoint</code>类型的参数，且环绕通知必须有返回值, 返回值即为目标方法的返回值。在切面类中创建环绕通知方法，示例如下：

```java
@Around("execution(public int lzj.com.spring.aop.ArithmeticCalculator.*(int, int))")
    public Object aroundMethod(ProceedingJoinPoint pdj){
        /*result为连接点的放回结果*/
        Object result = null;
        String methodName = pdj.getSignature().getName();

        /*前置通知方法*/
        System.out.println("前置通知方法>目标方法名：" + methodName + ",参数为：" + Arrays.asList(pdj.getArgs()));

        /*执行目标方法*/
        try {
            result = pdj.proceed();

            /*返回通知方法*/
            System.out.println("返回通知方法>目标方法名" + methodName + ",返回结果为：" + result);
        } catch (Throwable e) {
            /*异常通知方法*/
            System.out.println("异常通知方法>目标方法名" + methodName + ",异常为：" + e);
        }

        /*后置通知*/
        System.out.println("后置通知方法>目标方法名" + methodName);

        return result;
    }
}
```

-------

## 声明式事务

### @EnableTransactionManagement

开启基于注解的事务管理功能

注解原理：

<code style="color:#b30049;background-color:#fdf5f5">@EnableXXX</code>原理：注解上有个<code style="color:#b30049;background-color:#fdf5f5">XXXRegistrar</code>，或通过<code style="color:#b30049;background-color:#fdf5f5">XXXSelector</code>引入<code style="color:#b30049;background-color:#fdf5f5">XXXRegistrar</code>，<code style="color:#b30049;background-color:#fdf5f5">XXXRegistrar</code>实现了 
<code style="color:#b30049;background-color:#fdf5f5">ImportBeanDefinitionRegistrar</code>的<code style="color:#b30049;background-color:#fdf5f5">registerBeanDefinitions</code>方法，给容器注册<code style="color:#b30049;background-color:#fdf5f5">XXXCreator</code>。这个Creator实现了后置处理器， 后置处理器在对象创建以后，包装对象，返回一个代理对象，代理对象执行方法利用拦截器链进行调用。

```python
* 1）、@EnableTransactionManagement
*        利用TransactionManagementConfigurationSelector给容器中会导入组件
*        导入两个组件
*        AutoProxyRegistrar
*        ProxyTransactionManagementConfiguration
* 2）、AutoProxyRegistrar：
*        给容器中注册一个 InfrastructureAdvisorAutoProxyCreator 组件；
*        利用后置处理器机制在对象创建以后，包装对象，返回一个代理对象（增强器），代理对象执行方法利用拦截器链进行调用；
* 3）、ProxyTransactionManagementConfiguration是个@Configuration
*        1、给容器中注册事务增强器transactionAdvisor；
*           1）、事务增强器要用事务注解的信息，AnnotationTransactionAttributeSource解析事务注解
*           2）、事务拦截器transactionInterceptor：
*              TransactionInterceptor；保存了事务属性信息，事务管理器；
*              TransactionInterceptor是一个 MethodInterceptor；
*              在目标方法执行的时候；
*                 执行拦截器链；
*                 只有事务拦截器：
*                    1）、先获取事务相关的属性
*                    2）、再获取PlatformTransactionManager，如果事先没有添加指定任何transactionmanger
*                       最终会从容器中按照类型获取一个PlatformTransactionManager；
*                    3）、执行目标方法
*                       如果异常，获取到事务管理器，利用事务管理回滚操作；
*                       如果正常，利用事务管理器，提交事务
```



### @Transactional

<code style="color:#b30049;background-color:#fdf5f5">@Target({ ElementType.METHOD, ElementType.TYPE })</code>可以标注在类上，以及方法上

<code style="color:#b30049;background-color:#fdf5f5">@Transactional</code>注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。
系统设计：将标签放置在需要进行事务管理的方法上，而不是放在所有接口实现类上：只读的接口就不需要事务管理，由于配置了<code style="color:#b30049;background-color:#fdf5f5">@Transactional</code>就需要AOP拦截及事务的处理，可能影响系统性能。

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {

	@AliasFor("transactionManager")
	String value() default "";

	@AliasFor("value")
	String transactionManager() default "";

	Propagation propagation() default Propagation.REQUIRED;

	Isolation isolation() default Isolation.DEFAULT;

	int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;

	boolean readOnly() default false;

	Class<? extends Throwable>[] rollbackFor() default {};

	String[] rollbackForClassName() default {};

	Class<? extends Throwable>[] noRollbackFor() default {};

	String[] noRollbackForClassName() default {};

}
```

**propagation属性**：事务的传播行为，一个事务方法被另外一个事务方法调用时，当前的事务如何使用事务.，属性的内容主要先择在一下的Propagation的枚举类中

```

1. TransactionDefinition.PROPAGATION_REQUIRED：
   如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。这是默认值。
 
2. TransactionDefinition.PROPAGATION_REQUIRES_NEW：
   创建一个新的事务，如果当前存在事务，则把当前事务挂起。
 
3. TransactionDefinition.PROPAGATION_SUPPORTS：
   如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
 
4. TransactionDefinition.PROPAGATION_NOT_SUPPORTED：
   以非事务方式运行，如果当前存在事务，则把当前事务挂起。
 
5. TransactionDefinition.PROPAGATION_NEVER：
   以非事务方式运行，如果当前存在事务，则抛出异常。
 
6. TransactionDefinition.PROPAGATION_MANDATORY：
   如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
 
7. TransactionDefinition.PROPAGATION_NESTED：
   如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；
   如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

```

**isolation属性**：事务的隔离级别，也在<code style="color:#b30049;background-color:#fdf5f5">org.springframework.transaction.annotation.Isolation</code>枚举类中

```java

public enum Isolation {
 
	/**数据库的默认级别*/
	DEFAULT(TransactionDefinition.ISOLATION_DEFAULT),
 
	/**读未提交      脏读*/
	READ_UNCOMMITTED(TransactionDefinition.ISOLATION_READ_UNCOMMITTED),
 
	/**读已提交  不可重复读（update）*/
	READ_COMMITTED(TransactionDefinition.ISOLATION_READ_COMMITTED),
 
	/**可重复读      幻读（插入操作）*/
	REPEATABLE_READ(TransactionDefinition.ISOLATION_REPEATABLE_READ),
 
	/** 串行化         效率低*/
	SERIALIZABLE(TransactionDefinition.ISOLATION_SERIALIZABLE);
 
 
	private final int value;
 
	Isolation(int value) { this.value = value; }
 
	public int value() { return this.value; }
 
}
```

**timeout：事物的超时时间，设置事务在强制回滚之前可以占用的时间，默认为-1，不超时，单位为s（测试为单位s）**

**readOnly：是否只读**

1. <code style="color:#b30049;background-color:#fdf5f5">true</code>:  只读 ；代表着只会对数据库进行读取操作， 不会有修改的操作，如果确保当前的事务只有读取操作，就有必要设置为只读，可以帮助数据库，引擎优化事务
2. <code style="color:#b30049;background-color:#fdf5f5">false</code>: 非只读  不仅会读取数据还会有修改操作

## 扩展原理

<code style="color:#b30049;background-color:#fdf5f5">BeanFactoryPostProcessor</code>是<code style="color:#b30049;background-color:#fdf5f5">beanFactory</code>的后置处理器，在BeanFactory标准初始化之后调用，所有的bean定义已经保存加载到beanFactory，但是bean实例还未创建。

### BeanFactoryPostProcessor原理

1. ioc容器创建对象

2. invokeBeanFactoryPostProcessors(beanFactory)

 如何找到所有的<code style="color:#b30049;background-color:#fdf5f5">BeanFactoryPostProcessor</code>并执行他们的方法：

1）直接在BeanFactory中找到所有类型是BeanFactoryPostProcessor的组件，并执行他们的方法

2）在初始化创建其他组件前面执行

### BeanDefinitionRegistryPostProcessor

BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor

```java
public interface BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean definition registry after its
	 * standard initialization. All regular bean definitions will have been loaded,
	 * but no beans will have been instantiated yet. This allows for adding further
	 * bean definitions before the next post-processing phase kicks in.
	 * @param registry the bean definition registry used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException;

}

```

<code style="color:#b30049;background-color:#fdf5f5">postProcessBeanDefinitionRegistry()</code>；在所有bean定义信息将要被加载，bean实例还未创建的时候调用

优于<code style="color:#b30049;background-color:#fdf5f5">BeanFactoryPostProcessor</code>执行

利用<code style="color:#b30049;background-color:#fdf5f5">BeanDefinitionRegistryPostProcessor</code>给容器再额外添加一些组件

原理：

1）ioc创建对象

2）refresh() -》invokeBeanFactoryPostProcessors(beanFactory)

3）从容器或获取到所有的BeanDefinitionRegistryPostProcessor组件。依次触发所有的postProcessBeanDefinitionRegistry()方法 ，再触发postProcessBeanFactory方法BeanFactoryPostProcessor

4）再从容器中找到BeanFactoryPostProcessor组件，然后依次触发postProcessBeanFactory()方法

### ApplicationListener

<code style="color:#b30049;background-color:#fdf5f5">ApplicationListener</code>：监听容器中发布的事件。事件驱动模型开发

```java
public interface ApplicationListener<E extends ApplicationEvent>
```

监听ApplicationEvent及其下面的子事件

步骤：

1）写一个监听器来监听某个事件（ApplicationEvent及其子类）

2）把监听器加入到容器中

3）只要容器中有相关的事件发布，我们就能监听到这个事件

​		<code style="color:#b30049;background-color:#fdf5f5">ContextRefreshedEvent</code>：容器刷新完成（所有Bean都完全创建）会发布这个事件

​		<code style="color:#b30049;background-color:#fdf5f5">ContextClosedEvent</code>：关闭容器会发布这个事件

4）发布一个事件：

​		<code style="color:#b30049;background-color:#fdf5f5">applicationContext.publishEvent();</code>
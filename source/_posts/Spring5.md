---
title: Spring5
date: 2021-07-12 08:54:54
tags:
- 框架
- spring
- Java
categories: JavaWeb
---

## Spring5框架概述

Spring 框架是一个Java平台，它为开发Java应用程序提供全面的基础架构支持。Spring负责基础架构，因此您可以专注于应用程序的开发。

Spring可以让您从“plain old Java objects”（POJO）中构建应用程序和通过非侵入性的POJO实现企业应用服务。

### Spring的两大核心部分：IOC和Aop

1. <code>IOC</code>（Inversion of Control）控制反转，把创建对象过程交给Spring管理
2. <code>Aop</code>（Aspect Oriented Programming）面向切面，在不修改源代码的基础上进行功能增强

### Spring的特点

- 方便解耦，简化开发

- Aop编程支持
- 方便程序测试
- 方便和其他框架进行整合
- 方便进行事务操作
- 降低API开发难度

## IOC

### 什么是IOC

（1）控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理
（2）使用IOC目的：为了耦合度降低

### IOC底层原理

讲解IOC底层原理

第一步 xml配置文件，配置创建的对象

```xml
<bean od="dao" classs="com.wotzc.UserDao"></bean>
```

第二步 有service类和dao类，创建工厂类

```java
class UserFactory {
	public static UserDao getDao() {
		String classValue = class属性值; // xml解析
		Class clazz = Class.forName(classValue); // 通过反射机制创建对象
		return (UserDao) clazz.newInstance;
	}
}	
```

<code>org.springframework.beans</code>和<code>org.springframework.context</code>是Spring框架中IoC容器的基础，<code>BeanFactory</code>接口提供一种高级的配置机制能够管理任何类型的对象。<code>ApplicationContext</code>是<code>BeanFactory</code>的子接口。它能更容易集成Spring的**AOP功能**、**消息资源处理（比如在国际化中使用）**、**事件发布**和**特定的上下文应用层**比如在网站应用中的<code>WebApplicationContext</code>。

总之，<code>BeanFactory</code>提供了配置框架和基本方法，<code>ApplicationContext</code>添加更多的企业特定的功能。<code>ApplicationContext</code>是<code>BeanFactory</code>的一个子接口。

### IOC(BeanFactory)接口

IOC思想基于IOC容器完成，IOC容器底层就是对象工厂。

（1）<code>BeanFactory</code>：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用，加载配置文件时候不会创建对象，在获取对象（使用）才去创建对象。

（2）<code>ApplicationContext</code>：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用，加载配置文件时候就会把在配置文件对象进行创建。

尽量使用<code>ApplicationContext</code>除非你有更好的理由不用它。
因为<code>ApplicationContext</code>包括了<code>BeanFactory</code>的所有功能，通常也优于<code>BeanFactory</code>，除非一些少数的场景，例如：在受资源约束的嵌入式设备上运行一个嵌入式应用，它的内存消耗可能至关重要，并且可能会产生字节。然而，对于大多数典型的企业级应用和系统来说，<code>ApplicationContext</code>才是你想使用的。Spring大量使用了<code>BeanPostProcessor</code>扩展点（以便使用代理等）。如果你仅仅只使用简单的BeanFactory，很多的支持功能将不会有效，例如：**事务**和**AOP**，但至少不会有额外的步骤。这可能会比较迷惑，毕竟配置又没有错。

### IOC操作Bean管理(基于xml方式)

#### 什么是Bean管理

Bean管理指的是两个操作

1. Spring创建对象
2. Spring注入属性

#### Bean管理操作有两种方式

1. 基于xml配置文件方式实现
2. 基于注解方式实现

**基于xml方式创建对象**

```xml
<!-- 配置User对象创建 -->
<bean id="user" class="com.atguigu.spring5.User"></bean>
```

（1） 在spring配置文件中，使用bean标签，标签里面添加对应属性，就可以实现对象创建

（2）在bean标签里面有很多属性，介绍常用的属性

* id属性：唯一标识
* class属性：类全路径（包类路径）

（3）创建对象时，**默认的是执行无参构造方法完成对象创建**

**基于xml方式注入属性**

第一种注入方式：使用set方法进行注入

（1）创建类，定义属性和对应的set方法

```java
/** 
 * 演示使用set方法进行注入属性 
 */
 public class Book { 
       //创建属性
       private String bname;
       private String bauthor;
       //创建属性对应的set方法
       public void setBname(String bname) {
           this.bname = bname; 
       }
       public void setBauthor(String bauthor) {
           this.bauthor = bauthor; 
       } 
 }
```

（2）在spring配置文件配置对象创建，配置属性注入

```xml
<!--2 set方法注入属性-->
<bean id="book" class="com.atguigu.spring5.Book">
	<!--使用property完成属性注入
    	name：类里面属性名称
        value：向属性注入的值
    -->
    <property name="bname" value="易筋经"></property>
    <property name="bauthor" value="达摩老祖"></property>
</bean>
```

第二种注入方式：使用有参数构造进行注入

（1）创建类，定义属性，创建属性对应有参数构造方法

```java
/**
 *   使用有参数构造注入
 */
public class Orders {
    private String oname;
    private String address;

    public Orders(String oname, String address) {
        this.oname = oname;
        this.address = address;
    }
}
```

（2）在spring配置文件中进行配置

```xml
<!--3 有参数构造注入属性-->
<bean id="orders" class="com.atguigu.spring5.Orders">
	<constructor-arg name="oname" value="电脑"></constructor-arg>
    <constructor-arg name="address" value="China"></constructor-arg>
</bean>
```

（3）p名称空间注入

使用p名称空间注入，可以简化基于xml配置方式

第一步 添加p名称空间在配置文件中

![引入p名称空间](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/springp1.png)

第二步 进行属性注入，在bean标签里面进行操作

![p名称方式引入属性](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/springp2.png)

#### FactoryBean

 Spring有两种类型bean，一种<code>普通bean</code>，另外一种<code>工厂bean（FactoryBean）</code>

- 普通bean：在配置文件中定义bean类型就是返回类型
- 工厂bean：在配置文件定义bean类型可以和返回类型不一样
  	第一步 创建类，让这个类作为工厂bean，实现接口 FactoryBean
  	第二步 实现接口里面的方法，在实现的方法中定义返回的bean类型

```java
public class MyBean implements FactoryBean<Course> {
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

```xml
<bean id="myBean" class="com.atguigu.spring5.factorybean.MyBean"> </bean>
```

#### bean作用域

在Spring里面，设置创建bean实例是单实例还是多实例

在Spring里面，**默认情况下，bean是单实例对象**

**如何设置单实例还是多实例？**

（1）在spring配置文件bean标签里面有属性（<code>scope</code>）用于设置单实例还是多实例

第一个值 默认值，<code>singleton</code>，表示是单实例对象
第二个值<code>prototype</code>，表示是多实例对象

**singleton和prototype区别**

设置<code>scope</code>值是<code>singleton</code>时候，加载spring配置文件时候就会创建单实例对象
设置<code>scope</code>值是<code>prototype</code>时候，不是在加载spring配置文件时候创建对象，在调用getBean方法时候创建多实例对象

#### Bean生命周期

1、生命周期
（1）从对象创建到对象销毁的过程
2、bean生命周期

（1）通过构造器创建bean实例（无参数构造）

（2）为bean的属性设置值和对其他bean引用（调用set方法）

（3）调用bean的初始化的方法（需要进行配置初始化的方法）

（4）bean可以使用了（对象获取到了）

（5）当容器关闭时候，调用bean的销毁的方法（需要进行配置销毁的方法）
3、演示bean生命周期

```java
public class Orders {
        //无参数构造
        public Orders() {
            System.out.println("第一步 执行无参数构造创建bean实例");
        }

        private String oname;

        public void setOname(String oname) {
            this.oname = oname;
            System.out.println("第二步 调用set方法设置属性值");
        }
        //创建执行的初始化的方法
        public void initMethod() {
            System.out.println("第三步 执行初始化的方法");
        }
        //创建执行的销毁的方法
        public void destroyMethod() {
            System.out.println("第五步 执行销毁的方法");
        }
}
```

```xml
<bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod"> 
	<property name="oname" value="手机"></property>
</bean>
```

4、bean的后置处理器，bean生命周期有七步

（1）通过构造器创建bean实例（无参数构造）

（2）为bean的属性设置值和对其他bean引用（调用set方法） 

（3）把bean实例传递bean后置处理器的方法postProcessBeforeInitialization

（4）调用bean的初始化的方法（需要进行配置初始化的方法）

（5）把bean实例传递bean后置处理器的方法 postProcessAfterInitialization

（6）bean可以使用了（对象获取到了）

（7）当容器关闭时候，调用bean的销毁的方法（需要进行配置销毁的方法）

**演示添加后置处理器效果**

（1）创建类，实现接口BeanPostProcessor，创建后置处理器

```java
public class MyBeanPost implements BeanPostProcessor {
        @Override
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("在初始化之前执行的方法");
            return bean;
        }

        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("在初始化之后执行的方法");
            return bean;
        }
}
```

```xml
<!--配置后置处理器-->
<bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
```

#### xml自动装配

1、什么是自动装配
（1）根据指定装配规则（属性名称或者属性类型），Spring自动将匹配的属性值进行注入

2、演示自动装配过程

（1）根据属性名称自动注入

<!--实现自动装配 bean标签属性autowire，配置自动装配 autowire属性常用两个值： byName根据属性名称注入 ，注入值bean的id值和类属性名称一样 byType根据属性类型注入 -->

```xml
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName">
	<!--<property name="dept" ref="dept"></property>-->
</bean>
<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```

（2）根据属性类型自动注入

<!--实现自动装配 bean标签属性autowire，配置自动装配 autowire属性常用两个值： byName根据属性名称注入 ，注入值bean的id值和类属性名称一样 byType根据属性类型注入 -->

```xml
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byType">
	<!--<property name="dept" ref="dept"></property>-->
</bean>
<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```



#### 引入外部属性文件

1、直接配置数据库信息

```xml
<!--直接配置连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"></property> 
	<property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
    <property name="username" value="root"></property>
    <property name="password" value="root"></property>
</bean>
```

2、引入外部属性文件配置数据库连接池

（1）创建外部属性文件，properties格式文件，写数据库信息

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/spring5jdbc.png)

（2）把外部properties属性文件引入到spring配置文件中

引入context名称空间

```xml
xmlns:context="http://www.springframework.org/schema/context"
```

```xml
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"
```

在spring配置文件使用标签引入外部属性文件

<!--引入外部属性文件-->

```xml
 <context:property-placeholder location="classpath:jdbc.properties"/>
```

<!--配置连接池-->

```xml
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
	 <property name="driverClassName" value="${prop.driverClass}"></property>
	 <property name="url" value="${prop.url}"></property>
 	<property name="username" value="${prop.userName}"></property>
 	<property name="password" value="${prop.password}"></property>
 </bean>
```

### IOC操作Bean管理 (基于注解方式)

#### 什么是注解

注解，可以看作是对 一个 类/方法 的一个扩展的模版，每个 类/方法 按照注解类中的规则，来为 类/方法 注解不同的参数，在用到的地方可以得到不同的 类/方法 中注解的各种参数与值

注解也就是<code>Annotation</code>，相信不少人也和我之前一样以为和注释和doc一样，是一段辅助性的文字，其实注解不是这样的。

从JDK5开始，java增加了对元数据（描述数据属性的信息）的支持。其实说白就是代码里的特殊标志，这些标志可以在编译，类加载，运行时被读取，并执行相应的处理，以便于其他工具补充信息或者进行部署。

**注解的作用**

- **格式检查：**告诉编译器信息，比如被@Override标记的方法如果不是父类的某个方法，IDE会报错；
- **减少配置：**运行时动态处理，得到注解信息，实现代替配置文件的功能；
- **减少重复工作：**比如第三方框架xUtils，通过注解@ViewInject减少对findViewById的调用，类似的还有（JUnit、ActiveAndroid等）；

#### Spring针对Bean管理中创建对象提供注解

（1）@Component
（2）@Service
（3）@Controller
（4）@Repository

* 上面四个注解功能是一样的，都可以用来创建bean实例

#### 基于注解方式实现对象创建

第一步 引入依赖

第二步 开启组件扫描

<!--开启组件扫描 1 如果扫描多个包，多个包使用逗号隔开 2 扫描包上层目录 -->

`<context:component-scan base-package="com.atguigu"></context:component-scan>`

第三步 创建类，在类上面添加创建对象注解

//在注解里面value属性值可以省略不写，

 //默认值是类名称，首字母小写

 //UserService -- userService

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/component.png)

#### 基于注解方式实现属性注入

（1）<code>@Autowired</code>：根据属性类型进行自动装配
第一步 把service 和dao 对象创建，在service 和dao 类添加创建对象注解
第二步 在service 注入dao 对象，在service 类添加dao 类型属性，在属性上面使用注解
![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/service.png)

（2）<code>@Qualifier</code>：根据名称进行注入
这个<code>@Qualifier</code>注解的使用，和上面<code>@Autowired</code>一起使用

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/annomation.png)

## AOP

### 什么是AOP

面向切面编程（方面），利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。通俗描述：不通过修改源代码方式，在主干功能里面添加新功能.

#### AOP底层使用动态代理

第一种 有接口情况，使用JDK动态代理
创建接口实现类代理对象，增强类的方法

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/aop01.png)

第二种 没有接口情况，使用CGLIB动态代理
 创建子类的代理对象，增强类的方法
![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/spring5/aop02.png)

Spring框架一般都是基于AspectJ实现AOP操作，详情请自行查阅

## JdbcTemplate

### 什么是JdbcTemplate

Spring框架对JDBC进行封装，使用JdbcTemplate方便实现对数据库操作

（1）引入相关jar包

（2）在spring配置文件配置数据库连接池

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
	<property name="url" value="jdbc:mysql:///user_db" />
    <property name="username" value="root" />
    <property name="password" value="root" />
    <property name="driverClassName" value="com.mysql.jdbc.Driver" />
</bean>
```

（3）配置JdbcTemplate对象，注入DataSource

```xml
<!-- JdbcTemplate对象 --> 

<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">

​	 <!--注入dataSource--> 

​	<property name="dataSource" ref="dataSource"></property> 

</bean>
```

### JdbcTemplate操作数据库（添加） 

（1）在dao进行数据库添加操作
（2）调用JdbcTemplate对象里面update方法实现添加操作

```java
update(String sql,Object... args)
```

有两个参数
第一个参数：sql语句
第二个参数：可变参数，设置sql语句值 @Repository

```java
//注入JdbcTemplate
@Autowired private JdbcTemplate jdbcTemplate;
//添加的方法
@Override
public void add(Book book) {
    //1 创建sql语句
    String sql = "insert into t_book values(?,?,?)";
    //2 调用方法实现
    Object[] args = {book.getUserId(), book.getUsername(), book.getUstatus()};
    int update = jdbcTemplate.update(sql,args);
    System.out.println(update);
}
```

### JdbcTemplate操作数据库（查询返回对象）

```java
queryForObject(String sql, RowMapper<T> rowMapper,Object... orgs)
```

有三个参数

- 第一个参数：sql语句
- 第二个参数：RowMapper是接口，针对返回不同类型数据，使用这个接口里面实现类完成数据封装
- 第三个参数：sql语句值

```java
//查询返回对象 
@Override
public Book findBookInfo(String id) { 
    String sql = "select * from t_book where user_id=?";
    //调用方法 
    Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id); 
    return book; 
}
```

### JdbcTemplate操作数据库（查询返回集合） 

调用JdbcTemplate方法实现查询返回集合

```java
query(String sql, RowMapper<T> rowMapper, Object... orgs)
```

有三个参数

- 第一个参数：sql语句
- 第二个参数：RowMapper是接口，针对返回不同类型数据，使用这个接口里面实现类完成数据封装
- 第三个参数：sql语句值

```java
//查询返回集合
@Override public List<Book> findAllBook() {
    String sql = "select * from t_book"; 
    //调用方法
    List<Book> bookList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class)); 
    return bookList;
}
```

### JdbcTemplate操作数据库 （批量操作）

```java
batchUpdate(String sql, List<Object[]> batchArgs)
```

有两个参数

-  第一个参数：sql语句
-  第二个参数：List集合，添加多条记录数据

## 事务操作

### 什么事务？

事务是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果有一个失败所有操作都失败

### 事务四个特性（ACID）

原子性（atomicity）。一个事务是一个不可分割的工作单位，事务中包括的操作要么都做，要么都不做。

一致性（consistency）。事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。

隔离性（isolation）。一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

持久性（durability）。持久性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

### 事务操作（Spring事务管理介绍） 

1、事务添加到JavaEE三层结构里面Service层（业务逻辑层）
2、在Spring进行事务管理操作
（1）有两种方式：编程式事务管理和声明式事务管理（使用）
3、声明式事务管理
（1）基于注解方式（使用）
（2）基于xml配置文件方式
4、在Spring进行声明式事务管理，底层使用AOP原理
5、Spring事务管理API
（1）提供一个接口，代表事务管理器，这个接口针对不同的框架提供不同的实现类

### 事务操作（注解声明式事务管理）

#### 在spring配置文件配置事务管理器

```xml
<!--创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--注入数据源-->
    <property name="dataSource" ref="dataSource"></property> 
</bean>
```

#### 在spring配置文件，开启事务注解

（1）在spring配置文件引入名称空间 tx

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx" 			    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
```

（2）开启事务注解

```xml
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

3、在<code>service</code>类上面（或者<code>service</code>类里面方法上面）添加事务注解
（1）<code>@Transactional</code>，这个注解添加到类上面，也可以添加方法上面
（2）如果把这个注解添加类上面，这个类里面所有的方法都添加事务
（3）如果把这个注解添加方法上面，为这个方法添加事务

```java
@Service
@Transactional 
public class UserService {
}
```

1、在<code>service</code>类上面添加注解<code>@Transactional</code>，在这个注解里面可以配置事务相关参数

##### propagation：事务传播行为

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

##### ioslation：事务隔离级别

（1）事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题
（2）有三个读问题：脏读、不可重复读、虚（幻）读
（3）脏读：一个未提交事务读取到另一个未提交事务的数据

```
1. @Transactional(isolation = Isolation.READ_UNCOMMITTED)：读取未提交数据(会出现脏读,
 不可重复读) 基本不使用
 
2. @Transactional(isolation = Isolation.READ_COMMITTED)：读取已提交数据(会出现不可重复读和幻读)
 
3. @Transactional(isolation = Isolation.REPEATABLE_READ)：可重复读(会出现幻读)
 
4. @Transactional(isolation = Isolation.SERIALIZABLE)：串行化
```

##### timeout：超时时间

（1）事务需要在一定时间内进行提交，如果不提交进行回滚
（2）默认值是 -1 ，设置时间以秒单位进行计算

##### readOnly：是否只读

（1）读：查询操作，写：添加修改删除操作
（2）<code>readOnly</code>默认值false，表示可以查询，可以添加修改删除操作
（3）设置readOnly值是true，设置成true之后，只能查询

##### rollbackFor：回滚

（1）设置出现哪些异常进行事务回滚

##### noRollbackFor：不回滚

（1）设置出现哪些异常不进行事务回滚

### 事务操作（  XML声明式事务管理） 

1、在spring配置文件中进行配置
第一步 配置事务管理器
第二步 配置通知
第三步 配置切入点和切面

```xml
<!--1 创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--注入数据源-->
    <property name="dataSource" ref="dataSource"></property>
</bean> 

<!--2 配置通知-->
<tx:advice id="txadvice">
	<!--配置事务参数-->	
	<tx:attributes> 
        <!--指定哪种规则的方法上面添加事务-->
        <tx:method name="accountMoney" propagation="REQUIRED"/>
        <!--<tx:method name="account*"/>-->
	</tx:attributes>
</tx:advice> 

<!--3 配置切入点和切面-->
<aop:config> 
    <!--配置切入点-->
    <aop:pointcut id="pt" expression="execution(* com.atguigu.spring5.service.UserService.*(..))"/>
    <!--配置切面-->
    <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
</aop:config>
```

### 事务操作（完全注解声明式管理）

1、创建配置类，使用配置类替代xml配置文件

```java
@Configuration //配置类
@ComponentScan(basePackages = "com.atguigu") //组件扫描
@EnableTransactionManagement //开启事务
public class TxConfig {
//创建数据库连接池 
@Bean
public DruidDataSource getDruidDataSource() { 
    DruidDataSource dataSource = new DruidDataSource();
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql:///user_db");
    dataSource.setUsername("root"); 
    dataSource.setPassword("root"); 
    return dataSource;
} 

//创建JdbcTemplate对象 
@Bean
public JdbcTemplate getJdbcTemplate(DataSource dataSource) { 
    //到ioc容器中根据类型找到dataSource
    JdbcTemplate jdbcTemplate = new JdbcTemplate(); 
    //注入dataSource 
    jdbcTemplate.setDataSource(dataSource);
    return jdbcTemplate; 
} 

//创建事务管理器
@Bean
public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource) { 
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource); 
    return transactionManager; 
  }
}
```

## Spring5 框架新功能 

#### 整个Spring5框架的代码基于Java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

#### Spring 5.0框架自带了通用的日志封装

（1）Spring5已经移除Log4jConfigListener，官方建议使用Log4j2 

（2）Spring5框架整合Log4j2

#### Spring5框架核心容器支持@Nullable注解

（1）<code>@Nullable</code>注解可以使用在方法上面，属性上面，参数上面，表示方法返回可以为空，属性值可以为空，参数值可以为空
（2）注解用在方法上面，方法返回值可以为空

（3）注解使用在方法参数里面，方法参数可以为空

#### Spring5核心容器支持函数式风格GenericApplicationContext

```java
//函数式风格创建对象，交给spring进行管理 
@Test
public void testGenericApplicationContext() { 
    //1 创建GenericApplicationContext对象 
    GenericApplicationContext context = new GenericApplicationContext(); 
    //2 调用context的方法对象注册
    context.refresh(); context.registerBean("user1",User.class,() -> new User()); 
    //3 获取在spring注册的对象 
    // User user = (User)context.getBean("com.atguigu.spring5.test.User"); 
    User user = (User)context.getBean("user1"); 
    System.out.println(user);
}
```

#### Spring5支持整合JUnit5

```java
@SpringJUnitConfig(locations = "classpath:bean1.xml")
    public class JTest5 {
    @Autowired
    private UserService userService; 
    @Test
    public void test1() { 
    	userService.accountMoney(); 
    } 
}
```


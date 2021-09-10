---
title: JavaBean
date: 2021-07-12 09:24:36
tags:
- JavaBean
- Java
categories: Java
---

# JavaBean

## 基本概念

​	首先明确的是JavaBean是一种Java类，而且是一种特殊的、可重用的类。

​	JavaBean的种类按照功能可以划分为可视化和不可视化两类。可视化的JavaBean就是拥有GUI图形用户界面的，对最终用户是可见的。不可视化的JavaBean不要求继承，它更多的被使用在JSP中，通常情况下用来**封装业务逻辑**、**数据分页逻辑**、**数据库操作**和**事物逻辑**等，这样可以实现业务逻辑和前台程序的分离，提高了代码的可读性和易维护性，使系统更健壮和灵活。随着JSP的发展，JavaBean更多的应用在非可视化领域，并且在服务器端应用方面表现出了越来越强的生命力。--form 百度百科

　JavaBeans是Java中一种特殊的类，可以将多个对象封装到一个对象（bean）中。特点是可序列化，提供无参构造器，提供getter方法和setter方法访问对象的属性。名称中的“Bean”是用于Java的可重用软件组件的惯用叫法。 --from 维基百科

​	我们都知道，Java面向对象的基本特性有一个是封装，其具体作用就是当我们在设计Java类的时候，不希望外部可以直接调用类的成员变量，这样不利于程序的安全性，所以我们定义成员函数去调用这些成员变量，即get和set方法规范诞生了。
​	那么根据实体类中我们想要的这些get和set相关成员函数，就可以设计一个公共类，基于Java反射机制，来使用这些函数。
​	这就是Java bean最初的用处。Java bean也是一种类，只不过是调用其他对象内部成员函数的类。
​	但是同样需要强调一点，Java bean 在概念上并不是一种技术，而是一种规范。大量的技术人员根据这种规范，开发总结了很多技巧，便于封装使用，便于开发人员快捷开发。

## 使用规范

​	由于Java bean是Java公共的类，所以为了使编译工具或集成开发环境识别这种规范，那么我们需要在设计实体类的时候至少应该满足三个条件：

​	1、类中有一个public无参构造器，默认即可。
​	2、属性使用public 的get，set方法访问，也就是说设置成private，同时get，set方法与属性名的大小也需要对应。例如年龄属性age，get方法就要写成，public int getAge(){return this.age},其中很明显A要大写。同理，set方法就要写成public void setAge(int age){this.age = age ;}.
​	3、继承序列化接口，能够实现序列化功能。当然不一定需要直接实现序列化接口，简介继承了实现序列化接口的类也可以。这个是框架，工具跨平台反映状态必须具备的。
​	如下图设计原则所示。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/JavaBean/javabean.png)

JavaBean代码示例

```java
 1 package com.wotzc.javaBean;
 2 
 3 public class StudentsBean implements java.io.Serializable{
 4    private String firstName = null;
 5    private String lastName = null;
 6    private int age = 0;
 7 
 8    public StudentsBean() {
 9    }
10    public String getFirstName(){
11       return firstName;
12    }
13    public String getLastName(){
14       return lastName;
15    }
16    public int getAge(){
17       return age;
18    }
19 
20    public void setFirstName(String firstName){
21       this.firstName = firstName;
22    }
23    public void setLastName(String lastName){
24       this.lastName = lastName;
25    }
26    public void setAge(int age) {
27       this.age = age;
28    }
29 }
```

## 使用Java bean的优点

1、提高代码的可复用性：对于通用的事务处理逻辑，数据库操作等都可以封装在Java bean中，通过调用Java bean的属性和方法可快速进行程序设计。
2、程序易于开发维护：实现逻辑的封装，使事务处理和显示互不干扰。
3、支持分布式运用：多用Java bean，尽量减少java代码和HTML代码的混编。
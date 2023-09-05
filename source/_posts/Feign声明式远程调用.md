---
title: Feign声明式远程调用
date: 2021-08-26 13:51:55
tags:
- 远程调用
- Feign
- SpringCloud
categories: SpringCloud
---

#  简介

Feign是一个声明式的HTTP客户端，它的目的就是让远程调用更加简单。Feign提供了HTTP请求的模板，通过编写简单的接口和插入注解，就可以定义好HTTP请求的参数、格式、地址等信息。

Feign整合Ribbon（负载均衡）和Hystrix（服务熔断），可以让我们不再需要显示地使用这两个组件。

SpringCloudFeign在NetflixFeign的基础上扩展了对SpringMVC注解的支持，在其实现下，我们只需要创建一个接口并用注解的方式配置它，即可完成对服务提供方的接口绑定。简化了SpringCloudRibbon自行封装服务调用客户端的开发量。

--------

# 使用

假设会员服务想要调用优惠卷服务，想要远程调用别的服务的步骤如下：

## 引入open-feign的依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

## 编写一个接口告诉SpringCloud这个接口需要调用远程服务

### 先修改CouponController，添加以下controller方法：

```java
    @RequestMapping("/member/list")
    public R memberCoupons(){
        CouponEntity couponEntity = new CouponEntity();
        couponEntity.setCouponName("discount 20%");
        return R.ok().put("coupons",Arrays.asList(couponEntity));
    }
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/couponcontroller.png)

### 新建CouponFeignService接口

<code>@FeignClient("gulimall-coupon")</code>注解告诉SpringCloud这是一个远程客户端，要调用远程的<code>gulimall-coupon</code>服务

声明接口的每一个方法都是调用那个远程服务的那个请求

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/openfeign1.png)

上面的路径和方法是根据远程服务中的路径和方法所得

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/openfeign2.png)

## 使用@EnableFeignClients注解开启远程调用功能

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/openfeign3.png)

## 测试访问

访问<http://localhost:8000/member/member/coupons>

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/SpringCloudAlibabaNacos/feignclienttest.png)


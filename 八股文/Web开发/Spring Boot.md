# Spring Boot（脚手架）

## 概述

### 缺点

1. 配置操作不够精细
2. 依赖冗余

### 介绍一下Spring Boot

Spring Boot本身并不提供Spring的核心功能，而是作为Spring的脚手架框架，以达到快速构建项目、预置三方配置、开箱即用的目的

SpringBoot使用“习惯优于配置”的理念，通过提供众多起步依赖（Starter）降低项目依赖的复杂度。从而能够快速、容易地创建一个能独立运行、准生产级别、基于Spring框架的项目，使用Spring Boot可以不用或者只需要很少的Spring配置

### 和Spring MVC的区别



### Spring Boot的核心注解

1. **`@SpringBootApplication`**

   最核心的注解，使用在main方法所在类上，代表是一个SpringBoot的应用程序，并开启自动配置

   > 组合了：
   >
   > 1. `@SpringBootConfiguration`
   >
   >    组合了`@Configuration`，实现配置文件的功能
   >
   > 2. `@EnableAutoConfiguration`
   >
   >    开启自动配置
   >
   > 3. `@ComponentScan`
   >
   >    Spring组件扫描

2. **`EnableAutoConfiguration`**

   启动Spring应用程序上下文时进行自动配置，它会尝试猜测并配置项目可能需要的Bean

   自动配置通常是基于项目classpath中引入的类和已定义的Bean来实现的

   在此过程中，被自动配置的组件来自项目自身和项目依赖的jar包中

3. `@Import`

   `@EnableAutoConfiguration`的关键功能是通过`@Improt`注解导入的`ImportSelector`来完成。

   是`@EnableAutoConfiguration`注解的组成部分，也是自动装配功能的核心实现者

### 启动流程

1. 首先，Spring Boot项目创建完成会默认生成一个名为 *Application 的入口类，通过该类的main方法启动Spring Boot项目

   在main方法中，通过SpringApplication的静态方法——即run方法进行SpringApplication类的实例化操作

   然后再针对实例化对象调用另外一个run方法来完成整个项目的初始化和启动。

2. SpringApplication调用run方法的大致流程

    <img src="C:\Users\yaosu\AppData\Roaming\Typora\typora-user-images\image-20220611135241723.png" alt="image-20220611135241723" style="zoom:80%;" />

   重点包含以下操作：

   1. 获取监听器和参数配置
   2. 打印Banner信息
   3. 创建并初始化容器
   4. 监听器发送通知

   除上述核心操作外，run方法执行中还涉及时长统计、异常报告、启动日志、异常处理等辅助操作

## 自动装配

### 流程

使用Spring Boot时，我们只需引入对应的Starters，Spring Boot启动时便会**自动加载相关依赖，配置相应的初始化参数**，以最快捷、简单的形式对第三方软件进行集成，这便是Spring Boot的自动配置功能。

过程描述为：

1. SpringBoot通过`@EnableAutoConfiguration`注解开启自动配置功能
2. Spring Boot 在启动时会去依赖的 Starter 包中寻找 resources/META-INF/spring.factories 文件，然后根据文件中配置的 Jar 包去扫描项目所依赖的 Jar 包
3. 加载spring.factories中注册的各种AutoConfiguration类
4. 当某个AutoConfiguration类满足其`@Conditional`注解指定的生效条件时，实例化该AutoConfiguration类中定义的Bean（组件等），并注入Spring容器

### 实现

通过项目启动注解`@SpringBootApplication`中的`@EnableAutoConfiguration`注解开启自动装配

`@EnableAutoConfiguration`注解中组合的`@Import`注解又是实现自动装配的核心

> 其导入了`AutoConfigurationImportSelector`，在该类中加载 `META-INF/spring.factories` 的配置信息。然后筛选出以 `EnableAutoConfiguration` 为 key 的数据，加载到 IOC 容器中，实现自动配置功能



# Spring

## 概述

### 介绍一下Spring

### 为什么要用Spring



## 常用注解

1. **`@Conditional`注解**

   Spring4.0版本引入的新特性，可根据是否满足指定的条件来决定是否进行Bean的实例化及装配

2. **`@Conditional`衍生注解**

   * `@ConditionalOnBean`：在容器中有指定Bean的条件下

   - `@ConditionalOnClass`：在classpath类路径下有指定类的条件下。

   - `@ConditionalOnCloudPlatform`：当指定的云平台处于active状态时。

   - `@ConditionalOnExpression`：基于SpEL表达式的条件判断。

   - `@ConditionalOnJava`：基于JVM版本作为判断条件。

   - `@ConditionalOnJndi`：在JNDI存在的条件下查找指定的位置。

   - `@ConditionalOnMissingBean`：当容器里没有指定Bean的条件时。

   - `@ConditionalOnMissingClass`：当类路径下没有指定类的条件时。

   - `@ConditionalOnNotWebApplication`：在项目不是一个Web项目的条件下。

   - `@ConditionalOnProperty`：在指定的属性有指定值的条件下。

   - `@ConditionalOnResource`：类路径是否有指定的值。

   - `@ConditionalOnSingleCandidate`：当指定的Bean在容器中只有一个或者有多个但是指定了首选的Bean时。

   - `@ConditionalOnWebApplication`：在项目是一个Web项目的条件下。

## Spring的核心是什么？
Spring框架包含众多模块，其中**Core**是整个Spring框架的核心模块，其提供了**IoC容器**、**AOP功能**等一系列的基础功能

而这些功能及其他模块的功能都是建立在IoC和AOP之上的，若以说==IoC和AOP是Spring框架的核心==

### IoC

**IoC（Inversion of Control）**是**控制反转**的意思，这是一种面向对象编程的设计思想

指的是由容器创建、并维护对象与对象之间的依赖关系，降低对象之间的耦合度，使代码维护更容易

#### DI

其中，**DI（Dependency Injection）依赖注入**，它是IoC的技术实现

可以通过注解/XML配置文件的方式实现注入

##### 注解方式

1. `@AutoWired`：Spring提供，按类型

   > 默认情况下要求依赖对象必须存在，如果允许null值，可以设置它required属性为false
   >
   > 如果想使用按名称装配，可以结合@Qualifier注解一起使用

2. `@Resource`：JDK提供

   > 如果没有指定name属性，并且按照默认的名称（字段名）仍然找不到依赖对象时， @Resource注解会回退到按类型装配
   >
   > 一旦指定了name属性，就只能按名称装配

##### XML配置文件方式

1. setter设值注入
2. 构造注入
3. 接口注入：因侵入性较强不用了

IoC容器的本质就是一个工厂

### AOP

**AOP（Aspect Oriented Programing）**是**面向切面编程**思想，是对OOP的补充，可以在OOP的基础上进一步提高编程的效率。

它可以统一解决一批组件的共性需求（如权限检查、记录日志、事务管理等），实现类的增强

可以将解决共性需求的代码独立出来，然后通过配置的方式，声明这些代码在什么地方、什么时机调用。当条件满足，AOP会将该业务代码织入到我们指定的位置，从而统一解决了问题，又不需要修改这一批组件的代码

#### 要求

1. 只能对IoC容器中管理的Bean进行增强
2. CGLilb需要创建子类，所以不能对final修饰的类进行代理

#### 实现-动态代理

##### 静态代理和动态代理

* 静态代理通常只代理一个类，动态代理的是代理一个接口下的多个类
* 静态代理事先知道（编译时）代理的是什么，动态代理只有运行时才知道

##### 动态代理

###### 意义

拓展和增强

##### 实现

AOP可以有多种实现方法，Spring AOP支持如下两种：

1. JDK动态代理：默认，运行时创建接口的代理实例

2. CHLib动态代理：底层采用字节码技术，运行时创建子类代理的实例。当目标对象不存在接口时，Spring AOP采用这种方式

   > 在性能方面，CGLib创建的代理对象比JDK动态代理创建的代理对象高很多。但是，CGLib在创建代理对象时所花费的时间比JDK动态代理多很多。
   >
   > 所以，对于单例的对象因为无需频繁创建代理对象，采用CGLib动态代理比较合适。反之，对于多例的对象因为需要频繁的创建代理对象，则JDK动态代理更合适。

#### 应用场景

1. 外部可以将跨应用程序多个模块的功能抽象出来，并通过简单的AOP的使用，灵活地编织到模块中，例如：实现日志功能
2. Spring内部一些支持模块也是通过Spring AOP实现的，例如：事务管理

## Bean

### Spring如何管理Bean

管理Bean的常用注解：

1. `@ComponentScan`：用于声明扫描策略，指定容器扫描哪些报下被声明的类
2. `@Component`及其衍生的`@Repository`、`@Service`、`@Controller`：都用于声明Bean，但是语义不同
3. `@AutoWired`、`@Qualifier`：用于注入Bean
4. `@Scope`：用于声明Bean的作用域
5. `@PostConstrut`、`@PreDestory`：用于声明Bean的生命周期，调用时机不同（Bean实例化后/容器销毁前）

### Bean的作用域

默认情况下，Bean在Spring容器中是单例的，可以通过`@Scope`注解修改Bean的作用域。该注解有如下5个取值：

| 类型          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| singleton     | 在Spring容器中仅存在一个实例，即Bean以单例的形式存在。       |
| prototype     | 每次调用getBean()时，都会执行new操作，返回一个新的实例。     |
| request       | 每次HTTP请求都会创建一个新的Bean。                           |
| session       | 同一个HTTP Session共享一个Bean，不同的HTTP Session使用不同的Bean。 |
| globalSession | 同一个全局的Session共享一个Bean，一般用于Portlet环境。       |

### Bean的生命周期



## 说一说Spring容器

Spring主要提供了两种类型的容器：BeanFactory和ApplicationContext。

### BeanFactory

基础的IoC容器，类的通用工厂，默认采用延迟初始化策略——只有当客户端对象需要访问容器中的某个受管对象的时候，才对该受管对象进行初始化以及依赖注入操作。

启动较快，所需资源有限

### ApplicationContext

是在BeanFactory基础上，相对比较高级的实现，提供了额外的高级特性

默认全部初始化并绑定完成


## 用到了哪些设计模式？

### 工厂

### 单例

Spring中提供的单例不是线程安全的




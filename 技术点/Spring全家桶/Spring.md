# Spring

---

1. 方便解耦，简化开发：将所有对象的创建和依赖关系的维护交给Spring

2. 方便集成各种优秀框架

3. 降低Java EE API使用难度：对Java EE中非常难用的一些API提供了封装

4. 方便程序的测试

5. AOP编程支持：可以方便的实现对程序进行权限拦截和运行监控

6. 声明式事务的支持

   通过配置就可以完成对事务的管理

---

## IOC控制反转

---

### 依赖注入

（Denpendency Injection）是IOC控制反转的***技术实现***，底层创建对象使用的是***反射机制***

#### XML配置文件注入

##### 声明bean

告诉Spring创建某个对象

~~~ xml
<bean id="" class=""></bean>
~~~

> class是类全名

1. 设值注入：调用类的set方法

   * 简单类型的set注入（只要&必须有set方法）

     ~~~ xml
     <proprerty name="" value=""/>
     ~~~

     

   * 引用类型（可套娃）

     ~~~ xml
     <property name="" ref=""/>
     ~~~
     
     > name是属性名

2. 构造注入：调用类的有参构造方法

   * 创建对象的同时给属性赋值

     ~~~ xml
     <constructor-arg></constructor-arg>
     ~~~

#### 注解方式注入

##### 创建对象

~~~ java
@Component (value="")// 这是注解，写在class类上面
~~~

~~~ xml
<context:component-sscan base-package=""></context:component-sscan>
<!--声明组件扫描器-->
<!--指定注解在你项目中的包名，扫描其中所有的类，按照注解创建对象，或给属性赋值--> 
~~~

###### 其他注解

* `@Repository`（用在持久层上面）：放在dao的实现类上面，表示创建dao对象（访问数据库）
* `Service` （用在业务层上面）：放在service实现类上上面，创建service对象（业务处理，事务功能）
* `@Controller` （用在控制器上面），创建控制器对象（接收用户提交的参数，显示请求处理的结果）

#### 属性赋值（注解）

##### 简单类型赋值

`@Value("")`

1. 写在在属性声明上面
2. 写在在set方法上面

##### 引用类型赋值

这两个注解都写在声明的类对象上面

1. `@Autowired`

Spring提供，默认byType（自动类型），可以通过`@Qualifier(name="")`指定

2. `@Resource(name="")`

JDK提供，默认buName（指定名称）



自动类型、指定名称



## AOP面向切面编程

---

在运行时，动态地将代码切入到类的***指定方法***、***指定位置***上的编程思想

> 一个程序中跨越多个点的功能被称为**横切关注点**

==动态代理的规范化==

> 切面（Aspect）：给目标类增加的功能（一般非业务方法）

> 连接点（JoinPoint）：连接业务方法和切面的位置
>
> 切入点（Pointcut）：切入点，多个连接点方法的集合
>
> 通知（Advice）：切面执行的时间

---

### 动态代理

#### 实现方式

1. JDK动态代理：目标类必须实现接口
2. cglib动态代理：通过**继承**目标类创建子类。要求目标类不能是final的，方法也不能是final的

> Spring会自动选择，优先JDK，可以强制设置

##### AOP的技术实现框架AspectJ

1. 使用XML配置文件

2. 使用注解

切入时间

> 1. ~@Before~ 前置通知
> 2. ~@AfterReturning~ 后置通知
> 3. ~@Around~ 环绕通知（方法执行前后）
> 4. ~@AfterThrowing~ 异常通知（目标方法抛出异常后执行的通知）
> 5. ~@After~ 最终通知（总是会被执行的方法）

切入点表达式：

~~~ java
execution(访问权限 方法返回值 方法声明（参数） 异常类型)
~~~



#### 使用情景

1. 功能增强
2. 给多个类加相同功能、事务

#### 切入点表达式

~@Pointcut~

>  定义和管理切入点

当使用@Pointcut定义在一个方法的上面，此时这个**方法名**就是切入点表达式的**别名**

其他的通知中，value属性就可以直接使用别名





## 不放到Spring中的对象

1. 实体类，实体类数据来自于数据库
2. servlet、listener、filter

---

## Spring集成MyBatis



#### MyBatis使用步骤

1. 定义dao接口
2. 定义mapper文件（配置映射文件）
3. 定义主配置文件
4. 创建dao的代理对象`StudentDao dao = SqlSession.getMapper(StudentDao.Class)`

#### 整合步骤

1. 新建maven项目
2. 加入maven依赖
   1. Spring依赖
   2. MyBatis依赖
   3. MySQL驱动
   4. Spring的事务的依赖
   5. mybatis和spring集成的依赖：mybatis官方提供，用于在spring项目中创建mybatis的SqlSessionFactory、dao对象的
3. 创建实体类
4. 创建dao接口和mapper文件
5. 创建mybatis主配置文件
6. 创建Service接口和实现类，属性是dao
7. 创建Spring的配置文件：声明mybatis的对象交给spring创建
   1. 数据源DataSource（使用阿里Druid）
   2. SqlSessionFactory
   3. dao对象
   4. 声明自定义Service
8. 创建测试类



## Spring事务处理

1. 什么是事务？

   mybatis中提出了事务——一组sql语句的集合（都成功/都失败，结果一致，作为整体执行）

2. 什么时候想到使用事务？

   操作涉及到多个表/sql语句段的insert、update、delete，这些语句都成功才算完成功能（例如：转账）

   事务操作一般出现在service类业务方法上

3. 怎么使用jdbc、mybatis？

   jdbc：Connection conn; conn.commit(); conn.rollback();

   mybatis:Sqlsession.commit();SqlSession.rollback;

4. 3中处理方式有什么不足？

   1. 不同数据库访问技术，处理事务的对象、方法不同
   2. 掌握多种数据库中事务的处理逻辑：是么时候提交事务？什么时候回滚事务
   3. 处理事务的多种方法

   多种数据库访问技术，有不同的事务处理机制、对象、方法

5. 怎么解决不足

   Spring提供一种处理事务的统一模型（步骤）

6. 处理事务，需要做什么、怎么做

   声明式事务：把事务相关的资源和内容都提供给Spring，Spring就能处理事务的提交、回滚了，几乎不用代码

   **事务管理器**：一个接口和其众多实现类

   Spring把每一种数据库访问技术对应的事务处理类都创建好了

   声明对应实现类的bean

   **说明方法需要的事务**：

   1. 事务的说明级别常量（掌握）

   2. 事务的超时时间（一般不设）

   3. 事务的传播行为（7个，掌握三个）：控制业务方法是否有事务，什么事务

      REQUIRED（已经有就用，没有就新建）、REQUIRES_NEW（每次都新建）、SUPPORTS

**提交事务、回滚事务的时机**

1. 业务方法正常执行，Spring在方法执行后提交事务
2. 业务方法抛出运行时异常，Spring执行回滚，调用事务管理器的rollback（自动）
3. 抛出非运行时异常（主要是受查异常时），提交事务

> 受查异常：写代码中必须处理的异常

告诉Spring项目中的类信息、方法的名称、方法的事务传播行为



### @Transactional注解

放在public方法上面，表示当前方法具有事务

给注解属性赋值

#### 使用步骤

1. 声明事务管理器对象
2. 开启事务注解驱动

## 拦截器

## 配置类

## 注解（Annotation）

> 元注解：负责对其他注解进行说明
>
> - @Document
> - @Target
> - @Retention
> - @Inherited

## 事务管理

事务的打开、回滚和提交都是由事务管理器完成的

1. 编程式事务

   提供了Transaction Template模板

   相对麻烦，但是更灵活、控制更精确

2. 声明式事务（底层使用AOP原理）

   * 基于注解
     1. 创建事务管理器
     2. 开启事务注解
     3. Service层添加注解`@Transactional`
   * 基于XML配置文件

### 事务的传播方式

业务方法嵌套使用时，若两个方法都要保证事务，就需要Spring的事务传播机制进行控制，共7种：

| 事务传播类型                   | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED（最常见） | 如果当前没有事务，则新建一个事务；如果已存在一个事务，则加入到这个事务中。 |
| PROPAGATION_SUPPORTS           | 支持当前事务，如果当前没有事务，则以非事务方式执行。         |
| PROPAGATION_MANDATORY          | 使用当前的事务，如果当前没有事务，则抛出异常。               |
| PROPAGATION_REQUIRES_NEW       | 新建事务，如果当前存在事务，则把当前事务挂起。               |
| PROPAGATION_NOT_SUPPORTED      | 以非事务方式执行操作，如果当前存在事务，则把当前事务挂起     |
| PROPAGATION_NEVER              | 以非事务方式执行操作，如果当前存在事务，则抛出异常           |
| PROPAGATION_NESTED             | 如果当前存在事务，则在嵌套事务内执行；如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作 |

### `@Transactional`

* isolation属性：声明事务的隔离级别
* propagation属性：声明事务的传播机制

#### 类上写了注解，如何排除某个方法？


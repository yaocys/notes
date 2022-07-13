# SpringBoot

---

* 使用jar打包方式，意味着通过main提供
* SpringBoot的引导类，是项目入口，运行main方法就可以启动项目
* 使用SpringBoot和Spring构建的项目，业务代码编写方式完全一样



## 配置

### 分类：

* 两种类型的配置文件：properteies和yml/yaml
* 默认配置文件名称：application
* 同一级目录下优先级：properties>yml>yaml

### 数据格式

![image-20220411105545621](C:\Users\yaosu\AppData\Roaming\Typora\typora-user-images\image-20220411105545621.png)

参数引用：`${key}`

### 读取配置文件内容

1. 注入@Value
2. Environment
3. `@ConfigurationProperties`获取对象并注入相应属性值

### profile

动态配置切换

### 内部配置加载顺序

1. file:./config/：当前目录下的/config目录下
2. file:./：当前项目的根目录（）
3. classpath:./config/：classpath的/config目录
4. classpath:/：classpath的根目录

### 外部配置加载顺序

### 整合MyBatis

## SpringBoot高级

### 自动配置`@Condition`

> 是Spring4.0增加的条件判断功能，可以实现选择性地创建Bean操作

* 创建一个条件类实现Conditon接口，重写接口中的`matches()`方法
* 将重写的条件类作为参数传入`@Conditional`注解的参数中，`matches()`方法的布尔类型返回值决定了被注解的Bean是否会被创建

#### 动态

> 将类的判断定义为动态的，判断哪个字节码文件存在可以动态指定

`@ConditionOnXxxx`

#### 小结：

* 自定义条件：

  1. 自定义条件类：自定义类实现Condition接口，重写matches方法，在方法中进行逻辑判断，返回boolean值。

     两个参数：

     * context：上下文对象，可以获取属性值，获取类加载器，获取BeanFactory等
     * metadata：元数据对象，用于获取注解属性

  2. 判断条件：在初始化Bean时，使用`@Conditional(条件类.class)`注解

* SpringBoot提供的常用条件注解：

  1. `ConditionalOnProperty`：判断配置文件中是否有对应属性和值才初始化Bean
  2. `ConditionalOnClass`：判断环境中是否有对应字节码文件才初始化Bean
  3. `ConditionalOnMissingBean`：判断环境中没有对应Bean才初始化Bean

#### 切换内置web服务器

#### Enable注解

> Spring中提供了很多Enable开头的注解，这些注解都是用于动态启用某些功能的。而其底层原理是使用`@Import`注解导入一些配置类，实现Bean的动态加载

`@SpringBootApplication`其实是一个组合注解，包括了其它诸多的注解

其中`@EnableAutoConfiguration`是核心，它也是一个组合注解

> selectImports方法，返回值为字符串数组

其中最重要的是`Import`，来加载配置类

> * 四种用法：
>
> 1. 导入Bean
> 2. 导入配置类
> 3. 导入ImportSelector实现类，一般用于加载配置文件中的类
> 4. 导入ImportBeanDefinitionRegistrar实现类
>
> * 配置文件位置：META-INF/spring.factories，该配置中定义了大量的配置类，当SpringBoot应用启动时，会自动加载这些配置类初始化Bean
> * 并不是多有的Bean都会被初始化，在配置类中使用Condition来加载满足条件的Bean

**SPringleBoot工程不可以直接获取jar包中定义的Bean**

`@ComponentScan`扫描范围：当前引导类所在包及其子包

**解决**：

1. 使用`@ComponentScan`扫描指定包（不推荐）

2. 使用`@Import`注解，加载类，这些类都会被Spring创建并放入IOC容器

   > 参数是类

3. 对`@Import`注解进行封装->`@EnableXxx`

#### 自定义starter

### 事件监听

> SpringBoot的监听机制，其实是对Java提供的监听机制的封装
>
> Java中的事件监听机制定义了一下几个角色：
>
> 1. 事件：Event，继承java.util.EventObject类的对象
> 2. 事件源：Source，任意对象Object
> 3. 监听器：Listener，实现java.util.EventListener接口的对象

SpringBoot在项目启动时，会对几个监听器进行回调，我们可以实现这些监听器接口，在项目启动时完成一些操作












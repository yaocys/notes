# 深入理解Java虚拟机

---

JCP官方定义的Java技术体系：

* Java程序设计语言
* 各种硬件平台上的Java虚拟机实现
* Class文件格式
* Java类库API
* 来自商业机构和开源社区的第三方Java类库

![image-20220524213411050](C:\Users\yaosu\AppData\Roaming\Typora\typora-user-images\image-20220524213411050.png)

按照技术关注的重点业务：

1. Java Card：支持Java小程序运行在小内存设备上的平台
2. Java MicroE：…移动终端上的平台
3. Java StandardE：支持面向桌面级应用的Java平台
4. Java EnterpriseE：支持使用**多层架构的企业级应用**的Java平台



## Java编年史的重要节点

JDK8是2014年3月18日发布的

JDK9是2017年9月21日发布，且改成了：在每年的3月和9月各发布一个大版本

终结了每个JDK版本最少维护三年，改成每六个JDK大版本中才会被划出一个长期 支持（LTS）版，只有LTS版的 JDK能够获得为期三年的支持和更新，普通版的 JDK就只有短短六个月的生命周期。

2018年3月，Java EE称为历史名词，被送给Eclipse基金会变成Jakarta EE

2018年9月25日，JDK11发布。收费流言四起，OpenJDK和OracleJDK几乎是一样的，但前者只有半年时间的更新支持，后者商用付费，有三年更新支持。只能说迫使商业用户要么不断升级JDK版本，要么购买商业支持

2019年，JDK12发布前夕，RedHat接手了8和11的管理权力和维护职责



## Java虚拟机家族

第一框商用Java虚拟机：Sun Classic VM

1996年1月23日，JDK1.0；只提供编译，且编译与解释不能同时工作

JDK 1.2 Exact VM，有改进但是命短

JDK 1.3 HotSpot称为默认虚拟机

天下第二，BEA JRockit（专注服务器，完全即时编译，被收购，无了） & IBM J9 VM

专用虚拟机

移动KVM



## 自动内存管理

Java与C++之间有一堵由**内存动态分配**和**垃圾收集**技术所围成的高墙，墙外面的人想进去，墙里 面的人却想出来。

Java在虚拟机自动内存管理机制的帮助下，不再需要为每一个new操作去写配对 的delete/free代码，不容易出现**内存泄漏**和**内存溢出**问题；但是一旦出现问题，如果不了解虚拟机是怎样使用内存的，那排查错误、修正问题将会成为一项异常艰难的工作

### 运行时数据区域

#### 程序计数器

是一块较小的内存区域，可看作是**当前线程所执行的字节码的行号指示器**

在Java虚拟机的概念模型里，字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令

【线程私有】为了切换线程后能恢复到正确的执行位置，每个线程都需要有一个独立的程序计数器

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是本地（Native）方法，这个计数器值则应为空（Undefined）。

唯一没有OutOfMemoryError异常

#### Java虚拟机栈

线程私有，生命周期与线程相同

描述的是Java方法执行的==线程内存模型==：每一个方法被调用直至执行完毕的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程

##### 局部变量表

局部变量表存放了编译期可知的各种Java虚拟机基本数据类型、对象引用和returnAddress 类型（指向了一条字节码指令的地址）。

两类异常：如果线程请求的栈深度大于虚 拟机所允许的深度，将抛出StackOverflowError异常

#### 本地方法栈

虚拟机栈为虚拟机执行Java方法服务，本地方法栈为本地方法服务

Hot-Spot虚拟机将其合二为一

#### Java堆

虚拟机管理内存中最大的一块

线程共享

此内存区域的唯一目的就是==存放对象实例==，Java 世界里“几乎”所有的对象实例都在这里分配内存（包括：实例变量，字符串对象）。

是垃圾收集器管理的内存区域

逻辑连续，物理可以不连续。但对于大对象（例如数组），可能会要求连续

主流Java虚拟机都是按照可扩展实现

#### 方法区

线程共享的内存区域，存储已被虚拟机加载的类型信常量（如字符串直接量）、静态变量（类变量）、即时编译后的代码缓存等数据

##### 运行时常量池

方法区的一部分，类加载后存放，Class文件中常量池表（用于存放编译期生成的各种字面常量与符号引用）中的内从

#### 直接内存


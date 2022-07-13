# MyBatis框架

---

## 使用步骤

1. 定义dao接口

2. 定义mapper文件

3. 定义mybatis的主配置文件

   * `<environments>`配置数据库连接信息

   * `<mappers resource="">`配置mapper文件位置/`<package name="">`将包中所有mapper文件加载

     要求：

     1. mapper文件和dao接口在同一目录
     2. ……名称完全一样

   ```xml
       <environments default="development">
   <!--        default：默认使用哪一个环境-->
           <environment id="development">
   <!--            一个数据库信息的配置，环境-->
               <transactionManager type="JDBC"/>
   <!--            事务管理器->mybatis事务类型-->
               <dataSource type="POOLED">
   <!--                数据源->用于连接数据库——使用连接池：创建PooledDataSource类管理Connection连接对象
   UNPOOLED、JNDI-->
                   <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
   <!--                驱动类名-->
                   <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                   <property name="username" value="root"/>
                   <property name="password" value="Ue!p41SQL"/>
               </dataSource>
           </environment>
       </environments>
   ```

   

4. 创建dao的代理对象

## 深入理解参数

**简单类型**

@param注解（当多个简单类型），位置在形参前面

**对象类型**

调用get()方法

复杂方法（变量名，javaType="完整包名"，jdbcType=""）

按照位置获取#{arg0},#{arg1}

map传值

#### 占位符

常常在sql语句中作为列值使用

* #{}

  使用PrepareStatment预编译对象

  相当于预处理中的占位符?，可以防止SQL注入

* ${}

  使用Statment对象

  相当于拼接SQL串，对传入值不做任何解释的原样输出，会引起SQL注入

  > SQL注入
  >
  > 程序对用户输入数据的合法性没有判断和处理，导致攻击者可以在 Web 应用程序中事先定义好的 SQL 语句中添加额外的 SQL 语句，在管理员不知情的情况下实现非法操作，以此来实现欺骗数据库服务器执行非授权的任意查询

## 动态SQL

> SQL内容是变化的，可根据条件获取到不同的SQL语句
>
> <<通过标签实现>>

* <where>：用来包含<if>的，当多个if有一个成立，<where>就会制动增加一个where关键字，并去掉if中多余的and、or等

  条件都没有查询所有

* <foreach>：循环Java中数组、List集合，主要用在sql的 in 语句中

  collection表示接口中的方法参数类型

  item：自定义，表示数组和成员集合成员的变量

  open：循环开始时的字符

  close：结束

  separator：集合成员之间的分隔

* <sql id="">：sql代码片段，复用，配合<include refid="">



## PageHelper分页插件

1. 加入maven依赖

2. `<envrionments>`前加入`<plugins>`标签

3. PageHelper对象

   查询语句之前调用startPage()方法



## ResultMap

如果数据库中表的字段与`User`类的属性名称一致，我们就可以使用`resultType`来返回

但当它们不一致时，就要使用resultMap



## @Param注解

最基础的用法是：用在mapper层，dao层数据库方法有多个参数时使用

其他用法：

* 为参数方法取别名
* XML中的SQL使用了$
* 动态SQL用到了参数作为判断条件

### insert标签的keyProperty属性

新增后返回新增行的主键ID（属性）

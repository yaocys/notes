# SSM框架整合

---

## 步骤

### 1. 调整项目结构

### 2. 导入maven依赖

### 3. 修改web.xml文件

### 4. 添加框架配置文件

#### 1. spring_mvc.xml

* 这里scan扫描==只扫描mvc的包==

  web服务器启动的时候，spring-mvc.xml会优先于spring.xml启动，如果工程整包被扫描，那么service会优先于事务先启动，这样就导致事务无法注入管理，所以这里只能扫描mvc包，然后在spring.xml文件中扫描service包，这样就是先启动spring的事务管理注入，再注入service，最后注入controller，这个时候，事务才进入了spring 的IOC管理。**结论就是：为了避免这种问题出现，需要在spring_mvc.xml中只扫描controller的包，而在spring.xml的配置中只扫描service的包**

  > 是否被spring的IOC管理，可以在调用批量增、删、改的时候，通过后台打印查看，没有管理的提示是：
  >
  > `JDBC Connection [com.mysql.jdbc.JDBC4Connection@7103e78b] will not be managed by Spring`
  >
  > 并且每操作一次数据库，都会创建一个新的session，所以当某一条数据出错时，不会回滚。
  >
  >  
  >
  > 如果纳入管理的提示是：
  >
  > `JDBC Connection [com.mysql.jdbc.JDBC4Connection@29fd1532] will be managed by Spring`
  >
  > 并且每次操作不会创建新的session，所以当某一条数据出错时，会回滚所有提交内容。

  **PS**：**事务需要在spring.xml文件进行配置，后面的spring.xml文件中会注释说明**

* 添加json返回编码UTF-8



#### 2. spring_config.xml

#### 3. mybatis.xml

**引入mybatis.xml只是为了添加辅助参数配置项，比如显示sql或者控制返回空值等，如果项目中不需要这些配置，直接可以删除掉此文件**

### 5. 创建实体类

### 6. 创建dao接口

### 7. 创建mapper映射文件

### 8. 创建service

### 9. 创建controller


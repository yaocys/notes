# Spring MVC

---

> Spring专门做Web开发得一个模块（理解为一个高级版的Servlet），也是个容器，放控制器对象（接收请求，处理结果）
>
> **基于原生Servlet**

### Servlet

Java Web三大组件：Servlet、Filter、Listener

功能：接收客户端发送来的请求，并相应数据给客户端

如何做？

1. 实现servlet接口
2. 重写service方法
3. 注册servlet并配置访问路径

Servlet生命周期

1. 执行Servlet构造器方法
2. 执行init初始化方法
3. 执行service方法
4. 执行destroy销毁方法

---

用@Controller注解创建的是一个普通的类对象，但是Spring MVC能够赋予控制器对象一些额外的功能



Web开发底层是servlet，SpringMVC种有一个对象是Servlet：DispatherServlet（中央调度器）

> 负责接收用户的所有请求，并转发给Controller对象（处理请求）



实例1：

用户在页面发一个请求交给spring mvc控制器对象，并显示请求处理结果

1. 新建web maven工程
2. 加入依赖
   1. spring web mvc（间接加入spring）
   2. jsp、servlet依赖
3. 重点：在web.xml种注册spring mvc框架的核心对象DispatcherServlet（继承自HttpServlet），也叫**前端控制器**
4. 创建一个发起请求的页面
5. 创建控制器类（@Controller注解创建对象，并放入spring mvc容器），在类中的方法上面加入@RequestMapping注解
6. 创建一个用于显示结果的jsp页面
7. 创建spring mvc的配置文件
   1. 声明组件扫描器
   2. 声明视图解析器

### 转发和重定向区别

**转发（Redirect）是服务器行为，重定向（Forward）是客户端行为**

* forward转发

  只能在本站点内

* redirect重定向

  是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址.所以地址栏显示的是新的URL，浏览器至少做了两次访问请求

有==数据传递==就用转发，没有就用重定向

### Java Web四大作用域

1. PageContext

   作用范围是整个JSP页面，是四大作用域中最小的一个；生命周期是当对JSP的请求时开始，当响应结束时销毁。

2. ServletRequest

   作用范围是整个请求链（请求转发也存在）；生命周期是在service方法调用前由 服务器  创建，传入service方法。整个请求结束，request生命结束。

3. HttpSession

   作用范围是一次会话。生命周期是在第一次调用request.getSession()方法时，服务器会检查是否已经有对应的session,如果没有就在内存中创建一个session并返回。当一段时间内session没有被使用（默认为30分钟），则服务器会销毁该session。如果服务器非正常关闭（强行关闭），没有到期的session也会跟着销毁。如果调用session提供的invalidate（） ，可以立即销毁session。

   > 服务器正常关闭，再启动，Session对象会进行钝化和活化操作。同时如果服务器钝化的时间在session 默认销毁时间之内，则活化后session还是存在的。否则Session不存在。  如果JavaBean 数据在session钝化时，没有实现Serializable 则当Session活化时，会消失。

4. ServletContext

   作用范围是整个Web应用。当Web应用被加载进容器时创建代表整个web应用的ServletContext对象，当服务器关闭或Web应用被移除时，ServletContext对象跟着销毁。 (  this.getServletContext().getAttribute("***");  )

### request和response对象

request对象是服务器对浏览器请求的封装，而response是服务器对服务器响应的封装。 



## 拦截器

> 是用来拦截控制器方法的，执行位置分别是（分别对应三个方法）：
>
> 1. preHandle：控制器方法执行前，boolean类型的返回值表示是否拦截或放行，true为放行，调用控制器方法；false拦截
> 2. postHandle：执行后
> 3. afterComplation：渲染视图后

* 写拦截器类实现接口`HandlerInterceptor`

* 配置拦截器

  ```xml
  <mvc:interceptors>
      <mvc:interceptor>
          <mvc:mapping path=""/>
          <!--拦截路径-->
          <!--“/**”表示多重目录（所有请求）-->
          <mvc:exclude-mapping path=""/>
          <!--排除路径-->
          <bean class=""/>
          <ref bean=""/>
          <!--（只写）两种都默认拦截所有请求-->
      </mvc:interceptor>
  </mvc:interceptors>
  ```

### 多个拦截器的执行顺序

按配置顺序压栈执行

### 与过滤器的区别

1. 拦截器是基于Java**反射机制**，而过滤器基于**函数回调**
2. 拦截器不依赖于Servle容器，而过滤器依赖
3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用
4. 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问
5. 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次
6. 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑

### 触发时机

<img src="https://img-blog.csdnimg.cn/20200321183038496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dsYW1vdXIyMDE1,size_16,color_FFFFFF,t_70" alt="触发时机" style="zoom:50%;" />

## 使用实例

1. 引入maven依赖（Spring-web-mvc、Thymeleaf-Spring5）
2. 配置web.xml（配置前端控制器，spring mvc配置文件位置（初始化配置参数））->SpringMVC.xml（配置视图解析器）
3. 编写controller

### @RequestMapping注解

请求映射

* 标识在类上

  设置映射请求的请求路径的初始信息

* 标识在方法上

  设置映射请求请求路径的具体信息

支持路径中的占位符`{}`，配合`@PathVariable("")`传参

但是这样不能够一次传多个参数

## 获取请求参数

* ServletAPI获取字符串形式的数组

* 通过控制器方法的形参

  保持请求方法形参名和传递参数名（name）一致

  不一致则使用`@RequestParam`注解

  > value=name、required、defaultValue属性
  >
  > * `@RequestHeader`获取请求头
  > * `@CookieValue`获取cookie

* 实体类作为参数获取：请求参数名与实体类属性名一致

  > get请求乱码由tomcat配置解决
  >
  > ```xml
  >   <filter>
  >     <filter-name>CharacterEncodingFilter</filter-name>
  >     <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  >     <init-param>
  >       <param-name>encoding</param-name>
  >       <param-value>UTF-8</param-value>
  >     </init-param>
  >     <init-param>
  >       <param-name>forceResponseEncoding</param-name>
  >       <param-value>true</param-value>
  >     </init-param>
  >   </filter>
  >   <filter-mapping>
  >     <filter-name>CharacterEncodingFilter</filter-name>
  >     <url-pattern>/*</url-pattern>
  >   </filter-mapping>
  > ```
  >

#### 通过Json获取请求参数

请求设置请求头，在请求体里放json数据

* json数组，且数据不多时（`[id:id]`），可以直接用`@RequestParam`
* json数组，且数量多，可以用List接收
* json对象，且与实体类属性对应，可用`@RequestBody`自动绑定
* json对象，与实体类属性不对应，可用Map接收

## 域对象共享数据

> 原则是选择能实现功能的范围最小的域对象
>
> 1. request一次请求
>
> 2. session一次会话，**浏览器**开启到关闭
>
>    > 钝化：浏览器未关闭，服务器关闭，session中数据序列化到磁盘上
>    >
>    > 活化：浏览器未关闭，服务器关闭又开启了，读取数据到session
>    >
>    > 原理：只要当前jsessionid的cookie存在，当前会话中session创建的永远是同一个
>
> 3. servletContext(application)：整个应用的范围，**服务器**开启到服务器关闭
>
>    > 从头到尾只创建一次，都是同一个对象才能共享数据
>    >
>    > 转发属于一次请求，可以获取请求域中的数据
>    >
>    > 频繁使用的数据放这里面，免得反复去查

### Request域

* 使用servletAPI

  ```java
      @RequestMapping("/RequestAPI")
      public String testRequestByServletAPI(HttpServletRequest request){
          request.setAttribute("testParam","值是一个对象");
          // 参数：String，Object
          return "success";
          // 这是转发
      }
  ```


* 使用ModelAndView

  前六个不管用什么方式最终都要封装到ModlAndView中

  控制器都会返回ModelAndView对象

  被建议使用

  ```java
      @RequestMapping("/testModelAndView")
      public ModelAndView testModelAndView(){
          ModelAndView modelAndView = new ModelAndView();
          // 处理模型数据，即向请求域request共享数据
          modelAndView.addObject("testParam","ModelAndView共享数据");
          // 设置视图名称，这还不能乱写，其实就是上面转发的视图名
          modelAndView.setViewName("success");
          return modelAndView;
      }
  ```

  

* 使用Model

  model：其实是一个ModelMap类型——继承自LinkedHashMap

  ```java
      @RequestMapping("/testModel")
      public String testModel(Model model){
          // 这里是作为形参
          model.addAttribute("testParam","Model共享数据");
          return "success";
      }
  ```

* 使用map

  ```java
      @RequestMapping("/testMap")
      public String testMap(Map<String,Object> map){
          map.put("testParam","Map共享数据");
          return "success";
      }
  ```

* 使用ModelMap

  ```java
      @RequestMapping("/testModelMap")
      public String testModelMap(ModelMap modelMap){
          modelMap.addAttribute("testParam","ModelMap共享数据");
          return "success";
      }
  ```

  

**Model、ModelMap和Map的关系**

输出他们三个`getClass().getName()`

底层实现都是`BindingAwareModelMap`继承自`ExtendedModelMap`

`ExtendedModelMap`继承自`ModelMap`实现了`Model`接口

`ModelMap`继承自`LinkedHashMap`

### Session域

session一般用来保存用户的登陆状态

默认失效时间是不操作30分钟

### ServletContext域

## 视图

SpringMVC中的默认视图是View视图——渲染数据，将模型Model中的数据展示给用户

* ThymeleafView

  没有任何前缀才会被Thymeleaf解析

  当不符合当前的前缀/后缀时用下面两种

* 转发视图InternalRescourceView

  如果引入了JSTL依赖，自动转换为jstlView

* 重定向视图RedirectView

* **视图控制器**

  在SpringMVC配置文件中配置

  ```xml
  <mvc:view-controller path="/" view-name="index"/>
  <!--这么用的前提是控制器方法中直接反回视图而没有其他额外操作
  但是这样控制器中的请求映射方法都会失效-->
  <mvc:annotation-driven/>
  <!--需要开启MVC的注解驱动-->
  ```

## RESTFul

## @ResponseBody注解

步骤：

1. **加入jackson的依赖**（响应Java对象）
2. 开启注解驱动
3. 控制层-处理器方法上加上注解
4. Java对象直接作为控制器方法值返回，就会被自动转换为json格式的字符串

* 将方法的返回值以特定格式写入到response的body区域，进而将数据返回给客户端

  > `{json对象}`/`[json数组]`
  >
  > 实体类、map->对象
  >
  > list->数组

### SpringMVC 处理ajax

用形参接收即可

### 复合注解@RestController

相当于为类加了@Contriller注解，并且为其中的每个方法添加了@ResponseBody注解

ResponseEntity实现文件下载

## 异常处理器

HandlerExceptionResolver

* 基于配置

  配置异常处理

  ```xml
  <!--    配置异常处理类-->
      <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
          <property name="exceptionMappings">
              <props>
                  <!--键表示处理器方法执行过程中出现的异常
                  值表示返回的视图，跳转到指定页面-->
                  <prop key="java.lang.ArithmeticException">error</prop>
              </props>
          </property>
  <!--        将异常信息共享在请求域中-->
          <property name="exceptionAttribute" value="ex"/>
      </bean>
  </beans>
  ```

  

  * 基于注解

    ```java
    @ControllerAdvice
    public class ExceptionController{
        @ExceptionHandler(value={NullPointerException.class})
        public String testException(Exception e,Model model){
            model.addAttribute("e",e);
            return "error";
        }
    }
    ```






@RequestBody + map

```html
{
capacity: 48
courseId: "1001"
courseName: "全民游泳入门班"
description: "每周六上课"
frequency: 5
price: 299
startTime: 1653611273000
    {
        course{

        }
        coach{
            id:
            name:
        }
    }

}
```


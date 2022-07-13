# Spring MVC

## 常用注解

主要有两类

1. 处理请求地址映射的注解

   `@RequestMapping`及其衍生出的一系列注解，如：

   `@GetMapping`：get方式请求的url映射

   `@PostMapping`

   `@DeleteMapping`

2. 获取请求参数的注解

   `@RequestParam`：接收普通参数，可以当请求方法形参与传递参数名不一致时使用，也可以设置是否强制要求

   `@PathVariable`：绑定url中的占位符，获取路径参数

   `@RequestBody`：获取请求体中的参数，例如json对象（需要设置请求Content-Type）

   `@RequestHeader`：获取请求头中的参数

另外还有`@ResponseBody`注解，可以将返回值直接写入response对象的body区域中，如果返回Java对象则会被自动转换为json字符串

## 执行流程

1. 整个过程开始于客户端发出的一个HTTP请求，如果**匹配到DispatcherServlet的请求映射路径**，则web容器将该请求转交给DispacherServlet处理

2. DispatcherServlet接收到这个请求后，将根据请求的信息以及HandlerMapping的配置找到处理请求的处理器（Handler）

   可将HandlerMapping看作路由控制器，将Handler看作目标主机。

   >  SpringMVC并没有定义一个Handler接口，实际上任意一个Object都可以成为请求处理器

3. 当DispatcherServlet通过HandlerMapping得到对应当前请求的Handler后，通过HandlerAdapter对Handler进行封装，再以统一的适配器接口调用Handler。

   > HandlerAdapter是Spring MVC框架级接口，顾名思义，HandlerAdapter是一个适配器，它用统一的接口对各种Handler方法进行调用。

4. 处理器完成业务逻辑的处理之后，将返回一个ModelAndView给DispatcherServlet，ModelAndView包含了视图逻辑名和模型数据信息。

5. ModelAndView中包含的是“逻辑视图名”而非真正的视图对象，DispatcherServlet借由ViewResolver完成逻辑视图名到真实视图对象的解析工作。

6. 当得到真实的视图对象View后，DispatcherServlet就使用这个View对象对ModelAndView中的模型数据进行视图渲染。

7. 最终客户端得到的响应消息可能是一个普通的HTML页面，也可能是一个XML或JSON串，甚至是一张图片或一个PDF文档等不同的媒体形式。

1. ***什么是MVC？***

   MVC是一种设计模式，在这种模式下软件被分为3层：

   * Model（模型）：代表数据，指工程中的JavaBean，作用是处理数据，分两类：
     1. 实体类Bean：专门存储业务数据的，如：Student、User等
     2. 业务处理Bean：指Servlet或Dao对象，专门用于处理业务逻辑和数据访问
   * View（视图）：代表用户界面，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据
   * Controller（控制器）：代表数据地处理逻辑，是Model和View之间地桥梁，指工程中的Servlet，作用是接受请求和响应浏览器

   工作流程：用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕后将结果返回到Controller，Controller再根据请求处理的结果找到相对应的View视图，渲染数据后最终响应给浏览器

   软件分层地好处是：可以降低对象之间地耦合度，便于代码的维护

2. ***DAO层是做什么的？***

   DAO是Data Access Object的缩写，即数据访问对象，在项目中它通常作为独立的一层，专门用于访问数据库。

3. ***Spring MVC的各个组件***

   * 前端控制器（DispatcherServlet）：主要负责捕获来自客户端的请求和调度各个组件

   * 处理器映射器（HandlerMapping）：根据url查找后端控制器Handler

   * 处理器适配器（HandlerAdapter）：执行后端控制器（Handler），拿到后端控制器返回的结果MoldelAndView后将结果返回给前端控制器

   * 后端控制器/处理器（Handler）：主要负责处理前端请求，完成业务逻辑，生成ModwlAndView对象返回给HandlerAdapter

   * 视图解析器（ViewResolver）：主要负责将从DispatcherServlet中拿到的ModelAndView对象进行解析，生成View对象返回给DispatcherServlet

## 拦截器

### 执行流程

### 实现步骤


# BIO、NIO、AIO

## IO模型分类

* 同步（发送请求后等待返回）和异步（发送后不等待）
* 阻塞（线程调用read/write方法后，必须等待IO完成）和非阻塞



* 同步阻塞BIO
* 同步非阻塞NIO
* 异步非阻塞AIO

## BIO

> 数据读取必须阻塞在一个线程内等待其完成
>
> 适用 连接数少且固定

一客户一线程，接收客户端连接，并为每一个客户创建一个线程

但若用户数过于庞大，服务器资源会出现不够分配的情况

在处理客户端请求时，read方法会阻塞（未发请求等待）

accept无客户端连接阻塞

多线程在一定程度上解决了阻塞问题

### NIO和BIO区别

1. BIO是阻塞的，NIO是非阻塞的
2. BIO是面向流的，只能单向读写；NIO是面向缓冲的，可以双向读写
3. BIO为每一个连接新建线程；NIO多路复用，使用一个线程来监听所有的Socket连接

## NIO

> 适用 连接数多且短

### 阻塞模式

### 非阻塞模式

通道Chanel（全双工）、缓冲区Buffer、选择器Selector

选择器监听多个通道

<img src="https://segmentfault.com/img/remote/1460000037714808/view" alt="preview" style="zoom: 33%;" />

* 简单的（自己轮询）
* 多路复用Reactor



## AIO

> 适用 连接数多且长

与NIO的区别是，不再需要一个线程去轮询说有的IO操作状态，而是IO状态改变后，系统会通知响应线程来处理

多路复用Proactor



## 参考

[BIO、NIO、AIO面试题](https://blog.csdn.net/weixin_43122090/article/details/105462088)
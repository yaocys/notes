# Nginx

## 概述

### 发行版本

* Nginx开源版本

  基础功能，二次开发难度大

* Nginx plus商业版

* Openresty

  基于Nginx和Lua脚本

* Tengine

  淘宝开源，C语言模块化开发

### 运行流程

多进程，包含一个master主进程

## 配置文件

* worke_process：工作进程数

* worker_connections：每个进程的连接数

* include：额外的配置文件

  mime_types：对指定文件类型的操作
  
* sendfile：数据0拷贝，少一次读取到内存

* keepalive_timeout：保持链接超时时间

* server：虚拟主机，可以运行多个

  * listen：监听端口号
  * server_name：域名、主机名
  * location：匹配路径
  * error_page：错误信息页

### 虚拟主机与域名解析

虚拟主机：同一台机器上部署多个应用，同一个IP地址

## 反向代理


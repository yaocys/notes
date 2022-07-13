# Redis

选择数据库（共16个）

````sql
SELECT 1
````

`flushdb`：清空当前数据库

`flushall`：清空所有数据库

## 基本命令

`keys partten`：正则匹配，查看所有匹配的key

`exists <key>`：键是否存在，1存在，0不存在

`DEL <key>`：删除键，返回值是删除键的个数

`type <key>`：获得键值的数据类型

## 基本数据类型

### String

是二进制安全的

#### 命令

`set <key> <value>`

`get <key>`

`append <key> <value>`

`strlen <key>`

`setnx <key><value>` key不存在，才能设置（不能覆盖）



`incr <key>` key中存储数字增1（**原子操作**，而Java中的++不是）

`decr <key>` key中存储数字减1

`incrby/decrby <key><step>` 自定义步长



`mset <key><value><key><value>…`原子性，一个失败都失败

`mget <key><key>…`

`msetnx <key><value><key><value>…`



`getrange <key><begin><end>`

`setrange <key><start><value>`



`setex <key><过期时间><value>` 设置键值的同时，设置过期时间

`getset <key><value>`获取旧值同时设置新值

#### 数据结构

String的数据结构为简单动态字符串，内部结构类似于ArrayList，采用预分配冗余空间的方式减少内存的频繁分配

![image-20220604233018657](C:\Users\yaosu\AppData\Roaming\Typora\typora-user-images\image-20220604233018657.png)

### List

单键多值

简单的字符串列表，按插入顺序排序

底层是双向链表

#### 命令

`lpush/rpush <key><value1><value2>…`从左/右插入值

`lpop/rpop <key>` 值亡键亡

`rpoplpush <key1><key2>` 1列表右边吐出一个值，插到2列表左边

`lrange <key><start><end>` 按照索引下标获得元素

`lrange <key> 0-1` 0左边第一个，-1右边第一个（0-1表示所有）

`lindex <key><index>` 按照索引下标获取元素

`llen <key>`



`linsert <key> before <value><new value>` 在后面插入值

`lrem <key><n><value>` 从左边删除n个value

`lset <key><index><value>` 更改指定下标的值

#### 数据结构

数量较少，连续内存ziplist；数量多，多个ziplist链接成快速链表

### Set

### Hash

#### 命令

`hset <key><field><value>`：给集合中的field赋值

`hget <key><field>`：取出value

`hmset <key><field1><value1><field2><value2>`

`hexists<key><field>`

`hkeys <key>`：查看集合所有field

`hvals <key>`：列出集合所有value

`hincrby <key><field><increment>`

`hsetnx <key><field><value>`：将域值设为value，当且仅当域不存在

### ZSet

### Bitmaps

可以进行位操作的字符串

## 配置文件

## 订阅和发布

## 持久化

### RDB

在指定的**时间间隔**内将数据集**快照**写入磁盘

#### 实现

创建子进程，将数据同步到临时区域，再替换rdb文件

写时复制技术

### AOF

以日志形式记录每个写操作，只允许追加不允许改写文件

##  集群

1. 结构上，单个Redis服务器会发生单点故障

   将数据库赋值多个副本并部署在不同的服务器上

2. 容量上，单个Redis服务器的内存非常容易形成存储瓶颈

### 复制

当一台数据库中的数据更新后，自动将新的数据同步到其他数据库上

```sql
redis-server
# 主数据库
redis-server --p 6380 --slaveof 127.0.0.1 6379
# 从数据库
info replication
# 获取相关信息
```

从数据库是只读的

> 就算改可写也不会同步给主数据库，且会被主数据库覆盖

### 哨兵

解决主数据库挂了需要手动升格处理的问题

监控Redis系统的运行状态

功能包括：

1. 监控主从数据库是否正常运行
2. 主数据库出现故障自动将从数据库转换为主数据库

### 集群

即使使用哨兵，集群总数居存储量受限于可用存储内存最小的数据库节点，形成木桶效应

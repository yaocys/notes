# MySQL

### 导入数据

```sql
source
```

## 用户操作

### 新建远程连接用户

```sql
# 创建一个用户
# %代表所有IP可访问
create user 'remote'@'%' identified by 'Remote123';
# 为用户授权
grant all privileges on *.* to 'remote'@'%' with grant option;
```

## 存储引擎

```sql
-- 查看支持的存储引擎
show engines
```




---
layout: post   	
catalog: true
tags:
    - Postgresql
---



# 1. 压缩包版安装

## 1.1. 版本差异

15版本以上，需要Navicat 15.0.29或16.1

否则会报

```
连接PGSQL报错column “datlastsysoid“ does not exist Line1:SELECT DISTINCT datalastsysoid FROM pg_database
```

## 1.2. 简化压缩包

下载压缩包版本 [下载地址](https://www.postgresql.org/download/windows/)

**doc**， **pgAdmin 4** ， **StackBuilder** ，**symbols** 四个文件夹可以直接删除

## 1.3. 初始化

```
initdb -D "E:\pgsql\data" -E UTF8 -U postgres --locale="Chinese (Simplified)_China.936" --lc-messages="Chinese_China.936" -A scram-sha-256 -W
```

## 1.4. 修改配置文件

`postgresql.conf `

```
#listen_addresses = 'localhost' 改为 listen_addresses = '*'
```

`pg_hba.conf`，清空，并用如下内容进行替换

```
host all all 0.0.0.0/0 scram-sha-256
host all all ::/0 scram-sha-256
```

## 1.5. 注册为服务

```
pg_ctl.exe register -D "E:\pgsql\data" -PostgreSQL
```

## 1.6. 卸载

删除服务

```
pg_ctl.exe unregister -PostgreSQL
```

删除文件

# 2. 运行方式

在Windows环境下配置 PostgreSQL 的最大连接数时，如果采用 Windows 服务模式运行则配置最大连接数为200比较好，如果需要200个以上的并发连接，则最好采用控制台形式启动运行PostgreSQL

PostgreSQL控制台运行，启动和停止命令如下：

启动
**pg_ctl.exe -D "D:\Software\PostgreSQL\data" start**
停止
**pg_ctl.exe -D "D:\Software\PostgreSQL\data" stop**

-D 的参数 "D:\Software\PostgreSQL\data" 是 PostgreSQL 的数据实例位置
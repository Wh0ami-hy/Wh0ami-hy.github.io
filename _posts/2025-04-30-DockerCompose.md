---
layout: post   	
catalog: true 	
tags:
    - Docker
---

# 1. Docker Compose

## 1.1. 简介

Docker Compose 负责实现对 Docker 容器集群的快速编排，可以把项目的多个服务集合到一起，一键运行。

如：要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

Docker Compose 恰好满足了这样的需求。它允许用户通过一个单独的 `docker-compose.yml` 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）

Docker Compose 中有两个重要的概念：

-   服务 (`service`)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
-   项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。

Docker Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理

## 1.2. 安装

**docker-compose和docker compose的区别**

Compose分为V1和V2版本，安装方式分为两种，一种是独立安装（standalone），一种是插件安装（plugin），所以有四种组合方式：

|                   | V1             | V2             |
| ----------------- | -------------- | -------------- |
| standalone（独立式安装） | docker-compose | docker-compose |
| plugin（插件式安装）     | 不支持插件式安装       | docker compose |

插件安装的Compose，在V2版本，指令是docker compose（中间是空格），**最新版的docker安装时会自动以插件的形式安装docker compose**

支持同时采用两种方式安装Compose，安装后可以同时使用docker-compose和docker compose

**独立式安装**

在 Linux 64 位系统上直接下载对应的二进制包

```
$ sudo curl -L https://github.com/docker/compose/releases/download/2.17.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 国内用户可以使用以下方式加快下载
$ sudo curl -L https://get.daocloud.io/docker/compose/releases/download/2.17.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
# 给脚本执行权限
$ sudo chmod +x /usr/local/bin/docker-compose
```

运行`docker-compose --version`检查是否安装成功

卸载：如果是二进制包方式安装的，删除二进制文件即可。

```
$ sudo rm /usr/local/bin/docker-compose
```

## 1.3. Compose 模板文件

默认的模板文件名称为 `docker-compose.yml`

参考文档：https://docs.docker.com/compose/

```
version: "3.7"

services:
  app:
    build: ./
    ports:
      - 80:8080
    volumes:
      - ./:/app
    environment:
      - TZ=Asia/Shanghai
  redis:
    image: redis:5.0.13
    volumes:
      - redis:/data
    environment:
      - TZ=Asia/Shanghai

volumes:
  redis:
```

> 容器默认时间不是北京时间，增加 TZ=Asia/Shanghai 可以改为北京时间

## 1.4. 常用命令

docker compose命令的运行需要docker-compose.yml文件的支持，可以指定文件位置，也可以在文件同目录运行命令

```
## 构建镜像并启动文件内配置的所有容器
docker compose up

## 构建镜像并启动所有容器，后台运行
docker compose up -d

## 停止并删除所有容器
docker compose down

## 重启单个服务
docker-compose restart service-name

## 重启所有服务
docker compose restart

## 启动服务
docker compose start

## 停止服务
docker compose stop

## 展示当前docker compose编排过并运行的所有容器（在docker-compose.yml文件同目录下运行）
docker compose ps

## 检查docker-compose.yml文件
docker compose config

## 检查docker-compose.yml文件，有问题就输出
docker compose config -q

## 进入容器命令行
docker-compose exec service-name sh

## 查看容器运行log
docker-compose logs [service-name]
```

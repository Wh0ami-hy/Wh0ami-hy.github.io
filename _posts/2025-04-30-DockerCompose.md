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

**Docker Compose** 是一个用于定义和运行 **多容器 Docker 应用程序** 的工具。它允许你通过一个 YAML 文件（通常是 `docker-compose.yml`）来配置应用程序所需的所有服务、网络、卷、环境变量等，然后使用一条命令就可以启动整个应用环境。

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

## 1.3. Compose file 顶层字段

**version**

用于指定 Docker Compose 文件的格式版本。从 **Docker Compose v2+** 开始，官方建议不再强制使用 `version` 字段

| Version 字段  | 支持的 Compose 功能版本                      | 状态           |
| ------------- | -------------------------------------------- | -------------- |
| &#39;1&#39;   | 早期版本（无 services、networks 等顶级字段） | 已弃用         |
| &#39;2.x&#39; | 更完善的多服务支持，卷、网络等高级功能       | 已弃用         |
| &#39;3.x&#39; | 主要用于 Swarm 模式部署微服务架构            | 已弃用         |
| &#39;3.8&#39; | 最后一个较稳定的版本之一                     | 推荐用于旧项目 |

**name**

为项目指定一个名称（可选，否则默认使用目录名），name值会作为`${COMPOSE_PROJECT_NAME}`公共变量的值

- 必须以小写字母或数字开头
- 后续可以是小写字母、数字、下划线 `_`或短横线 `-`
- 不能包含大写字母、空格、特殊字符（如 `/, .` 等）

**services（必须字段）**

定义组成应用的所有服务（容器）。每个服务对应一个容器

```yml
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

每个服务可以包含以下常用子字段：

- `image`: 使用的镜像名
- `container_name`：创建的容器名称
- `build`: 构建镜像的方式（可以是一个路径或详细构建选项）
	- context：指定 Docker 构建镜像时使用的上下文路径（也就是构建镜像所需的文件所在的目录）
	- dockerfile：指定用于构建镜像的具体 Dockerfile 文件名（如果不在默认位置或不是默认名称）
- `ports`: 映射端口
- `environment`: 设置环境变量
- `depends_on`: 定义启动顺序依赖
- `volumes`: 挂载卷
- `networks`: 加入哪些网络
- `restart`: 容器重启策略

**networks**

定义自定义网络，服务可以通过这些网络进行通信。

```yml
networks:
  app-network:
    driver: bridge
```

然后在服务中引用：

```yml
services:
  web:
    networks:
      - app-network
  db:
    networks:
      - app-network
```
**volumes**

定义命名卷，用于持久化数据或共享文件。

```yml
volumes:
  mysql_data:
  redis_data:
```

然后在服务中挂载：

```yml
services:
  mysql:
    volumes:
      - mysql_data:/var/lib/mysql
```

**configs**

为 Swarm 模式提供配置文件支持（如配置文件内容注入容器）。

**secrets**

为 Swarm 模式提供敏感信息管理（如密码、API Key 等）。

**build**

定义构建镜像的参数（通常在`services`内部使用，但也可以作为顶层字段用于多阶段构建）

```yml
build:
  base-image:
    context: .
    dockerfile: base.Dockerfile
```

然后在服务中引用：

```yml
services:
  web:
    build:
      target: base-image
```
## 1.4. 常用命令

进入你的 `docker-compose.yml` 所在目录后，可以使用以下命令：

| 命令                                  | 说明                     |
| ----------------------------------- | ---------------------- |
| docker compose up                   | 启动并创建所有服务容器（前台模式）      |
| docker compose up -d                | 后台启动容器                 |
| docker compose down                 | 停止并删除容器、网络（不删卷）        |
| docker compose ps                   | 查看运行中的服务状态             |
| docker compose logs                 | 查看容器日志                 |
| docker compose build                | 构建或重新构建服务              |
| docker compose stop                 | 停止服务                   |
| docker compose start                | 启动已停止的服务               |
| docker compose restart service-name | 重启单个服务                 |
| docker compose restart              | 重启所有服务                 |
| docker compose config               | 检查docker-compose.yml文件 |
| docker compose exec service-name sh | 进入容器命令行                |

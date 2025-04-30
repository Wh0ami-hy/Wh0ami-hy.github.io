---
layout: post   	
catalog: true 	
tags:
    - Docker
---
# 1. Dockerfile构建镜像

构建镜像，是以一个镜像为基础，在其上进行定制

镜像的定制实际上就是**定制每一层所添加的配置、文件**。我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，这个脚本就是 Dockerfile

Dockerfile 分为四部分：**基础镜像信息、维护者信息、镜像操作指令、容器启动执行指令**

注：禁止使用 docker commit定制镜像，使用 docker commit 意味着除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知

Dockerfile 是一个文本文件，其内包含了一条条的指令（Instruction），**每一条指令构建一层**，因此每一条指令的内容，就是**描述该层应当如何构建**

建立一个文本文件，并命名为 Dockerfile

```shell
touch Dockerfile
```

**基础知识：**

1、每个保留关键字（指令）都是必须是大写字母

2、执行从上到下顺序执行

3、`#`标识注释

4、每一个指令都会创建提交一个新的镜像层，并提交

**Docker 执行 Dockerfile 的大致流程：**

1、docker 从基础镜像运行一个容器
2、执行一条指令并对容器作出修改
3、执行类似 docker commit 的操作提交一个新的镜像层
4、docker 再基于刚提交的镜像运行一个新容器
5、执行 dockerfile 中的下一条指令直到所有指令都执行完成
## 1.1. 排除文件

**.dockerignore文件**

在项目的根目录下，新建一个文本文件`.dockerignore`，

```
.git
node_modules
npm-debug.log
```

上面代码表示，这三个路径要排除，不要打包进入 image 文件。如果你没有路径要排除，这个文件可以不新建。
## 1.2. 指定基础镜像（FROM）

**指定基础镜像，因此一个 `Dockerfile` 中 `FROM` 是必备的指令，并且必须是第一条指令**

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 `scratch`。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。如果以 `scratch` 为基础镜像的话，意味着不以任何镜像为基础

```
FROM <image>  
FROM <image>:<tag> 
FROM <image>:<digest>

三种写法，其中<tag>和<digest> 是可选项，如果没有选择，那么默认值为latest
```

## 1.3. 设置环境变量（ENV）

定义了环境变量，无论是后面的其它指令，还是运行时的应用，都可以直接使用定义的环境变量。

通过环境变量，可以让一份 `Dockerfile` 制作更多的镜像，只需使用不同的环境变量即可。

```
# 设置时区
ENV TZ=Asia/Shanghai
```
## 1.4. 设置工作目录（WORKDIR）

`WORKDIR <工作目录路径>`，设置后续指令（如 `RUN`, `CMD`, `COPY` 的**相对路径** ）的工作目录，WORKDIR 指令中需要使用绝对路径，如果指定的路径在镜像中不存在，WORKDIR 会自动创建该目录

如果后续命令使用**相对路径** ，则路径会基于 `WORKDIR 指令的绝对路径` 解析。

如果使用**绝对路径** （以 `/` 开头），则与 `WORKDIR` 无关。

```
FROM nginx
WORKDIR /project-nginx
# nginx.conf 被复制到了容器的 /etc/nginx/nginx.conf，而非 /project-nginx/etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/nginx.conf
# index.html 被复制到了容器的/project-nginx/html
COPY index.html ./html
```

## 1.5. 复制项目文件到容器（COPY 或 ADD）

**COPY**

`COPY <源路径> <目标路径>`指令用于将文件从主机复制到镜像中。它通常用于将应用程序代码和配置文件添加到镜像中。

`<目标路径>` 可以是容器内的绝对路径，也可以是相对于工作目录的相对路径。目标路径不需要事先创建，如果目录不存在会在复制文件前先行创建缺失目录。

需要注意，使用 `COPY` 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。这个特性对于镜像定制很有用

如果源路径为文件夹，复制的时候不是直接复制该文件夹，而是将文件夹中的内容复制到目标路径

**ADD**

与COPY类似，但还支持从URL中下载文件，并且可以自动解压压缩文件。

Dockerfile **最佳实践**中要求，所有的文件复制均使用 `COPY` 指令，仅在需要自动解压缩的场合使用 `ADD`。
## 1.6. 执行命令（RUN）

RUN指令执行命令，通常用于安装软件包或者配置环境。为了优化镜像大小和构建速度，建议合并多个RUN指令，并清理不必要的文件。RUN指令执行结果都会打包进入 image 文件

`RUN` 指令用来执行命令行的命令，其格式有两种：

- shell 格式：`RUN <命令>`，如 `RUN yum -y install vim`​
- exec 格式：`RUN ["可执行文件", "参数1", "参数2"]`，这更像是函数调用中的格式

**Dockerfile 支持 Shell 类的行尾添加 `\` 的命令换行方式，以及行首 `#` 进行注释的格式**

此外，通常要在一组命令的最后添加清理工作的命令 `apt-get purge -y --auto-remove $buildDeps`，删除为了编译构建所需要的软件，清理所有下载、展开的文件，并且清理 `apt` 缓存文件。这是很重要的一步，镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉
## 1.7. 暴露端口（EXPOSE）

声明容器运行时要监听的端口。`EXPOSE <端口>`

要将 `EXPOSE` 和在运行时使用 `-p <宿主端口>:<容器端口>` 区分开来。`-p` 是映射宿主端口和容器端口，也就是将容器的对应端口服务公开给外界访问，而 `EXPOSE` 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

在 Dockerfile 中使用 EXPOSE 声明的好处是在容器运行时，使用随机端口映射时，也就是 `docker run -P` 时，会自动映射 `EXPOSE` 的端口。
## 1.8. 数据挂载（VOLUME）

`VOLUME <路径>`  不能自动挂载宿主机目录，**只能声明挂载点，需配合运行时`-v`参数`docker run -v /host/path:/container/path  my_nginx_image`**

在 Dockerfile 中使用 `VOLUME /home/projects` 并不会自动将外部系统的`/home/projects` 目录挂载到容器中的`/home/projects`路径下。`VOLUME` 在 Dockerfile 中的作用是：声明一个挂载点（Mount Point），告诉 Docker 这个路径用于挂载外部存储卷。

触发容器运行时创建匿名卷 ：如果没有在运行时显式绑定宿主机目录（通过 `-v` 或 `--mount`），Docker 会自动为该路径分配一个匿名卷（Anonymous Volume）。
## 1.9. 定义默认命令（CMD）

CMD 指令用于指定容器启动时默认执行的命令。每个 Dockerfile 只能有一个 CMD 指令

```
#定义运行容器时执行的命令
CMD ["python", "app.py"]
```
## 1.10. 入口点（ENTERPOINT）可选

如果你的应用程序有固定的启动命令，可以使用ENTRYPOINT 来定义不可变的入口点。CMD提供的参数会被追加到ENTRYPOINT后面作为参数传递

```
语法：ENTRYPOINT ["executable", "param1", "param2"]
# ENTRYPOINT ["flask", "run", "--host=0.0.0.0"]
```
## 1.11. 其他可选

**USER**

设置启动容器的用户，可以是用户名或UID，不设置则默认root用户

**ARG**

构建参数和 `ENV` 的效果一样，都是设置环境变量。所不同的是，`ARG` 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。

**LABEL** 

添加元数据标签

```
# 作者
LABEL maintainer="yatoil"
# 描述
LABEL description="A multi-stage Dockerfile for a Flask app with Nginx reverse proxy"
```

## 1.12. 构建镜像（build）

docker build 命令会根据 Dockerfile 文件及上下文构建新 Docker 镜像。**构建上下文**是指 Dockerfile 所在的本地路径或一个URL（Git仓库地址）。构建上下文环境会被递归处理，所以构建所指定的路径还包括了子目录，而URL还包括了其中指定的子模块。

```shell
docker build [选项] <上下文路径/URL/->
#  `.` 表示当前目录，Docker 会在这个目录下查找名为 `dockerfile-nginx` 的文件。
docker build -f "dockerfile-nginx" -t "my-nginx-image" .
```

构建前，构建进程会将全部内容（递归）发送到守护进程。**大多情况下，应该将一个空目录作为构建上下文环境，并将 Dockerfile 文件放在该目录下。**

在构建上下文中使用的 Dockerfile 文件，是一个构建指令文件。在 Docker 构建镜像的第一步，docker CLI 会先在上下文目录中寻找`.dockerignore`文件，根据`.dockerignore` 文件排除上下文目录中的部分文件和目录，然后把剩下的文件和目录传递给 Docker 服务。

Dockerfile 一般位于构建上下文的根目录下，也可以通过`-f`指定该文件的位置：

```sh
docker build -f /path/to/a/Dockerfile .
```

构建时，还可以通过`-t`参数指定构建成镜像的仓库、标签。

**多阶段构建**

...待补充

# 2. 容器运行

```
docker run -d -p 6688:6688 my-nginx-image
```

# 3. 注意

Dockerfile中的`VOLUME`、`EXPOSE`仅是声明作用，实际要在docker run 时的 `-v`、`-p`参数实现

要么Dockerfile 中使用 `VOLUME`，要么容器run运行时使用 `-v`，不要同时使用
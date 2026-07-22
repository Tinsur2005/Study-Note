# Docker入门

## 一、Docker介绍

**Docker**是一个快速构建、运行、管理应用的工具

在各种组件安装时，随着各种中间件都需要配置，使用Linux下面的命令安装就会变得非常麻烦。

如果是利用传统方式部署MySQL，大概的步骤有：

1. 搜索并下载MySQL安装包
2. 上传至Linux环境
3. 编译和配置环境
4. 安装

Docker技术能够避免部署对服务器环境的依赖，减少复杂的部署流程，使用Docker能轻松部署各种常见软件、Java项目等，大大减少了运维工作量。使用Docker安装Mysql往往只需要如下一步：

```bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql:latest
```

---

## 二、Docker容器化

Docker 的C/S客户端-服务端架构（如图）：用户在客户端 docker-cli 执行各类 docker 命令，指令会发送给 Docker 主机内的 Docker 守护进程 Docker Daemon；守护进程作为核心服务，既可以从远程镜像仓库 Registry 拉取镜像到本地，也能基于本地镜像创建运行容器，同时支持在本地构建自定义镜像，或是将本地镜像推送至镜像仓库实现共享；其中镜像是静态只读的软件模板，容器是镜像运行后生成的实例。

**守护进程（Daemon）**是在后台运行的特殊进程，用于执行特定系统任务，通常在系统引导时启动并持续运行，或在需要时启动后自动结束。

![image-20260721204117322](./../assets/2-Docker%E5%85%A5%E9%97%A8/image-20260721204117322.png)

![image-20260721204232685](./../assets/2-Docker%E5%85%A5%E9%97%A8/image-20260721204232685.png)

1. `docker run` 每次都会重新创建一个容器 
2. `docker start` 只是启动容器中被停掉的进程
3. `docker ps` 查看当前容器的运行状态
4. `docker rmi` 删除镜像，这个i就是image的意思
5. `docker rm` 删除容器  `docker rm -f` 可以强制删除正在运行的容器

### 1、Docker主机、客户端、应用市场

首先要在电脑上安装**Docker环境**，安装了**Docker环境**的机器叫**Docker主机**，又叫**Docker Host**，安装了Docker后，Docker的后台进程**DockerDaemon**就会一直运行，时刻准备服务；

Docker还提供了一个客户端**docker-cli**用来操作Docker主机的后台进程；

Docker还有一个**应用市场**，应用市场中的软件叫做**镜像**。

### 2、镜像、容器、镜像仓库

**镜像：**将应用所需的函数库、依赖、配置等与应用一起打包得到的就是镜像

**容器：**为每个镜像的应用进程创建的隔离运行环境就是容器

**镜像仓库：**存储和管理镜像的服务就是镜像仓库，`DockerHub`是目前最大的镜像仓库，其中包含各种常见的应用镜像 `https://hub.docker.com/`

DockerHub网站是官方仓库，阿里云、华为云会提供一些第三方仓库，我们也可以自己搭建私有的镜像仓库。

---

## 三、安装Docker

### 1、配置Docker的yum库

**YUM**是CentOS/RHEL系列Linux的软件包管理工具，可以`自动下载`安装软件并解决依赖关系。

```bash
sudo yum install -y yum-utils
```

配置Docker的下载地址源

```bash
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 2、安装Docker

执行以下命令安装Docker：

```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

可以使用`docker -v`查看是否安装成功

### 3、启动Docker

目前无法连接到Docker的守护进程，Docker本身也是一个服务，需要启动起来一直运行；

我们输入的命令就是发送给这个进程，由这个进程处理我们的命令：

```bash
# 启动Docker
systemctl start docker
# 停止Docker
systemctl stop docker
# 重启
systemctl restart docker
# 设置开机自启，就不需要每次自己启动了
systemctl enable docker --now
```

### 4、配置镜像加速

默认的DockerHub下载速度比较慢，可以配置国内镜像地址，镜像地址可能会失效或变更。

阿里云镜像加速地址获取：`https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors`

配置镜像步骤如下，此处的镜像地址可能会变化，每次配置都需要去上方链接获取最新地址：

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://uoe2o5ov.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 5、卸载Docker

可使用以下命令卸载Docker：

```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

## 四、示例：安装Mysql数据库

使用以下命令安装Mysql：

```bash
docker run -d \
  --name mysql \
  --restart=always \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=1234 \
  mysql
```

**docker run：**创建并运行一个容器，`-d`是让容器在后台运行

**--name mysql：**给容器起个名字，**必须唯一**

**--restart=always：**崩溃后自动重启

**-p 3306:3306：**设置端口映射

​	`-p`代表宿主机端口:容器内端口，MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内一致。

**mysql：**设置镜像名称，Docker会根据这个名字搜索并下载镜像

![image-20260721204241252](./../assets/2-Docker%E5%85%A5%E9%97%A8/image-20260721204241252.png)


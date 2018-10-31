---
layout: post
title: Docker 入门 - 第一部分- 方向和设置
tags: tool docker
date: 2018-10-28 10:13:00
image: /img/docker_getstarted.png
---

 欢迎, 很高兴你能学习 Docker. Docker 入门向导教你如何:

 1. 搭建 Docker 环境
 2. 建造一个镜像并在容器中运行
 3. 扩展你的应用以运行多个容器
 4. 在集群中分发你的应用
 5. 在后台添加数据库来堆叠服务
 6. 将你的应用部署到生产环境

 ## Docker 思想

 Docker 是一个可以让开发者, 系统管理员在容器中开发, 部署和运行应用的一个平台. 利用 Linux 容器来部署应用称为容器化. 容器并不是一个新鲜事物, 但是用它如此容易的部署应用却是.

 容器化快速流行的原因是:

 * 灵活性: 最复杂的应用也可以被容器化
 * 轻量性: 容器借用并分享主机内核
 * 互换性: 可随时部署更新和升级
 * 便携性: 本地建造, 部署到云端, 然后在任何地方运行
 * 扩展性: 可增加并自动分发容器副本
 * 堆叠性: 可随时垂直堆叠服务

 ![](https://docs.docker.com/get-started/images/laurel-docker-containers.png)

 ### 镜像和容器

 启动一个容器就意味着运行一个镜像. 镜像是一个可以执行的组件, 包括运行应用所需的所有需要的东西, 如代码, 运行时库, 环境变量和配置文件等.

 容器是镜像的一个运行时实例, 就是镜像在内存中被执行. 你可以使用 `docker ps` 查看运行的容器列表, 就像在 Linux 中一样.

 ### 容器和虚拟机

 在 Linux 中, 容器在本地运行, 并跟其他容器分享主机内核. 容器运行分离的进程, 不会比其他可执行程序占用更多的内存, 来实现轻量化.

 对比来说, 虚拟机运行一个完全独立的客操作系统, 通过虚拟机管理程序使用虚拟化技术访问主机资源. 通常, 一个虚拟机比多个应用程序占用更多的资源.

 ![](https://www.docker.com/sites/default/files/Container%402x.png)

 ## 准备 Docker 环境

 在[支持的平台](https://docs.docker.com/engine/installation/#supported-platforms)上安装一个 Docker 社区版或企业版的[维护版本](https://docs.docker.com/engine/installation/#updates-and-patches).

 > **完整的 Kubernetes 集成**
 > * [Mac 的 Docker Kubernetes](https://docs.docker.com/docker-for-mac/kubernetes/) 使用 [17.12 Edge (mac45)](https://docs.docker.com/docker-for-mac/edge-release-notes/#docker-community-edition-17120-ce-mac45-2018-01-05) 或 [17.12 Stable (mac46)](https://docs.docker.com/docker-for-mac/release-notes/#docker-community-edition-17120-ce-mac46-2018-01-09) 及更高版本.
 > * [Windows 的 Docker Kubernetes](https://docs.docker.com/docker-for-windows/kubernetes/) 使用 [18.02 Edge (win50) ](https://docs.docker.com/docker-for-windows/edge-release-notes/#docker-community-edition-18020-ce-rc1-win50-2018-01-26) 及更高版本.

[安装 Docker](https://docs.docker.com/engine/installation/)

### 测试 Docker 版本

1. 运行 `docker --version` 确保你安装的的是Docker的支持版本. 

```
docker --version

Docker version 17.12.0-ce, build c97c6d6
```

2. 运行 `docker info` 或 (`docker version` 没有 `--`) 查看 Docker 的详细安装信息.

```
docker info

Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 17.12.0-ce
Storage Driver: overlay2
...
```

> 为了避免权限问题(不使用 `sudo`), 将你的用户名添加到 `docker` 用户组中, [查看更多](https://docs.docker.com/engine/installation/linux/linux-postinstall/).

### 测试 Docker 安装

1. 运行一个简单的 `hello-world` 镜像测试 Docker 的安装:

```
docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:ca0eeb6fb05351dfc8759c20733c91def84cb8007aa89a5bf606bc8b315b9fc7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

2. 列出下载到你主机的 `hello-world` 镜像

```
docker image ls
```

3. 列出 `hello-world` 容器, 这个容器打印消息后会自动退出. 如果他仍在运行, 你不需要使用 `--all` 选项.

```
docker container ls --all

CONTAINER ID     IMAGE           COMMAND      CREATED            STATUS
54f4984ed6a8     hello-world     "/hello"     20 seconds ago     Exited (0) 19 seconds ago
```

## 回顾备忘单

```
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```
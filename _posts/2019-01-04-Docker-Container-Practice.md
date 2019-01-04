---
layout: post
tile: Docker 容器使用
tags: tool docker
date: 2019-01-04 9:00:00
image: https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQKn-Fk3kQFfliaphhK8KC_7U-UQqN5dY2uXgWPgiT6JuH544J7
---

## 自定义容器

Dockerfile -> image -> container.

### Dockerfile

如果我们有一个开发环境，我们可以将该开发环境所需要的依赖包都写入 Dockerfile. 例如， Ardupilot 的[开发环境安装指南](http://ardupilot.org/dev/docs/building-setup-linux.html)，我们可以将其写入容器，供其他开发者直接使用，省去了每人都需要重新配置开发环境的烦恼。

请参考[beandrewang/ardupilot](https://hub.docker.com/r/beandrewang/ardupilot), 
[Dockerfile](https://github.com/beandrewang/dockers/blob/master/ardupilot/Dockerfile), 将其摘录如下
[修改后的install-preques-ubuntu.sh](https://github.com/beandrewang/dockers/blob/master/ardupilot/install-preques-ubuntu.sh).

```dockerfile
# 制定平台为 ubuntu 18.04
FROM ubuntu:bionic

# 这一步非常关键， 如果不设置时区，在安装的过程中，会要求我们手动输入时区，按照我的经验，Docker的安装应该是自动的， 
# 即使我们想手动输入， 在这一步 Docker build 就会卡死
RUN echo 'Etc/UTC' > /etc/timezone && \
    ln -s /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    apt-get update && apt-get install -q -y tzdata \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# 将 ardupilot 的环境安装脚本修改后拷贝到image的根目录
COPY ./FROM ubuntu:bionic

RUN echo 'Etc/UTC' > /etc/timezone && \
    ln -s /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    apt-get update && apt-get install -q -y tzdata \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# setup entrypoint
COPY ./install-preques-ubuntu.sh /
RUN chmod -x ./install-preques-ubuntu.sh \
    && bash ./install-preques-ubuntu.sh -y
# 修改权限，执行安装脚本
RUN chmod -x ./install-preques-ubuntu.sh \
    && bash ./install-preques-ubuntu.sh -y

ENTRYPOINT ["bash"]
```

有了 Dockerfile后， 我们可以 build image 了。 方法有两种。

### 本地 build 

cd 到 Dockerfile 所在目录， 执行，

```
docker build -t yourimagename:tag .
```

执行完成后，我们可以查看

```
docker images
```

### 使用 Docker hub build

将 Dockerfile 上传到 github, 然后使用 docker hub 的 [auto build](https://docs.docker.com/docker-hub/builds/) 功能。

## 分享容器

### 本地打包

```
$ docker save busybox > busybox.tar

$ ls -sh busybox.tar

2.7M busybox.tar

$ docker save --output busybox.tar busybox

$ ls -sh busybox.tar

2.7M busybox.tar

$ docker save -o fedora-all.tar fedora

$ docker save -o fedora-latest.tar fedora:latest
```

如果其他人收到你打包的容器后， 怎么使用

```
docker load --input busybox.tar
```

### 上传 docker hub

1. [注册 dockerhub](https://hub.docker.com/signup)
2. 创建 repository
3. docker login --username yourusername --email youemail
4. tag your image

```
docker tag bb38976d03cf yourhubusername/verse_gapminder:firsttry
```

4. push you image to docker hub

```
docker push yourhubusername/verse_gapminder
```

别人怎么用，

```
docker pull yourhubusername/verse_gapminder
```

## 使用 GUI

在 docker 中， 使用 GUI 有两种方法。

### 方法一， VNC

这种方法，需要将VNC server 安装到 image 中， 然后再将桌面系统安装进去。 这样通过浏览器就可以使用 GUI了。 缺点也很明显，镜像太大了。

[docker-ubuntu-vnc-desktop](https://github.com/fcwu/docker-ubuntu-vnc-desktop)

### 方法二， X11

使用本地主机的X11 Server, 这种方法比较直接，但是 Windows 需要稍微麻烦一点。

* Ubuntu

```
xhost +local:docker

docker run -it --rm \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    beandrewang/ros2
```

* Windows

1. Install [`Xming`](https://sourceforge.net/projects/xming/)
2. Add your ip such as `192.168.0.5` in to `c:\Program Files (x86)\Xming\x0.hosts file`

```
set DISPLAY=192.168.0.5:0.0
docker run -it --rm \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    beandrewang/ros2
```

## 打开多个终端

容器启动起来后，只会启动一个终端，那么我们又需要在多个终端执行相应的指令或程序。 我们可以使用 

`docker exec -it yourcontainer `

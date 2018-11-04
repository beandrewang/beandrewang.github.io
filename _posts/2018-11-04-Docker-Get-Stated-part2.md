---
layout: post
title: Docker 入门 - 第一部分- 方向和设置
tags: tool docker
date: 2018-10-28 10:13:00
image: /img/docker_getstarted.png
---

## 前提条件

* 安装 [Dodcker 1.13 或更高版本](https://docs.docker.com/engine/installation/)。
* 阅读第一部分[方向与设置](https://beandrewang.github.io/2018-10-28-Docker-Get-Stated-part1/)
* 快速测试安装环境，确保安装环境运行正确。

```
docker run hello-world
```

## 介绍

是时候按照 Docker 的方式创建一个应用了。 我们从这个体系的最底层 - 容器开始。 在这一层之上是服务 - 定义了容器在生产中是如何工作的， 这个将在第三部分介绍。 最上层是栈 - 定义了所有的服务之间是如何交互的，将在第五部分介绍。

* 栈
* 服务
* 容器 （你在这里）

## 新的开发环境

过去， 如果你想开始写一个 Python 应用， 第一项工作就是要往你的主机上安装一个 Python 环境。 但是， 这就要求你主机上的开发环境要匹配应用运行的需求， 同时还要匹配生产环境。

用 Docker， 你可以拉一个便携的 Python 环境作为镜像， 根本不需要任何的安装。 然后， 你可以在这个基础的 Python 镜像上编码， 确保你的应用及其依赖， 运行时环境在一起。

这个便携的镜像有时被定义为 `Dockerfile`.

### 使用 `Dockerfile` 定义容器

`Dockerfile` 定义了容器内环境中的内容。 在这个环境内，访问象网络，硬盘等资源是虚拟化的， 跟系统的其他部分是隔离开的。 所以你需要将端口映射到外边， 并且要指定哪些文件需要拷贝到这个环境中去。 但是， 做完这些工作之后， 我们可以期望在 `Dockefile` 中定义的建造的应用跟在其他任何地方运行一样。

**`Dockerfile`**

创建一个空路径， `cd` 到这个路径， 创建一个命名为 `Dockefile` 的文件， 复制并粘贴一下的内容， 保存文件。 注意 Dockefile 中的每一句注释。

```
# Use an official Python runtime as a parent image
# 使用官方 Python 运行时环境作为父镜像
FROM python:2.7-slim

# Set the working directory to /app
# 将工作目录设置到 /app
WORKDIR /app

# Copy the current directory contents into the container at /app
# 将当前目录的内容拷贝到容器的 /app 目录中去
COPY . /app

# Install any needed packages specified in requirements.txt
# 安装所有在 requriements.txt 文件中指定的所有包
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
# 将容器的80端口作为外部的访问端口
EXPOSE 80

# Define environment variable
# 定义环境变量
ENV NAME World

# Run app.py when the container launches
# 在容器启动时运行 app.py
CMD ["python", "app.py"]
```

### 应用本身

创建两个文件， `requirements.txt` 和 `app.py`. 将他们放到 `Dockerfile` 所在的目录。 我们的应用就完成了，就像你看到的，这非常简单。 当上边的 `Dockerfile` 被创建到一个镜像中时， 因为 `Dockerfile` 内的 `COPY` 命令， `app.py` 和 `requirements.txt`会被拷贝到镜像中， `app.py` 的输出会从 HTTP 输出， 因为 ESPOSE 命令。

**`requirements.txt`**

```
Flask
Redis
```

**`app.py`**

```python
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

现在我们看到 `pip install -r requirements.txt` 安装了 Python 的 Flask 和 Redis 库， 并且这个应用安装了环境变量 `NAME`, 因为 调用了 `socket.gethostname()`. 最后， 因为我们只是安装了 Redis 库， 而没有安装整个 Redis, 这个应用将会输出错误。

> **注意**： 

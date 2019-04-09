# Docker 初识

[TOC]



### Docker基本概念 --  2019.04.10

* 灵活性： 几乎所有复杂的应用都可以容器化
* 轻量级：容器之间会共享主机的内核
* 通用：可以随时部署更新和升级
* 可移植性：可以本地构建，发布到云上，并在任意地方运行它
* 可编排性：你可以增加并自动分发容器副本
* 可堆叠性：可以随时的将不同应用进行组合

### Docker和虚拟机区别

* 一个**容器**是直接跑在linux上并且可以共享宿主机的内核，是以进程的形式执行的

* **虚拟机**是通过hypervisor完整的“guest”操作系统，但是VMs会占用比应用所需更多的资源

  ![Docker VS Virtual Machine](https://docs.docker.com/images/Container%402x.png)

### 容器

学习如何通过Docker的方式构建一个app。可以通过一个Dockerfile定义Docker容器。

```dockerfile
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

接下来就是建立上面用到的`requirements.txt`和`app.py`文件。

```bash
$ ls
Dockerfile		app.py			requirements.txt
```

现在可以使用build命令创建一个Docker 镜像。 我们可以使用`--tag`,或者是`-t`

```bash
docker build --tag=friendlyhello .
```

可以通过下面的命令，在本地Docker镜像仓库中找到你新建的镜像

```bash
$ docker image ls

REPOSITORY            TAG                 IMAGE ID
friendlyhello         latest              326387cea398
```

我们可以发现tag默认是 `lastest`, 完整的tag选项语法是这样的`--tag=friendlyhell:v0.0.1`

>代理服务器和DNS服务器都是可以设置的
>
>代理服务器可以隐藏你的真实应用的地址，并且可以通过Dockerfile进行定义
>
>```dockerfile
># Set proxy server, replace host:port with values for your servers
>ENV http_proxy host:port
>ENV https_proxy host:port
>```
>
>DNS 配置
>
>DNS没有设置的话，会造成`pip`的问题，所以需要给`pip`命令正常工作提供DNS环境。可以通过设置`/etc/docker/deamon.json`文件的`dns`字段
>
>```json
>{
>  "dns": ["your_dns_address", "8.8.8.8"]
>}
>```

运行app

我们可以通过宿主机的4000端口来映射Docker容器的80端口，命令如下

```bash
docker run -p 4000:80 friendlyhello
```

你会看到你的python应用已经运行于 `http://0.0.0.0:80` ,这是因为这个信息是来自于Docker容器内部的，而没有显示出你的映射关系。

接着去你的浏览器，输入`localhost:4000` 就可以看到web页面了

![Hello World in browser](https://docs.docker.com/get-started/images/app-in-browser.png)

当然也可以使用`curl`命令来获取到同样的内容

```bash
$ curl http://localhost:4000

<h3>Hello World!</h3><b>Hostname:</b> 8fc990912a14<br/><b>Visits:</b> <i>cannot connect to Redis, counter disabled</i>
```


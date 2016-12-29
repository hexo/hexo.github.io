---
title: docker简介和安装
date: 2016-12-04 21:33:55
tags:
categories:  Linux
---
最近对docker技术产生了兴趣，准备利用业余时间进行学习，但随着学习的深入，博主越来越觉得这个技术有点意思，所以决定做一个连载的学习日志，记录下自己的学习历程

### 什么是Docker

Docker是基于Go语言实现的云开源项目，主要目标是“Build, Ship and Run Any App, Anywhere”,即通过对应用组件的封装(Packaging),分发(Distribution),部署(Distribution),运行(Runtime)等生命周期的管理，达到应用组件级别的“一次封装，到处运行”。这里的应用组件，既可以是一个web应用，也可以是一套数据库服务，甚至是一个操作系统或者编译器
读起来就感觉很厉害的样子，让博主想起了JAVA的一次编译多处运行，经过深入的了解，发现这个东西其实本质上就是强大版的Git

### Docker和虚拟机的关系

作为一种轻量级的虚拟化方式，Docker在运行应用上和传统的虚拟机方式相比具有显著的优势

Docker容器很快，启动和停止可以在秒级实现
Docker容器对于系统资源需求很少，一台主机上可以同时运行数千个Docker容器
Docker通过类似Git的操作来方便用户获取，分发，和更新应用镜像，指令简明，学习成本低
Docker通过Dockerfile配置文件来支持灵活的自动化创建和部署机制，提高工作效率
Docker引入安全选项和镜像签名机制，极大的提高了使用Docker的安全性

### Docker的核心概念

#### Docker镜像

类似于虚拟机镜像，可以将它理解为一个面向Docker引擎的只读模板，包含了文件系统，镜像是创建Docker容器的基础

#### Docker容器

Docker容器类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。容器是从镜像创建的应用运行实例，可以将其启动，开始，停止，删除，这些容器都是相互隔离的，互不可见的

#### Docker仓库

Docker仓库类似于代码仓库，是Docker集中存放镜像文件的场所，注册服务器用来存放仓库，每个仓库集中存放某一类镜像，往往包括多个镜像文件，例如存放Ubuntu操作系统镜像的仓库称为Ubuntu仓库，这些镜像通过Tag来进行区别。根据存储的的镜像公开与否，Docker仓库可以分为公开仓库和私有仓库两种形式

### 安装Docker

[官网安装教程](https://docs.docker.com/engine/installation/linux/)

### 镜像

Docker运行容器前需要本地存在对应的镜像，如果镜像不在本地，Docker会尝试先从默认镜像仓库下载（默认使用DockerHub公共注册服务器中的仓库），用户也可以通过配置，使用自定义的镜像仓库

```
$ sudo docker pull ubuntu
```
此处默认下载tag为latest的镜像，如果制定某一镜像可以通过：tagname 来实现
还可以选择从其他注册服务器的仓库下载，此时，需要在仓库名称前指定完整的仓库注册服务器地址。例如从DockerPool社区的镜像源dl.dockerpool.com下载最新的Ubuntu镜像
```
$ sudo docker pull dl.dockerpool.com:5000/ubuntu
```
下载镜像到本地后，即可以使用该镜像了，例如使用该镜像创建一个容器，在其中运行bash应用

```
$ sudo docker run -t -i ubuntu /bin/bash
```

### 查看镜像信息

```
$ sudo docker images
```
通过docker inspect 命令可以获取某一个镜像的详细信息
```
$ sudo docker inspect imageID
```

### 搜寻镜像

使用docker search 命令可以搜索远端仓库中共享的镜像，默认搜索Docker Hub官方仓库中的镜像
```
$ sudo docker search mysql
```
根据关键字进行搜索，结果按照星星数目进行排序

### 上传镜像

可以使用docker push 命令上传镜像到仓库，默认上传到Docker Hub官方仓库（需要提前登陆）
登陆：
```
$ docker login
$ username:
$ password:
##登出 docker logout
```

```
$ sudo docker tag test:latest user/test:latest
$ sudo docker push user/test:latest
```

### 其他命令

- sudo docker rmi imageID/Tag :删除镜像，如果想强行删除有正在运行容器的镜像则使用-f参数
- sudo docker commit -m ‘提交信息’ -a ‘作者信息’ 容器名 提交的镜像名 ： 这条命令是用来把某个容器的内容来提交为一个新的镜像
- sudo docker ps -a:查看本机上存在的所有容器
- sudo docker save -o 导出文件.tar ubuntu:14.04 :导出镜像到文件中
- sudo docker load < ubuntu_14.04.tar :从文件中载入镜像

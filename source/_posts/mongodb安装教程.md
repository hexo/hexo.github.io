---
title: mongodb安装教程
date: 2016-10-11 11:20:49
tags:
categories: 数据库
---
### 写在前面
本文承接上文，mongodb权限管理，采用第一种安装方法　流程为mongodb shell安装并设置全局变量 （centos,其他系统类似）

### 安装
- 打开官网download页面，查看相应版本的 url ，然后shell中
```
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-amazon-3.2.6.tgz
```
- 解压
```
tar zxvf mongodb-linux-x86_64-amazon-3.2.6.tgz
```
- 修改文件名为mongodb
```
mv mongodb-linux-x86_64-amazon-3.2.6.tgz mongodb
```
至此，mongodb安装完毕

### 运行mongodb
首先明确几点

- mongodb 服务器密切相关的有两个命令， mongod 和 mongo （作用看第三条），但这两个命令并不能全局使用，因为木有注册嘛。。。
- mongod 和 mongo 可执行文件位于 mongodb/bin 目录下
- mongod 用于启动 mongodb的服务器 ， mongo 用于数据库操作
- mongod启动时默认将数据库文件存放于/data/db，即机器（或当前盘符）的根目录 data/db 中 ，如果根目录下没有data文件夹或其中db文件夹，mongod不会去创建 ，所以会导致启动不成功
- mongod启动时，可以以配置文件或配置命令方式进行配置（本文主要介绍配置文件方式）

启动mongodb
```
cd bin && ./mongod
```
不出意外，会收到错误信息，告知启动失败，找不到 /data/db 嘛
回到 mongodb目录 ， 新建配置文件 mongodb.conf
```
cd ../ && vi mongodb.conf
```
在文件中写入一下代码
```
dbpath = ../data/db //设置db路径
port = 27017 //设置端口
auth = true //设置开启权限
```
保存后执行步骤1的命令，但这时候把配置文件加上
```
cd bin
./mongod --config ../mongodb.conf
```
会打印出信息，最后告诉你成功了。好啦，现在咱们就可以打开 mongo 进行用户和数据库管理啦。
新开一个窗口然后在bin目录下 运行
```
./mongo
```
会进入mongo shell界面
至此mongodb的启动配置和启动已经完成。

### 全局配置mongodb

Unix加载环境变量来自3种配置文件：
  1.首先加载 /etc/profile 系统全局变量.
  2.接着是其他系统配置文件 如 /etc/bashrc
  3.用户自定义配置文件  ~/.bash_profile
  4.用户自定义配置文件 ~/.bashrc
  关于这几个配置文件的区别见linux配置文件那篇博文，说明的很详细
```
vi ~/.bash_profile
```
在文件中写入以下代码
```
export PATH=xxx:$PATH
```
xxx代表你机器上相应的 mongodb/bin的绝对路径 ，我的机器是
```
export PATH=/search/nginx/html/mongodb/bin:$PATH
```
编辑完成后保存， 任意目录下执行 mongod ，依然报错，但并不是报的 没有 mongod这个变量，而是找不到/data/db 的错误 ，这证明咱们的全局配置还是有效的，只是运行mongodb的配置文件需要修正一下。
配置文件方式启动mongodb，及conf文件配置
理论上代码是这样的：
```
mongod --config xxx.conf
```
那咱们就搞个绝对路径放这，就能保证任意文件夹下都能执行 xxx.conf 啦。就拿咱们之前配置的mongodb.conf 来说，咱们应该这样写：
```
mongod --config /search/nginx/html/mongodb/mongodb.conf
```
mongodb.conf 需要修改一下，将dbpath也修改为绝对路径
```
dbpath = /search/nginx/html/mongodb/data/db
```
运行后，成功
每次都输 ` mongod --config /search/nginx/html/mongodb/mongodb.conf ` 这么一长串也不爽， 给它起个别名
```
alias runmongod='mongod --config /search/nginx/html/mongodb/mongodb.conf
```
这个关闭终端就没了，所以我们需要把它放到用户全局配置中 ,执行
```
vi ~/.bashrc
```
在其中写入
```
alias runmongod='mongod --config /search/nginx/html/mongodb/mongodb.conf'
```
保存并重启终端，就可以任意目录 通过 runmongod 来启动 mongod了， mongo启动， 还是输入 mongo即可

现在我们可以成功的启动mongodb了，接下来就是如何关闭mongodb，执行
```
vi ~/.bashrc
```
写入
```
alias stopmongod='mongo —dbpath /search/nginx/html/mongodb/data/db —shutdown'
```
这样就可以在任意目录下输入
```
stopmongod
```
来结束运行mongodb

### 博主的思考
看了这么多是不是有点晕呢，这也是博主踩坑最多的地方，想当初在公司实习的时候光弄这个就弄了好几天。确实没有方法二来的容易，很多需要自己配置的内容，不过也增加了不少对于mongodb的理解，还是很有意义的，不过博主还是强烈推荐采用官网上方法二的安装步骤哦

---
title: mongodb 3.2.6 权限管理
date: 2016-10-11 10:00:29
tags:
categories: 数据库
---
### 写在前面
mongodb作为一款优秀的非关系型数据库，得到的广泛的应用，发展也是很迅猛，随着版本迭代的速度越来越快，尤其是2.0～3.0 的版本迭代，改变了大量的内容，甚至shell操作命令都进行了修改，所以网上一些老的教程很容易给刚刚接触mongodb的童鞋带来困扰。本教程就把自己踩过的坑交代一下，虽然主要内容是关于mongodb的权限管理，但不止于权限管理，希望对大家有所帮助
### 安装
#### 方法一
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
#### 方法二
打开官网，以博主的系统Ubuntu 14.04LTS为例，其他系统也均可找到相应的安装方式，推荐此种方法
https://docs.mongodb.com/master/tutorial/install-mongodb-on-ubuntu/?_ga=1.257365259.1896541923.1467683775
按照步骤一步步完成即可
### 运行mongodb
需要注意的是，上面这两种安装方式是有很大不同的，博主两种方法都试过，本文后面的流程都是采用第二种方法，以此为前提，至于第一种方法，后面会有新的博文单独讲解
- 启动mongodb ： sudo service mongod started
- 停止mongodb ： sudo service mongod stop
- 重启mongodb ： sudo service mongod restart
- 进入shell ： 启动mongodb后在命令行输入 mongo

### Shell 操作
- show dbs： 显示当前数据库
- use yourdb： 选择某个数据库
- db.dropDatabase() : 删除当前使用的数据库

### 权限管理
mongodb默认不开启安全授权机制，需要在启动mongod时指定 -auth或者在配置文件里设置auth来启用这个特性
在配置文件里 /etc/mongod.conf 设置security： authorization: enabled
开启后在登录时url规范是 'mongodb://username:password@ipaddress:port/databaseName'
mongodb权限管理部分有一个很大的坑，就是我们在安装好mongodb时，一定要先创建一个admin数据库，作为管理数据库成员的数据库，然后开启权限，否则就会产生开启权限后，shell连接失败的结果

#### 创建admin数据库
```
use admin #创建admin数据库（use 后面的数据库如果不存在就会自动创建）
db.system.users.find() #查看数据库中所有用户的信息
```

#### 数据库角色
既然需要权限管理，那么问题来了，数据库有哪些角色？
- 数据库用户角色
- 数据库管理角色
- admin数据库中有额外角色

##### 数据库用户角色
针对每一个数据库进行控制
- read：可以读取所有非系统集合，以及系统集合中的system.indexes, system.js, system.namespaces
- readWrite: 包含了所有read权限，以及修改所有非系统集合，以及系统集合system.js的权限

##### 数据库管理角色
每一个数据库包含了下面的数据库管理角色
- dbAdmin： 对数据库对象的管理操作，没有数据库的读写权限
- dbOwner： 该数据库的拥有者，具有该数据库的全部权限
- userAdmin： 为当前用户创建，修改用户和角色，拥有userAdmin的用户可以将该数据库的任意权限赋予任意的用户

##### admin数据库角色
集群管理角色:用户管理整个系统，而非单个数据库
- clusterAdmin：最大的集群管理功能，相当于下面三个的集合
- clusterManager：提供了集群和复制集管理和监控操作，拥有该权限的用户可以操作config和local数据库（即分片和复制功能）
- clusterMonitor： 仅仅监控集群和复制集
- hostManager：提供了监控和管理服务器的权限，包括shutdown节点，logrotate，repairDatabase

备份和恢复角色
- backup： 备份
- restore： 恢复

所有数据库角色
- readAnyDatabase：具有read每一个数据库的权限，但是不包括应用到集群中的数据库
- readWriteAnyDatabse：具有readWrite每一个数据库的权限，但是不包括应用到集群中的数据库
- userAdminAnyDatabase： 具有userAdmin每一个数据库的权限，但是不包括应用到集群中的数据库
- dbAdminAnyDatabase：具有dbAdmin每一个数据库的权限，但是不包括应用到集群中的数据库

超级管理员角色
- root ：具有dbadmin到admin数据库，useadmin到admin数据库以及UserAdminAnyDatabase，但是不具有备份恢复，直接操作system.

#### 创建用户并赋予角色权限
db.creatUser(user,pwd,customData,roles)
- user:用户的名字
- pwd: 用户的密码
- customData: 为任意内容，例如可以为用户全名介绍
- roles: 指定用户的角色，数组的形式

举个栗子：在admin数据库下创建一个管理员用户
```
db.createUser({
user:"test",
pwd:"123",
roles:
[
{
role:"userAdminAnyDatabase",
db:"admin"
},
{
role:"readWriteAnyDatabase",
db:"admin"
},
{
role:"dbAdminAnyDatabase",
db:"admin"
}
]})
```
创建某个数据库的权限，首先要使用use dbname到对应的数据库目录下
```
db.createUser({
user:"test2",
pwd:"123",
roles:[{role:"read",
db:"test"
}
]})
```
#### 如何验证权限

注意：用户是对应到某个数据库的，在指定的库中授权，就要在指定的库中验证,哪里创建，哪里认证！
我们已经创建了用户下面就要用验证的方式，进入某一个数据库，通过
```
db.auth("username","password")
```
方式进行验证，验证结果为1为验证成功
同时也可以在某个数据库下进行如下操作
- db.getUsers() :获得数据库的所有用户权限信息
- db.getUser():获取数据库的某个用户的权限信息
- db.dropUser():删除某个用户
- db.dropAllUsers():删除所有用户
- db.changeUserPassword():修改密码

#### 必需的角色
上面讲了mongodb的很多不同的角色，对应不同的权限，感觉很复杂，那我们在实际的开发过程中都需要设置哪些角色呢？
首先我们需要创建一个admin数据库
```
use admin
```
命令会自动创建一个admin数据库
然后我们在该数据库下创建一个用户并赋予userAdminAnyDatabase的权限， 这个用户可以用来管理所有的用户，创建，删除用户等功能，作为管理其他用户的管理员
如下所示
```
use admin
switched to db admin
db.createUser(
{
user: "useradmin",
pwd: "useradmin",
roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
}
)
```
这样我们就可以使用useradmin用户来创建和管理其他数据库中的所有用户了
现在我们想针对某一个数据库进行数据的增删改查了，首先我们要切换到对应的数据库中
```
use dbname
```
然后，我们输入
```
db.abc.insert({"a":1,"b":2})
```
插入失败，为什么呢？
因为我们没有权限，我们当前的用户权限只是针对用户管理的，并没有操作数据库增删改查的能力
所以要进行授权切换到该数据库的用户，这个用户是我们之前创建好的，具有读写权限，这样就可以成功插入了
那么，有没有一个用户不仅可以管理其他用户，而且也可以对集合进行任意操作？答案是肯定的，那就是role角色设置为root
```
show users
```
查看当前库下的用户
创建了这么多用户，如何查看所有的用户？我们切换到admin数据库并进行认证
```
db.system.users.find().pretty()
db.system.users.find().count()
```
这样我们基本上就实现了mongodb的权限管理了

---
title: linux-ip命令的使用
date: 2017-02-08 17:01:21
tags:
categories: Linux
---
### Linux 使用ip命令可进行网络管理任务

要给自己的机器设置ip地址，24表示子网掩码255.255.255.0
```angular2html
sudo ip addr add 192.168.0.111/24 dev eth0
```
显示eth0的ip地址
```angular2html
ip addr show eth0
```
删除IP地址,用del替代add
```angular2html
sudo ip addr del 192.168.0.111/24 dev eth0
```
列出路由表
```angular2html
ip route show 
```
显示路由过程
```angular2html
ip route get 10.42.0.47
```
更改默认路由
```angular2html
sudo ip route add default via 192.168.0.196
```
激活和停止网络接口
```angular2html
sudo ip link set eth0 down/up
```
监控netlink信息,监控局域网内所有设备的可达情况
```angular2html
ip monitor all
```
查看接入局域网内设备的MAC地址
```angular2html
ip neighbour
```
显示不同网络接口的统计数据
```angular2html
ip -s link
```
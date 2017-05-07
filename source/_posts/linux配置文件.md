---
title: linux配置文件
date: 2016-10-11 11:30:23
tags:
categories: Linux
---
关于Linux的配置文件，可以分为两种，一种是系统的，一种是用户的，博主以前也分不清这里面有什么区别，经过查资料理解后整理下来下面几条，应该算是挺全的，仅供参考

- /etc/profile:文件为系统的每一个用户设置环境信息，当用户第一次登录时，该文件被执行

- /etc/bashrc:为每一个运行bash shell的用户执行此文件，当bash shell被打开时，该文件被读取

- ~/.bash_profile:每个用户都可以使用该文件输入专用于自己使用的shell信息，当用户登录时执行一次，默认情况下会设置一些环境变量，执行用户的.bashrc文件,类似于/etc/profile，需要重启才会生效

- ~/.bashrc:包含专用于你的bash shell 的bash信息，每次打开新的shell时，该文件被读取,类似于/etc/bashrc

- ~/.bash_logout:当每次退出bash shell时，执行该文件

- ~/.bash_profile是login方式进入bash运行的，~/.bashrc是non-login方式进入bash运行的，通常前者会调用后者

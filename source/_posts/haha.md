---
title: Linux下多版本python共存
date: 2017-01-17 11:53:42
tags:
categories: python
---
最近由于实验室开发项目需要，两个项目需要不同的Python环境，所以研究了一下pyenv和virtualenv这两个工具的使用方法，下面简要介绍一下
### pyenv
pyenv是一个能简易地在多个Python版本中进行切换的工具，它简单而优雅
- 进行全局的Python版本切换
- 为单个项目提供对应的Python版本
- 只依赖Python本身
- 自带virtualenv 能够进行virtualenv管理
- 提供下载不同的Python版本和衍生版本

### 安装
```
$ git clone https://github.com/yyuu/pyenv.git ~/.pyenv     #使用 git 把 pyenv 下载到home目录
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc     #然后需要修改环境变量，使用 Bash Shell 的输入

$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc

$ echo 'eval "$(pyenv init -)"' >> ~/.bashrc     #最后添加 pyenv init

$ exec $SHELL -l     #输入命令重启 Shell,然后就可以重启pyenv
```
### 查看可安装的版本
```
$ pyenv install --list
```
### 安装指定版本的Python
```
$ pyenv install 3.4.1
```
这里利用pyenv命令安装的python版本都安装在~.pyenv/versions文件夹下，当然，如果你从图形界面进入Ubuntu的用户主目录下可能看不见.pyenv文件夹，这时候，你可以使用ls -a看到隐藏的文件夹
使用pip安装的包完成之后，可能需要对数据库进行更新:
```
pyenv rehash
```
### 卸载指定的Python版本
```
pyenv uninstall x.x.x
```
### 已安装Python版本查看
```
pyenv versions
```
### Python版本切换
- 全局版本切换 pyenv global 2.7.1
- 局部版本切换 pyenv local 3.4.1 #在某目录下执行，只在该目录下生效

### 创建独立虚拟环境
```
pyenv virtualenv 2.7.1 env271 # 该命令创建了名为env271的虚拟环境，使用了python2.7.1版本，这个环境的真实目录为～/.pyenv/versions/
```
### 激活
```
pyenv activate env271
```
进入虚拟环境env271在命令行的最左端会出现（env271）的标志，不过这个功能马上要被更改，需要打开 ～/.bashrc 增加下面这段内容
```
export PYENV_VIRTUALENV_DISABLE_PROMPT=1
```
这样下次输入的时候就不会有提示信息了
### 切换
```
pyenv deactivate env271
```
即可切换回原来的环境
### 删除环境
```
pyenv uninstall env271
```
或者
```
rm -rf ~/.pyenv/versions/env271/
```
### 总结
以上就是使用多版本的Python的方法，建议直接安装pyenv 工具，因为首先自带了virtualenv，同时这样使用virtualenv也更加方便

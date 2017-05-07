---
title: Tensorflow安装教程Ubuntu14.04LTS

date: 2016-11-28 21:24:54
tags:
categories: 机器学习
---
### Cuda installation

由于Tensorflow依赖GPU的运算，所以要提前安装好CUDA环镜

[安装CUDA](https://developer.nvidia.com/cuda-downloads)

打开链接，选择相应的版本，博主的环境是64位ubuntu环境，我们使用的CUDA版本是8.0，因为最新的tensorflow对CUDA的版本也是有很高的要求的

点击下载后等待就好1.9G的文件下载还是蛮快的，下载成功后进入文件的目录后，执行下面的三条命令，就可以成功安装了CUDA
安装后的文件存存储在/usr/local/cuda目录下
选择cuda版本
![选择cuda版本](http://oelvsay9f.bkt.clouddn.com/installcuda.png)
安装成功后还需要安装cuDNN这个liabrary，主要作用是对CUDA进行加速，当使用CUDA进行CNN等算法的计算时，会消耗大量的计算时间，cuDNN的作用就体现出来了
[安装cuDNN](https://developer.nvidia.com/cudnn)

下载cuDNN v5.1 Library for Linux
完成后进入文件所在目录，执行以下操作
```
$ tar xvzf cudnn-8.0-linux-x64-v5.1-ga.tgz
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

把相关的文件复制到CUDA的目录中去
然后链接cuDNN的库文件 ，这步很重要，博主第一次配环境的时候就是忘记了这步，因为官网上也没有给出，遇到了个小小的坑
```
$ sudo ln -sf /usr/local/lib/libcudnn.so.5.1.5 /usr/local/lib/libcudnn.so.5
$ sudo ln -sf /usr/local/lib/libcudnn.so.5 /usr/local/lib/libcudnn.so
$ sudo ldconfig -v
```

### Install TensorFlow

有很多种方法可以安装TensorFlow，博主选择使用pip工具，这样简单又方便，官网给出了很多种方法，可以参考一下
[安装TensorFlow](https://www.tensorflow.org/versions/r0.11/get_started/os_setup#download-and-setup)
首先安装pip,已经安装过的小伙伴可以跳过了
```
sudo apt-get install python-pip python-dev
```

选择需要的版本，博主根据自己的系统，输入了下面命令
```
$ export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.11.0-cp27-none-linux_x86_64.whl
$ sudo pip install --upgrade $TF_BINARY_URL
```

安装成功后，执行下面的步骤，打开用户配置文件～/.bash_profile ,关于几种配置文件的区别，博主刚刚好在以前的文章中也写到过
增加下面的设置
```
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
```
### Test

检测TensorFlow是否安装成功，在命令行输入Python，引入TensorFlow，出现下面的结果说明安装成功，我们学习深度学习的第一步已经成功迈出了，哈哈
![tensorflow安装成功](http://oelvsay9f.bkt.clouddn.com/successten.png)

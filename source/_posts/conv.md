---
title: Keras 卷积层和池化层
date: 2017-05-08 11:17:11
tags:
categories: 机器学习
---
Keras 中的卷积层是设置深度神经网络很重要的层结构，下面记录下博主对卷积层的学习笔记和理解

### 一维卷积层（时域卷积）
keras.layers.convolutional.Conv1D(filters, kernel_size, strides=1, padding='valid', dilation_rate=1, activation=None, use_bias=True, kernel_initializer='glorot_uniform', bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None, kernel_constraint=None, bias_constraint=None)

主要参数如下
```
filter：卷积核的数目
kernel_size: 整数或由单个证书构成的list／tuple，卷积核的空域或时域窗长度
strides： 整数或由单个证书构成的list／tuple，为卷积的步长
padding： 补0策略，为‘valid’， ‘same’， ‘causual’ valid将产生因果卷积，
即output[t]不依赖于input[t+1:] valid 代表只产生有效的卷积，即对边界数据不处理 
same代表保留边界处的卷积结果，通常会导致输出shape与输入shape相同
```
名词
```
输入shape：形如（sample， steps， input_dim）的3D张量
输出shape：形如（sample， new_steps, nb_filter）的3D张量
```

### 二维卷积层（对图像的空域卷积）
keras.layers.convolutional.Conv2D(filters, kernel_size, strides=(1, 1), padding='valid', data_format=None, dilation_rate=(1, 1), activation=None, use_bias=True, kernel_initializer='glorot_uniform', bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None, kernel_constraint=None, bias_constraint=None)

该层对二维输入进行滑动窗卷积，当使用该层为第一层时，需要提供input_shape参数。例如input_shape = （128，128，3）代表128*128的彩色RGB图像（data_format='channels_last'）

名词
```
channels_first: 输入形如（samples， channels， rows， cols）的4D张量

channels_last: 输入形如（samples, rows, cols, channels）的4D张量

这里的shape是指函数内部实现的输入shape，而不是函数接口应定义的input_shape

输出shape：

channels_first:形如（samples, nb_filter, new_rows, new_cols）的4D张量

channel_last: 形如（samples, new_rows, new_cols, nb_filter）的4D张量

输出的行列数可能因填充方法而改变
```

### MaxPooling1D层
对时域1D信号进行最大值池化
keras.layers.pooling.MaxPooling1D(pool_size=2, strides=None, padding='valid')
参数
```
pool_size:整数，池化窗口大小

strides：整数或None，下采样因子，例如设2将会使得输出shape为输入的一半，若为None则默认值为pool_size。

padding：‘valid’或者‘same’
```
```
输入shape

形如（samples，steps，features）的3D张量

输出shape

形如（samples，downsampled_steps，features）的3D张量
```
### MaxPooling2D层

keras.layers.pooling.MaxPooling2D(pool_size=(2, 2), strides=None, padding='valid', data_format=None)
为空域信号施加最大值池化

参数

pool_size：整数或长为2的整数tuple，代表在两个方向（竖直，水平）上的下采样因子，如取（2，2）将使图片在两个维度上均变为原长的一半。为整数意为各个维度值相同且为该数字。
strides：整数或长为2的整数tuple，或者None，步长值。

border_mode：‘valid’或者‘same’

data_format：字符串，“channels_first”或“channels_last”之一，代表图像的通道维的位置。该参数是Keras 1.x中的image_dim_ordering，“channels_last”对应原本的“tf”，“channels_first”对应原本的“th”。以128x128的RGB图像为例，“channels_first”应将数据组织为（3,128,128），而“channels_last”应将数据组织为（128,128,3）。该参数的默认值是~/.keras/keras.json中设置的值，若从未设置过，则为“channels_last”。

输入shape

‘channels_first’模式下，为形如（samples，channels, rows，cols）的4D张量

‘channels_last’模式下，为形如（samples，rows, cols，channels）的4D张量

输出shape

‘channels_first’模式下，为形如（samples，channels, pooled_rows, pooled_cols）的4D张量

‘channels_last’模式下，为形如（samples，pooled_rows, pooled_cols，channels）的4D张量






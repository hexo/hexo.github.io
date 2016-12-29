---
title: "浅谈单元测试"
date: 2016-08-14 18:19:02
tags: "单元测试"
categories: "前端"
---
# 单元测试总结

### 单元测试简介
单元测试（又称为模块测试，Unit Testing）是针对程序模块来进行正确性验证的测试工作。程序单元是应用的最小可测试部件。在面向过程编程中，一个单元就是单个程序，函数，过程等；对于面向对象编程，一个单元就是方法，包括基类，抽象类，派生类中的方法  （from 维基百科）

### 为什么要进行单元测试？
javascript是面向对象编程的，很多时候我们都需要将一个功能抽象成一个组件，方便团队其他开发者调用，那么我们就要保证我们给出的组件是正确可用的。对于前端这种GUI编程来说，单元测试确实很麻烦，很多前端开发者都会忽略了单元测试。但是随着Node的发展迅猛，出现了越来越多的javascript单元测试框架，现在很多优秀的开源项目都会有单元测试

### 重要性
如果我写好了一个接口，没有经过测试就直接交给了别人，那么出错之后就要接着调试。作为node端开发者，经常会写一些接口，举个简单的例子。我在开发一个get /user的API给前端调用，我写完了之后自己就要开一个前端页面，刷浏览器进行调试，同时还要辅助以一系列的console.log()，而且当需求有一些改动之后我还要重复进行调试，这是很耗时间的。而对于单元测试框架来说我只需要在命令行输入mocha(以mocha框架为例)，就可以完成单元测试。所以说单元测试的意义在于提高开发效率

### 单元测试的分类
单元测试根据主流的分类可以分成两类，BDD（行为驱动开发），TDD（测试驱动开发），我们主要采用BDD，这种更加符合编程习惯

- TDD: 开发人员先写了一些测试代码，然后实现测试用例里面提到的方法。最后把所有的测试用例都通过， 关注所有功能是否被正确实现
- BDD: BDD的测试用例更像是一份说明书，这两者的区别主要也是在于语言的风格不同，关注的是系统的实现是否与期望一致

### mocha框架
> Mocha 是TJ Holowaychunk开发的基于node.js和浏览器的集合各种特性的javascript测试框架，而且可以让异步测试也变得很简单，当捕获到错误时，会给出灵活且准确的报告

#### 全局安装

```
 npm install -g mocha
```

#### 如何向mocha传参数
当我们运行test目录下的测试文件时，我们只需要在项目的根目录下执行mocha，不过mocha还可以接受一些参数
很重要的有一下有几个

- 默认情况下mocha只会执行一个测试用例2s，若超时则会停止执行并报错，这个时间对于一些接口测试来说是不够的,通过 -t 20000 即可设置为允许单个测试用例最长执行20s

- 默认情况下mocha只会执行test目录下的js文件，如果有二级目录是不会执行的，所以要通过 --recursive 就会执行test目录下的全部js文件

- mocha和istanbul配合使用时，要先由istanbul开一个进程，然后在该进程下执行mocha，要在mocha前加 _

### 辅助工具

#### assert

是node.js自带模块，直接通过require（'assert'）引入
是一个断言库，可完成简单的测试需求
了解更多请参考node.js官方文档：https://nodejs.org/api/assert.html

#### should.js

```
npm install should
```

> should.js是由TJ开发的断言库，提供了表达性，可读性都很强的断言风格
全部断言请参考：https://github.com/tj/should.js

#### supertest

```
npm install supertest
```

> supertest是一个http断言库，使用它可以测试http服务，如使用express，koa搭建的服务等，全部语法规则请参考：https://github.com/visionmedia/supertest
（请注意官方给出的示例是在express环境下的，koa环境下同样支持，语法相似）

#### 全局安装 istanbul

```
npm install -g istanbul
```

测试的时候，我们常常关心，是否所有的代码都测试到了，这个指标就叫做代码覆盖率

Istanbul是javascript程序的代码覆盖率检测工具
它有四个测量维度

- 行覆盖率（line coverage）：是否每一行都执行了？
- 函数覆盖率（function coverage）：是否每一个函数都调用了？
- 分支覆盖率（branch coverage）：是否每一个if分支都被执行了？
- 语句覆盖率（statement coverage）： 是否每条语句都被执行了？

##### 与测试框架mocha结合

istanbul cover _mocha :首先由istanbul开一个进程，在这个进程下执行mocha测试，instanbul就会捕获到覆盖率数据，如果想向mocha 传参数，通过下面这样

```
istanbul cover _mocha -- test/test.js
```
关于istanbul的更多信息可以参考ruanyifeng的入门教程：http://www.ruanyifeng.com/blog/2015/06/istanbul.html

### co-mocha

co-mocha是mocha经过co库包装后支持生成器函数的一个库，使用方法和mocha类似

### co-supertest
co-supertest是supertest经过co库包装后支持生成器函数的一个库，使用方法也和supertest类似

### ES6测试
如果测试脚本是用ES6写的，那么运行测试之前，需要先用babel转码
```
npm install babel-core babel-preset-es2015 --save-dev
```
然后在项目目录下，新建一个.babelrc配置文件
```
{
  "presets":["es2015"]
}
```
最后，使用--compilers 参数后面紧跟一个用冒号分隔的字符串，冒号左边是文件名，冒号右边是用来处理这一类文件的模块名
```
./node_modules/mocha/bin/mocha --compilers js:babel-core/register
```
###关于前端测试
本教程根目录下的testfront文件夹下为简单的通过mocha进行前端测试的例子，chai.js为前端测试使用的断言库，类似should.js，这个是不唯一的，根据喜好可自行选择，mocha.js是我们测试依赖的框架，mocha.css是浏览器使用的css，这些都不用管，我们把需要被测试的代码放在index.html中，把测试代码放在tests.js中，并在index.html中引入，这样在浏览器打开index.html时，我们就可以看到测试的结果了

> 在根目录下输入 mocha init front 会有惊喜发生

本教程根目录下的phantomjs为前端测试工具phantomjs的使用方法，phantomjs说白了就是一个浏览器内核，但是没有浏览器界面，我们在命令行可以进行浏览器端的测试

####全局安装
```
npm install phantomjs -g
```
进入phantomjs目录下，输入phantomjs，进入浏览器环境
然后输入phantomjs XXX.js,即可执行

istanbul会生成coverage文件夹，是测试报告，浏览器打开lcov-report文件夹下的index.html

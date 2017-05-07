---
title: Git相关知识学习
date: 2017-01-17 16:19:52
tags:
categories: Linux
---
### 前言
接触Git也有一年的时间了，虽然平时使用的也就用到几个简单命令，但是还有很多的小技巧可以提高开发的效率，出现问题后也是重要的，这里做一个连载的总结

在使用hexo写博客的时候，用到了Next主题，由于是从github clone下来的，所以当我准备把项目上传到自己的github的时候next目录由于两个git冲突，是灰色的
那么就需要在本地目录下首先删掉next目录里的.git文件，然后执行下面的命令把cache冲缓存的内容删除
```angular2html
    git rm -r --cached some-directory
```
这样输入git status 的时候你就会发现，出现了next相关的文件，然后就很简单了
```angular2html
git add .
git commit -m 'addnext'
git push origin dev
```
这样就完成了文件的上传
---
title: 解决pycharm无法调用pip安装的包
tags:
  - Python
  - django
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-02-26 15:27:11
---
### 环境
django 2.1.7
python 3.7
[xadmin](https://github.com/sshwsfc/xadmin/tree/django2)
pycharm 2018.3.4
### 问题
在调试xadmin时，已经执行pip3 install future,安装future成功，执行pip3 list也能看到future，可是在pycharm上调试运行时，老是提示：
ModuleNotFoundError: No module named 'future'。崩溃！！！
后来发现时charm不能调用pip安装的包。
N
### 解决办法
经网络查找办法：

- 原因：pycharm没有设置解析器

- 解决方法:

打开pycharm->File->Settings->Project Interpreter->设置为你的python路径，我的是:C:\Python27\python.exe,你们根据各自python安装路径修改一下即可

可在我电脑上还是不好使，可能时我环境里同时安装了python2.7、python3.7，不管了，先按照https://blog.csdn.net/sinat_23619409/article/details/79962518，在pycharm添加包。
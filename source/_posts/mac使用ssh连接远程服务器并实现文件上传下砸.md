---
title: mac使用ssh连接远程服务器并实现文件上传下砸
tags:
  - linux
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-08-20 18:54:41
---

### 使用ssh连接远程主机

    ssh username@192.168.100.100
    其中，username是登录用户名，@后接ip地址，点击确定之后输入密码即连接到远程主机。要查看当前有多少个处于登录状态的用户，可以使用who命令查看。

### 使用scp命令实现上传下载

1.从服务器上下载文件 scp username@servername:/path/filename /Users/mac/Desktop（本地目录）
例如:
    
    scp root@123.207.170.40:/root/test.txt /Users/mac/Desktop就是将服务器上的/root/test.txt下载到本地的/Users/mac/Desktop目录下。注意两个地址之间有空格！

2.上传本地文件到服务器 scp /path/filename username@servername:/path ;
例如
    
    scp /Users/mac/Desktop/test.txt root@123.207.170.40:/root/

3.从服务器下载整个目录 scp -r username@servername:/root/（远程目录） /Users/mac/Desktop（本地目录）
例如:

    scp -r root@192.168.0.101:/root/ /Users/mac/Desktop/

4.上传目录到服务器 scp -r local_dir username@servername:remote_dir
例如：

    scp -r test root@192.168.0.101:/root/ 把当前目录下的test目录上传到服务器的/root/ 目录

注：目标服务器要开启写入权限。

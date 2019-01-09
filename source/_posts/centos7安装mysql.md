---
title: centos7安装mysql
tags:
  - centos
  - linux
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-08-10 18:02:20
---
## 环境
Linux版本：5.7

## 安装
[下载地址](https://pan.baidu.com/s/14YeOQnQs7HKVHf16ngnSwA)
yum install -y mysql*
注：一开始一直报‘Failed to start mysqld.service: Unit not found.’
网上查找方法，好一通折腾，原来没安装mysql-community-server。

### 启动
执行

    service mysqld start
通过如下命令找到mysql root初始密码

    grep 'temporary password' /var/log/mysqld.log

- 登录mysql，修改密码
执行

      mysql -uroot -p

然后输入密码登录时，一直如下错误：

      ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

一般这个错误是由密码错误引起，解决的办法自然就是重置密码
解决方案如下：
1. 停止mysql数据库：service mysqld stop或systemctl stop mysqld

2. 用以下命令启动MySQL，以不检查权限的方式启动：

mysqld --skip-grant-tables &

此时又报了一个错误：2018-02-01T02:52:55.093724Z 0 [ERROR] Fatal error: Please read "Security" section of the manual to find out how to run mysqld as root!

执行命令：mysqld --user=root --skip-grant-tables &

3. 登录mysql：service mysqld start 
   mysql -uroot或mysql

4. 更新root密码

mysql5.7以下版本：UPDATE mysql.user SET Password=PASSWORD('123456') where USER='root';

mysql5.7版本：UPDATE mysql.user SET authentication_string=PASSWORD('123456') where USER='root';

5.刷新权限：flush privileges;

6.退出mysql：exit或quit

7.使用root用户重新登录mysql

mysql -uroot -p

Enter password:<输入新设的密码123456>

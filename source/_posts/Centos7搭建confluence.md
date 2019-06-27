---
title: Centos7搭建confluence
tags:
  - confluence
  - centos
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-02-13 10:19:43
---

### CentOS 7下yum重装 MySQL 5.7
先卸载干净：https://ywnz.com/linuxysjk/2962.html
转载自：https://www.jianshu.com/p/8d73382d7d76

注：centos上有可能存在mariadb
### linux安装破解Confluence-6.8.5

转载：http://blog.51cto.com/moerjinrong/2149177
https://www.jianshu.com/p/fb2574567eae

### 搭建jira

##### 此 MySQL 实例没有适当配置。请遵照 文档进行 MySQL 5.7+ 设置。
 需要先关闭mysql，然后添加

 > default-storage-engine=INNODB
innodb_default_row_format=DYNAMIC
innodb_large_prefix=ON
innodb_file_format=Barracuda
innodb_log_file_size=2G

### Confluence、Jira、Bitbucket统一账户管理
https://blog.csdn.net/niuniu0186/article/details/79412088


### 反向代理

https://blog.csdn.net/xu_Melon/article/details/78815981

每次修改**.conf文件都要进行刷新，刷新命令：
> cd /usr/local/openresty/nginx/sbin/
> ./nginx -s reload
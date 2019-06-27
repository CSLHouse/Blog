---
title: centos7.4基于bind搭建DNS服务器
tags:
  - centos
  - bind
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-02-15 15:15:20
---

### 查看bind版本
dig @localhost version.bind txt chaos

### 区域 (zone) 数据库文件
 存在于/var/named/下
 资源记录：    Resource Record, 简称rr；
 记录类型：    A， AAAA， PTR， SOA， NS， CNAME， MX

        SOA    ：Start Of Authority，起始授权记录；一个区域解析库有且只能有一个 SOA 记录，而且必须放在第一条；这个域由哪部 DNS 作为 Primary DNS;
        NS    ：Name Service，域名服务记录；一个区域解析库可以有多个 NS 记录；其中一个为主的；    
        A    ：Address, 地址记录，FQDN --> IPv4；   
        AAAA：地址记录， FQDN --> IPv6；
        CNAME：Canonical Name，别名记录；
        PTR    ：Pointer，IP --> FQDN
        MX    ：Mail eXchanger，邮件交换器
        优先级：0-99，数字越小优先级越高；
### PTR 记录
反向解析记录，用于将一个IP地址映射到对应的域名，也可以看成是 A 记录的反向，IP 地址的反向解析。
 name    ：IP地址，有特定格式，IP反过来写，而且加特定后缀；例如 1.2.3.4 的记录应该写为 4.3.2.1.in-addr.arpa.；
        value    ：FQND
        
        范例：
            4.3.2.1.in-addr.arpa.  IN  PTR  www.baidu.com.

### 实践部署
部署环境：

    操作系统         ：CentOS 7.4
    DNS 服务器 IP    : 192.168.1.9
    内网域名        ：intra.fengbaohuyu.com

#####  主配置文件格式
    全局配置段：
        options { ... }
    日志配置段：
        logging { ... }
    区域配置段：
        zone { ... }
        那些由本机负责解析的区域，或转发的区域；
        
    注意：每个配置语句必须以分号结尾；

##### 配置解析正向区域




### 命令
- 重载配置文件:
  > rndc reload
- 检查语法错误
> named-checkzone  ZONE_NAME   ZONE_FILE
> named-checkconf

- 启动
  > service named start
- 测试
  > nslookup admin.intra.fengbaohuyu.com 192.168.1.9
- 设置开机启动
  > chkconfig named on

参考：https://blog.csdn.net/devalone/article/details/80580094
    https://www.cnblogs.com/kevingrace/p/9359989.html
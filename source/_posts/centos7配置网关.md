---
title: centos7配置网关
tags:
  - centos
  - linux
  - 443
  - 后端
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-08-02 17:37:29
---
### 起源
搭建虚拟机并配置centos7之后，在主机终端采用ssh root@192.168.131.141方式连接虚拟机，
当访问公司内网，执行curl http://###下载资源，下载失败，错误显示'### port 443: "拒绝连接"',

### 配置网关
一开始以为没配置网关，所以网上搜索了网关的配置方法，记录一下：

1、CentOS 修改DNS 

修改对应网卡的DNS的配置文件 

    # vi /etc/resolv.conf  
修改以下内容 

    nameserver 8.8.8.8 #google域名服务器 
    nameserver 8.8.4.4 #google域名服务器 
2、CentOS 修改网关  
修改对应网卡的网关的配置文件 

    [root@centos]# vi /etc/sysconfig/network 

修改以下内容 
NETWORKING=yes(表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络，而且很多系统服务程序将无法启动) 
HOSTNAME=centos(设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应) 
GATEWAY=192.168.1.1(设置本机连接的网关的IP地址。例如，网关为10.0.0.2) 
3、CentOS 修改IP地址 

修改对应网卡的IP地址的配置文件
注：查看/etc/sysconfig/network-scripts下，是否已经有网卡位置文件，一个和使用ip addr命令查看ip第二条开头的名称一致的文件（我的是ens33）

    # vi /etc/sysconfig/network-scripts/ifcfg-eth0 

修改以下内容 

    TYPE=Ethernet
    PROXY_METHOD=none
    BROWSER_ONLY=no
    BOOTPROTO=static
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_FAILURE_FATAL=no
    IPV6_ADDR_GEN_MODE=stable-privacy
    NAME=ens33
    UUID=bfef954d-eca2-4096-947f-8c6faf5e44b0
    DEVICE=ens33
    ONBOOT=yes
    IPADDR=192.168.131.141
    GATEWAY=192.168.131.2
    NETMAK=255.255.255.0
    NM_COTROLLED=no
    DNS1=8.8.8.8 
4、重新启动网络配置 

    # service network restart  
或 

    # /etc/init.d/network restart 

### 解决问题
请教高人才发现时时DNS写错了，与公司的dns不一致，
执行vi /etc/resolv.conf ，添加nameserver 192.168.1.254之后成功


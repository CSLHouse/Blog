---
title: ssh隧道搭建透明网关
tags:
  - 标签1
  - 标签2
categories:
  - 前端
comments: false    // 是否开启评论
date: 2019-06-21 18:11:22
---
## 环境说明
192.168.1.246 局域网内主机做透明网关服务器 安装centos7.4
13.115.71.* 墙外服务器 linux系统

## 墙外服务器设置
1. 打开ipv4转发
  > /etc/sysctl.conf net.ipv4.ip_forward=1
  > sysctl -p
  > cat /proc/sys/net/ipv4/ip_forward 验证
2. 设置iptables源地址转发
   > iptables -t nat -A POSTROUTING -s 192.168.200.0/24 -o eth0 -j MASQUERADE
3. 打开ssh隧道功能
   > /etc/ssh/sshd_config PermitTunnel yes
重启sshd服务 service sshd restart
4. 执行脚本ssh_tun_server.sh
  
   ```
    #!/bin/sh
    iptables -t nat -A POSTROUTING -s 192.168.200.0/24 -o eth0 -j MASQUERADE

    ip tuntap add mode tun
    ip link set tun0 up
    ip addr add 192.168.200.1/32 peer 192.168.200.2 dev tun0
    arp -sD 192.168.200.2 eth0 pub
    iptables -t nat -A POSTROUTING -s 192.168.200.2 -o eth0 -j MASQUERADE
   ```
  5. 执行文件时出现-bash: ./test.sh: /bin/sh^M: bad interpreter: No such file or directory  
      - 首先用vi命令打开文件
      - 在vi命令模式中使用 :set ff 命令
      - 修改文件format为unix   :set ff=unix
      - 然后可执行

## 本地网关服务器设置
1. 私钥连接vps云服务器
   以腾讯云为例：
   > chmod 400 [key]
   > ssh -i [key] root@vps_ip
2. 安装dnsmasq
   - 安装命令：
    > yum -y install dnsmasq
   - dnsmasq.conf配置
    ```
      resolv-file=/etc/resolv.dnsmasq.conf
      strict-order
      domain-needed
      listen-address=127.0.0.1,192.168.1.246
      conf-dir=/etc/dnsmasq.d,.rpmnew,.rpmsave,.rpmorig
    ```
   - resolv.dnsmasq.conf配置
    ```
      nameserver 119.29.29.29
      nameserver 114.114.114.114
    ```
   - /etc/resolv.conf配置
    ```
      nameserver 127.0.0.1
    ```
   - 命令：
    > systemctl enable dnsmasq
    > systemctl start dnsmasq
    > systemctl restart dnsmasq
2. 安装iptables
    - 命令：
      ```
        #先检查是否安装了iptables
        service iptables status
        #安装iptables
        yum install -y iptables
        #升级iptables
        yum update iptables 
        #安装iptables-services
        yum install iptables-services
        #停止firewalld服务
        service iptables stop
        #相当于以前的chkconfig iptables on
        systemctl enable iptables.service
        #开启服务
        service iptables start
        #保存规则
        service iptables save
        iptables-save > [path]
        #读取规则
        iptables-restore < [path]
      ```
3. 打开ipv4转发
  > /etc/sysctl.conf net.ipv4.ip_forward=1
  > sysctl -p
  > cat /proc/sys/net/ipv4/ip_forward 验证
4. 打开ssh隧道功能
   > /etc/ssh/sshd_config PermitTunnel yes
    重启sshd服务 service sshd restart
5. 执行脚本
   ```shell
    ip tuntap add tun0 mode tun
    ip link set tun0 up
    ip addr add 192.168.200.2/32 peer 192.168.200.1 dev tun0

    ssh -NTCf -w 0:0 -i [vps密钥路径] root@[vps_ip] 
    ip route add [vps_ip] via 192.168.1.1  #vps以太网口直接出去
    ip route add default via 192.168.200.1

    iptables -F
    iptables -t nat -F
    iptables -t nat -A INPUT -p udp -m udp --dport 53 -j ACCEPT
    iptables -t nat -A OUTPUT -p udp -m udp --sport 53 -j ACCEPT
    iptables -t nat -A POSTROUTING -d [vps_ip] -o ens33 -j MASQUERADE
    iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o tun0 -j MASQUERADE
   ```
6. 国内外数据分流
   - 优化dns数据分流
     创建/etc/dnsmasq.d/china.dns文件，内容获取方式：wget [https://raw.githubusercontent.com](https://raw.githubusercontent.com/qiuyuanfeng/dnsmasq-china-list/master/accelerated-domains.china.conf)
  
   
7. 





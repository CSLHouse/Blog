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
##### 环境说明
192.168.1.9 局域网内主机做透明网关服务器 安装centos7.4
13.115.71.* 墙外服务器 linux系统

##### 墙外服务器设置
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

##### 网关服务器设置
1. 私钥连接墙外服务器
  - 在服务器端ssh-keygen -t rsa -b 2048
  - 在.ssh目录下 cat id_rsa.pub >> authorized_keys
  - 在dns服务器端 上传私钥id_rsa   scp root@118.24.77.34:~/.ssh/id_rsa ./ 
  - chmod 400 id_rsa
  - ssh -i id_rsa root@118.24.77.34
一般vps都可以自己创建私钥，直接下载传到网关服务器就好
2. 连接墙外服务器
    > channel 0: open failed: administratively prohibited: open failed
   
   执行ssh -NTCf -w 0:0 -i storm root@118.24.77.34时出现以上错误。
   解决办法：两端开启隧道功能PermitTunnel yes
3. Permission denied (publickey,gssapi-keyex,gssapi-with-mic)
   vi /etc/ssh/sshd_config
    PermitRootLogin yes->no
    UsePAM yes->no
    PasswordAuthentication yes->no
4. 安装dnsmasq 
   > yum install dnsmasq -y

  vim /etc/dnsmasq.conf
5. 打开iptables
   ```
    #先检查是否安装了iptables
    service iptables status
    #安装iptables
    yum install -y iptables
    #升级iptables
    yum update iptables 
    #安装iptables-services
    yum install iptables-services

    #查看iptables现有规则
    iptables -L -n
    #先允许所有,不然有可能会杯具
    iptables -P INPUT ACCEPT
    #清空所有默认规则
    iptables -F
    #清空所有自定义规则
    iptables -X

    #注册iptables服务
    #相当于以前的chkconfig iptables on
    systemctl enable iptables.service
    #开启服务
    systemctl start iptables.service
    #查看状态
    systemctl status iptables.service

   ```
    打开53端口
    iptables -t nat -A INPUT -p udp -m udp --dport 53 -j ACCEPT
    iptables -t nat -A OUTPUT -p udp -m udp --sport 53 -j ACCEPT
    
6. 删除默认网关
   > route del default
7. 执行脚本clien.sh
   ```
    #!/bin/sh
    ip tuntap add tun0 mode tun
    ip link set tun0 up
    ip addr add 192.168.200.2/32 peer 192.168.200.1 dev tun0

    ssh -NTCf -w 0:0 -i storm root@118.24.77.34
    ip route add 118.24.77.34 via 192.168.1.1  
    ip route add default via 192.168.200.1

    iptables -t nat -F
    iptables -t nat -A INPUT -p udp -m udp --dport 53 -j ACCEPT
    iptables -t nat -A OUTPUT -p udp -m udp --sport 53 -j ACCEPT
    iptables -t nat -A POSTROUTING -d 118.24.77.34 -o enp1s0 -j MASQUERADE
    iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o tun0 -j MASQUERADE

   ```
8.   





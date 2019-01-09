---
title: CentOS下编译安装shadowsocks-libev
tags:
  - centos
  - shadowsocks-libev
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-11-06 17:25:45
---
####  安装

- 编译环境准备&安装依赖包

> yum install -y gcc automake autoconf libtool make build-essential autoconf libtool  asciidoc xmlto libsodium libev-devel  udns-devel
  yum install -y curl curl-devel unzip zlib-devel openssl-devel perl perl-devel cpio expat-devel gettext-devel
- 下载源码

> git clone https://github.com/shadowsocks/shadowsocks-libev.git

- 开始编译

> cd shadowsocks-libev
  ./autogen.sh
  ./configure --prefix=/usr && make
  make install
  
- 准备必须的文件

> mkdir -p /etc/shadowsocks-libev
  cp ./debian/shadowsocks-libev.init /etc/init.d/shadowsocks-libev
  cp ./debian/shadowsocks-libev.default /etc/default/shadowsocks-libev
  cp ./debian/shadowsocks-libev.service /lib/systemd/system/
  cp ./debian/config.json /etc/shadowsocks-libev/config.json
  chmod +x /etc/init.d/shadowsocks-libev
  
- 编辑配置文件
    
> vim /etc/shadowsocks-libev/config.json

- 启动服务

> service shadowsocks-libev start

#### FAQ

- 如果执行./autogen.sh时出现下列错误：

> configure.ac:309: error: required file 'libcork/Makefile.in' not found
  configure.ac:309: error: required file 'libipset/Makefile.in' not found

请初始化git库

> git submodule init && git submodule update

- 如果执行./configure –prefix=/usr出现下列错误

> configure: error: mbed TLS libraries not found.

请安装TLS开发包

> yum install -y mbedtls-devel

若提示：没有可用软件包 mbedtls-devel。
请执行以下命令再重新安装：

> yum install epel-release
  yum update
  yum install mbedtls-devel


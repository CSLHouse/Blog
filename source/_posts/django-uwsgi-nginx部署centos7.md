---
title: django+uwsgi+nginx部署centos7
tags:
  - Python
  - nginx
  - uwsgi
categories:
  - 前端
comments: false    // 是否开启评论
date: 2019-03-27 17:02:22
---

### centos7搭建django环境

#### 安装python3.7
1. 安装编译 Python3的相关包
   > yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel

这里面有一个包很关键libffi-devel，因为只有3.7才会用到这个包，如果不安装这个包的话，在 make 阶段会出现如下的报错：
> ModuleNotFoundError: No module named '_ctypes'

2. 下载 python3.7的源码包
> wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz

> #解压缩
> tar -zxvf Python-3.7.0.tgz
> #进入解压后的目录，依次执行下面命令进行手动编译
> 添加配置,第一个参数指定安装目录,第二个加上后，安装ssl，不然以后pip3装东西会出错
> ./configure prefix=/usr/local/python3 --with-ssl
> make && make install
如果最后没提示出错，就代表正确安装了，在/usr/local/目录下就会有python3目录
3. 添加软链接
   > #添加python3的软链接 
   > ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3.7 
   > #添加 pip3 的软链接 
   > ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3.7
   > #测试是否安装成功了 
   > python -V
4. 更改yum配置，因为其要用到python2才能执行，否则会导致yum不能正常使用（不管安装 python3的那个版本，都必须要做的）
   > vi /usr/bin/yum 
   > 把 #! /usr/bin/python 修改为 #! /usr/bin/python2 
   > vi /usr/libexec/urlgrabber-ext-down 
   > 把 #! /usr/bin/python 修改为 #! /usr/bin/python2

#### 创建python3虚拟环境
> python3 -m venv [环境名]
进入虚拟环境 
> cd **/bin
> source activate

#### 把项目上传到服务器
> scp -r storm_oa_system root@192.168.1.10:/home/projects

#### 安装依赖
####### 如何自动生成和安装requirements.txt依赖
1. 生成requirements.txt文件
   > pip freeze > requirements.txt
2. 安装requirements.txt依赖
   > pip install -r requirements.txt

####安装和配置uwsgi
1. 安装

> pip3 install uwsgi
2. 配置
   把参数写到配置文件里
   ```
   [uwsgi]
    socket = 127.0.0.1:8002
    #http = :8002
    home=/home/projects/virtual/env_oa
    # the base directory (full path)
    chdir           = /home/projects/storm_oa/storm_oa_system
    # Django s wsgi file
    module          = storm_oa_system.wsgi
    # process-related settings
    # master
    master          = true
    # clear environment on exit
    vacuum          = true
    # maximum number of worker processes
    processes       = 4
    harakiri        = 1200
    uwsgi_read_timeout      = 600
    limit-as        = 600
    daemonize       = /home/projects/storm_oa/conf/uwsgi_logs/access.log
   ```
  注意：与nginx通信时，必须选用socket = 127.0.0.1:8002
3. 运行uwsgi
   > uwsgi /home/projects/storm_oa/conf/uwsgi.ini -d /home/projects/storm_oa/conf/uwsgi.log
4. 关闭uwsgi
   > pkill -9 uwsgi

####安装和配置nginx
1. 安装
> wget http://nginx.org/download/nginx-1.15.8.tar.gz
> tar -zxvf nginx-1.15.8.tar.gz
> cd nginx-1.15.8
>  ./configure --user=nobody --group=nobody --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_gzip_static_module --with-http_realip_module --with-http_sub_module --with-http_ssl_module
> make && make install
(注: --with-http_ssl_module:这个不加后面在nginx.conf配置ssl:on后,启动会报nginx: [emerg] unknown directive "ssl" in /opt/nginx/conf/nginx.conf 异常)
2. 配置
  ```
  events{}

  http{
        include      /usr/local/nginx/conf/mime.types;
        default_type  application/octet-stream;
        log_format  main  '[$remote_addr] - [$remote_user] [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
        access_log /home/projects/logs/nginx_run.log main;
        sendfile        on;

        upstream django {
                # server unix:///path/to/your/mysite/mysite.sock; # for a file socket
                server 127.0.0.1:8002; # for a web port socket (we'll use this first)
        }

        server{
                charset utf-8;
                listen 80;
                server_name 192.168.1.10;
                client_max_body_size 75M;

                access_log /home/projects/logs/nginx_access.log;
                error_log /home/projects/logs/nginx_error.log;

                location /media/ {
                        alias /home/projects/storm_oa_system/media/;
                }
                location /static/ {
                        gzip_static on;
                        expires max;
                        add_header Cache-Control public;
                        alias /home/projects/storm_oa_system/static_cdn/;
                }

                # Finally, send all non-media requests to the Django server.
                location / {
                        uwsgi_pass  django;
                        include     /usr/local/nginx/conf/uwsgi_params; # the uwsgi_params file you installed
                        #uwsgi_pass 127.0.0.1:8002;
                        uwsgi_read_timeout 1500;
                        uwsgi_send_timeout 300;
                        client_body_timeout 1500;
                        proxy_read_timeout 300;
                        proxy_http_version 1.1;
                        proxy_set_header Connection "";
                }
        }
  }
  ```
3. 运行
   > nginx -c /home/projects/config/nginx.conf

#### uwsgi和nginx的开机自启动
1. nginx的开机自启动
   - 在系统服务目录里创建nginx.service文件
    > vi /usr/lib/systemd/system/nginx.service
   - 写入内容如下：
      ```
      [Unit]
      Description=nginx
      After=network.target

      [Service]
      Type=forking
      ExecStart=/usr/bin/nginx -c /home/projects/config/nginx.conf
      ExecReload=/usr/bin/nginx -s reload
      ExecStop=/usr/bin/nginx -s quit
      PrivateTmp=true

      [Install]
      WantedBy=multi-user.target
      ```
    [Unit]:服务的说明
      Description:描述服务
      After:描述服务类别
      [Service]服务运行参数的设置
      Type=forking是后台运行的形式
      ExecStart为服务的具体运行命令
      ExecReload为重启命令
      ExecStop为停止命令
      PrivateTmp=True表示给服务分配独立的临时空间
      注意：[Service]的启动、重启、停止命令全部要求使用绝对路径
      [Install]运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3
    - 设置开机自启动
    > systemctl enable nginx.service
    - 查看nginx状态
    > systemctl status nginx.service
    - 杀死nginx重启nginx
    > pkill -9 nginx
    > ps aux | grep nginx
    > systemctl start nginx
2. uwsgi的开机自启动
   与nginx同理
   - 启动方式
    > systemctl enable uwsgi.service   开机自启
    > systemctl start uwsgi.service    启动
    > systemctl restart uwsgi.service  重新启动

#### uwsgi启动失败
django是在虚拟环境下构建的 所以自启动的时候要加上虚拟环境

> ExecStart=/home/projects/virtual/env_oa/bin/uwsgi /home/projects/config/uwsgi.ini -d /home/projects/logs/uwsgi_access.log --home=/home/projects/virtual/env_oa


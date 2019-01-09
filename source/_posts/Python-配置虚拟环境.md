---
title: Python--配置虚拟环境
tags:
  - Python
  - 前端
categories: Python
comments: false    // 是否开启评论
date: 2018-07-20 20:24:15
---

# 安装虚拟环境

1. 打开终端,利用pip/pip3 安装 (pip安装在python2,pip3安装在python3中)

        执行：sudo pip install virtualenv
        
2. 创建环境

   virtualenv 环境名
   
   例：
       
        1. mkdir ~/py_envs # 在用户目录下创建了一个统一管理虚拟环境的目录    
        2. cd ~/py_envs # 跳进这个目录
        3. virtualenv env_workspace1 # 创建一个虚拟工作空间
        
   创建指定python版本的环境
   
   **virtualenv venv --python=python2.7**
3. 激活环境（切换到新环境目录）

    执行：source ./bin/activate
    例:
    
       cd env_workspace1 # 进入虚拟环境
       source bin/activate # 激活虚拟环境
4. 使用环境 （接下来直接pip安装需要的插件）

   注意！不要加sudo，否则会安装到系统环境中，没有安装到虚拟环境中执行：pip install xxx
   
   例：
   
        pip install flask     
        pip install django
        pip install Scipy
5. 退出环境

        deactivate
    
6. 删除环境 （需要在退出环境之后才能操作）
    
        rmvirtualenv 环境名

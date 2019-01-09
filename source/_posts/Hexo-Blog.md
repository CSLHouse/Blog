---
layout: fore-end
title: 利用Hexo搭建自己的博客
tags:
  - 前端
categories: 前端
comments: false    // 是否开启评论
date: 2018-07-20 20:47:03
---

## 原理
        GitHub会为每一个注册用户分配一个300M的个人空间，所以和hexo结合起来，利用github给我们的免费空间，
        将我们的静态网页托管到上面，就实现了一个免费的搭建个人博客的方案。
## 主要设计名词

- Git
- Hexo
- Node.js
- npm
- Markdown
- VS Code  
## 主要步骤
### 注册GitHub帐号，新建代码仓库
1. 登录GitHub网站，注册一个帐号
2. 新建一个代码仓库
3. 填写仓库信息，其中仓库名称必须和用户名一样

### 环境配置
1. 安装git。由于我使用的是MAC OS系统，自带了git功能，如果是其他系统，安装一个git客户端软件即可
2. 安装Node环境，Hexo就是基于Node的，访问速度特别快。进入node.js官网，选对操作系统，安装即可

### 安装Hexo，初始化博客

git和node环境安装好了以后。接下来就可以安装hexo了，直接在命令行中敲入如下代码：

    npm install -g hexo-cli
    
接下来建站

    hexo init <folder>  # 初始化博客，<文件夹名称>，表示文件会下载到当前目录下的这个文件夹内
    cd <folder>         # 进入到这个文件夹目录
    npm install         # 安装npm
使用相关编辑器打开这个文件夹<folder>中的文件夹，我是用的是[VS Code](https://code.visualstudio.com/)
### 本地预览

    hexo server # 开启本地服务器
然后在浏览器里面输入网址http://localhost:4000，就能看到默认主题的界面

*这是因为初始化hexo的时候就已经把一个能够起小型的网路服务器功能的依赖装好了，所以启动一下这个本地服务，就能看到hexo的默认主题*

### 上传到远程代码仓库
接下来就要将本地的代码上传到远程github仓库。首先建立和之前新建的代码仓库之间的关联，利用ssh key，具体是先用命令行在本地生成一个ssh key，
然后复制到github上去。

一. 先检查本地有没存在ssh key

    ls -al ~/.ssh  # 列出在.ssh文件夹下所有的文件，如果存在这个文件夹或者这些文件的话
二. 生成新的ssh key

    ssh-keygen -t rsa -C "your_email@example.com" # 注意将`your_email`替换成之前注册github帐号时的邮箱

生成成功或已经存在的，进入到~/.ssh/路径下，用文件编辑器打开文件，里面的内容就是ssk key，将内容复制到剪切板
1. 登录GitHub网站,依次点击Settings –> SSH and GPG keys –> New SSH key，进入新建SSK key页面，随便填写一下Title,然后将剪切板中复制好的ssh key复制到key中去，最后点击Add SSH key按钮，就OK了
2. 用VSCode打开博客主目录，找到主目录在的_config.yml文件，编辑这个文件，找到最下方deployment模块（顺便找到Site模块，修改一下博客的titile，subtitle，author，
改成你自己个性化的
   ），将deployment模块里面的代码替换为如下：
   
        deploy:
          type: git
            repository: https://github.com/用户名/用户名.github.io.git # 将用户名替换为你自己的github用户名
            branch: master

特别提醒：每个分号:后都要有一个空格，不然接下来生成和部署博客到github上时会报下面这种错误：

        JS-YAML: bad indentation of a mapping entry at line , column
三. 进到博客根目录下，先执行

    hexo generate       # 或者：hexo g  生成静态页面至public目录
    
如出现报错

    ERROR Local hexo not found in ~/blog
    ERROR Try runing: 'npm install hexo --save'
则执行：    
    
    npm install hexo --save
再执行：

    hexo deploy  # 或者：hexo -g   将.deploy目录部署到GitHub
如果无法连接到git,则执行：

    npm install hexo-deployer-git --save  # 安装hexo-deployer-git
最后再执行：

    hexo g
    hexo d

四. 进到浏览器，打开http://用户名.github.io网址，就可以看到和之前本地看到的hexo默认主题的首页，
看到你修改好的博客title，subtitle，author，等。至此，和github的关联已经建立好，接下来就可以写博客了

### 写博客
写博客主要使用到几个命令：

    hexo new "postName" # 新建文章 默认在\source\_posts\postName.md文件夹下
    hexo new page "pageName" #新建页面，如关于我界面
新建后，在source文件目录下生成一个以postName命名的.md文件，直接用markdown语法编辑，写文章就是这么简单。
hexo可以将markdown语法的文件渲染成静态的HTML。关于markdown语法，非常简单，上手很快，可以上网去搜一下语法
[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/)

    hexo clean # 删除之前生成的public文件夹和缓存数据
    hexo g     # 重新生成博客文件
    hexo d     # 将修改同步到github远程仓库
    
### 换主题，个性化配置
1. 更换主题
进入hexo相关主题展示的theme网站,选取一款你喜欢的主题。将你喜欢的主题clone到themes文件夹下，我选的是jacman主题，
直接编辑主目录下的_config.yml配置文件，找到theme,将默认的主题换成你喜欢的主题名即可
2. 配置主题样式
换完主题后，就可以修改主题样式了，主要修改主配置文件_config.yml以及主题文件下的配置文件_config.yml。
3. 添加第三方评论系统

使用[disqus](https://disqus.com/),直接注册，然后获取到duoshuo_shortname，填到jacman主题下面配置文件_config.yml对应的地方，第三方评论系统就安装好了


### 参考文章

[利用 GitHub Pages 快速搭建个人博客](https://www.jianshu.com/p/e68fba58f75c)
[我的个人技术博客搭建之旅](http://chenliangjing.me/2016/05/01/%E6%88%91%E7%9A%84%E4%B8%AA%E4%BA%BA%E6%8A%80%E6%9C%AF%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E4%B9%8B%E6%97%85/)

---
title: Git 常用命令
tags:
  - Git
categories:
  - Git
comments: false    // 是否开启评论
date: 2018-07-24 15:32:58
---

### Git global setup

    git config --global user.name "YourNames"
    git config --global user.email "YourMail"
    
Create a new repository

    git clone git@xxx.com:open/open.git
    cd open
    touch README.md
    git add README.md
    git commit -m "add README"
    git push -u origin master
Existing folder or Git repository

    cd existing_folder
    git init
    git remote add origin git@xxx.com:open/open.git
    git add .
    git commit
    git push -u origin master
如何删除本地分支

    git branch -d branch_name
如何删除远端分支

    git push origin :branch_name
远端分支已经删除，本地还出现这个分支

    git fetch origin -p 
如何撤销上一次提交 ***高风险操作***
    
    git reset ^1 --hard 









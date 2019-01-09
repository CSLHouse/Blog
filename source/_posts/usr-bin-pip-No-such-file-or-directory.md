---
title: '/usr/bin/pip: No such file or directory'
tags:
  - Python
categories:
  - Python
comments: false    // 是否开启评论
date: 2018-07-24 15:30:20
---

应用pip时出现You are using pip version 9.0.1, however version 10.0.1 is available.问题，说明需要更新，

使用sudo -H pip install --upgrade pip命令更新成功之后，运行pip --version，得到-bash: /usr/bin/pip: No such file or directory；

执行which pip得到/usr/local/bin/pip，可见是出现了缓存问题，

执行hash -r可清除缓存，pip正常

---
title: 彻底关闭windows Defender
tags:
  - windows10
  - windows defender
categories:
  - 前端
comments: false    // 是否开启评论
date: 2018-07-24 15:27:45
---

windows Defender是win10自带的保护电脑的工具，当遇到安装的软件性能出现问题时，需验证是否是windows Defender的影响.
[关闭windows Defender](https://jingyan.baidu.com/article/59a015e3702f57f7948865cb.html) （验证有效）：

1. win加r搜索输入regedit进入注册表编辑器
2. 找到‘HKEY_LOCAL_MACHINE’选项（左侧菜单栏第三个就是），打开。
3. 接下来找到SYSTEM，然后打开第三个选项。
4. 继续找‘CurrentControlSet’---‘Services’—‘SecurityHealthService’
5. 最后找到start时候打开，将里面数字2改为4重启即可永远关闭，开启重新改回2，再重启生效

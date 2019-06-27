---
title: django+xadmin开发OA系统遇到的问题
tags:
  - Python
  - jango
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-02-27 14:23:34
---

### 禁止访问 (403)
###### 错误详情
```
禁止访问 (403)
CSRF验证失败. 请求被中断.

Help
Reason given for failure:

    CSRF token missing or incorrect.
    
In general, this can occur when there is a genuine Cross Site Request Forgery, or when Django's CSRF mechanism has not been used correctly. For POST forms, you need to ensure:

Your browser is accepting cookies.
The view function passes a request to the template's render method.
In the template, there is a {% csrf_token %} template tag inside each POST form that targets an internal URL.
If you are not using CsrfViewMiddleware, then you must use csrf_protect on any views that use the csrf_token template tag, as well as those that accept the POST data.
The form has a valid CSRF token. After logging in in another browser tab or hitting the back button after a login, you may need to reload the page with the form, because the token is rotated after a login.
You're seeing the help section of this page because you have DEBUG = True in your Django settings file. Change that to False, and only the initial error message will be displayed.

You can customize this page using the CSRF_FAILURE_VIEW setting.

```  

###### 解决步骤:

1. django工程settings.py

MIDDLEWARE_CLASSES = (
    'django.middleware.common.CommonMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',#确认存在
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    # Uncomment the next line for simple clickjacking protection:
    # 'django.middleware.clickjacking.XFrameOptionsMiddleware',
)

2. html中的form添加模板标签{% csrf_token %}
``` 
  <form action="." method="post">{% csrf_token %}
```

3.  django工程
> def block_top_toolbar(self, context, nodes):
        context = get_context_dict(context or {})
    # 这里 xadmin/storm/model_list.top_toolbar.import.html 是自己写的html文件
        nodes.append(loader.render_to_string("xadmin/storm/model_list.top_toolbar.import.html", context=context))

### ValueError: could not convert string to float:

###### 问题描述
在调用Assets.objects.get_or_create时，错误总是锁定在最后一位参数。


### xadmin 添加excel导入按钮 然后启动 list_editable=['degree', 'students'] 编辑 degree字段报错

问题查找到时因为adminx.py文件里

> def post(self, request, *args, **kwargs):
  return super(AssetsAdmin, self).post(request, args, kwargs)

当把super(AssetsAdmin, self).post(request, args, kwargs)里的参数kwargs去掉后正常

### 在Django管理员更改列表中,如何显示空格而不是默认的“(无)”？

网上搜索empty_value_display,但不起作用


### 用pyinstaller打包时出现错误

> TypeError: expected str, bytes or os.PathLike object, not NoneType


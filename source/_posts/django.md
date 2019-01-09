---
title: django
tags:
  - Python
  - django
categories:
  - 前端
comments: false    // 是否开启评论
date: 2018-08-17 11:54:16
---

## 开启新项目
#### 创建项目

cd 到一个你想放置你代码的目录，然后运行以下命令：

    $ django-admin startproject mysite
运行：

    $ python manage.py runserver
更换端口：

    $ python manage.py runserver 8080
#### 创建应用
应用是一个专门做某件事的网络应用程序——比如博客系统，或者公共记录的数据库，或者简单的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

manage.py 所在的目录下，运行命令

    $ python manage.py startapp polls

## 编写视图
在 polls 目录里新建一个 urls.py 文件，输入如下代码：

    from django.urls import path
    
    from . import views
    
    urlpatterns = [
        path('', views.index, name='index'),
    ]
下一步是要在根 URLconf 文件中指定我们创建的 polls.urls 模块。在 mysite/urls.py 文件的 urlpatterns 列表里插入一个 include()， 如下：

    from django.contrib import admin
    from django.urls import include, path
    
    urlpatterns = [
        path('polls/', include('polls.urls')),
        path('admin/', admin.site.urls),
    ]
#### path() 参数
- route
    
route 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。

这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 https://www.example.com/myapp/ 时，它会尝试匹配 myapp/ 。处理请求 https://www.example.com/myapp/?page=3 时，也只会尝试匹配 myapp/。

- view

当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 HttpRequest 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。

-  kwargs

任意个关键字参数可以作为一个字典传递给目标视图函数。

- name

为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。

## 数据库配置
打开 mysite/settings.py，这个配置文件通常使用 SQLite 作为默认数据库。

如果想使用其他数据库，需要安装合适的[database bindings](https://docs.djangoproject.com/zh-hans/2.1/topics/install/#database-installation)，然后改变设置文件中 DATABASES 'default' 项目中的一些键值：
           
- ENGINE 

可选值有'django.db.backends.sqlite3'，'django.db.backends.postgresql'，'django.db.backends.mysql'，
或 'django.db.backends.oracle'。其它 可用后端。

- NAME - 数据库的名称。

如果使用的是SQLite，数据库将是你电脑上的一个文件，在这种情况下， NAME 应该是此文件的绝对路径，包括文件名。
默认值 os.path.join(BASE_DIR, 'db.sqlite3') 将会把数据库文件储存在项目的根目录。

如果你不使用 SQLite，则必须添加一些额外设置，比如 USER 、 PASSWORD 、 HOST 等等。
想了解更多数据库设置方面的内容，请看文档：[DATABASES](https://docs.djangoproject.com/zh-hans/2.1/ref/settings/#std:setting-DATABASES) 。

## 创建模型

模型 - 也就是数据库结构设计和附加的其它元数据。

    from django.db import models
    
    
    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')
    
    
    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

## 激活模型

上面的一小段用于创建模型的代码,Django 可以：
- 为这个应用创建数据库 schema（生成 CREATE TABLE 语句）。

- 创建可以与 Question 和 Choice 对象进行交互的 Python 数据库 API。

但是首先得把 polls 应用安装到项目里。

因为 PollsConfig 类写在文件 polls/apps.py 中，所以它的点式路径是 'polls.apps.PollsConfig'。
在文件 mysite/settings.py 中 INSTALLED_APPS 子项添加点式路径后，它看起来像这样：

    INSTALLED_APPS = [
        'polls.apps.PollsConfig',
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]
现在你的 Django 项目会包含 polls 应用。接着运行下面的命令：

    python manage.py makemigrations polls
    

## 写一个真正有用的视图
每个视图必须要做的只有两件事：返回一个包含被请求页面内容的 HttpResponse 对象，或者抛出一个异常，比如 Http404 。

首先，在 polls 目录里创建一个 templates 目录。Django 将会在这个目录里查找模板文件。

默认的设置文件设置了 DjangoTemplates 后端，并将 APP_DIRS 设置成了 True。这一选项将会让 DjangoTemplates 在每个 INSTALLED_APPS 
文件夹中寻找 "templates" 子目录。

在刚刚创建的 templates 目录里，再创建一个目录 polls，然后在其中新建一个文件 index.html 。
换句话说，你的模板文件的路径应该是 polls/templates/polls/index.html 。因为 Django 会寻找到对应的 app_directories ，
所以你只需要使用 polls/index.html 就可以引用到这一模板了。


## 自定义 应用 的界面和风格

首先，在你的 polls 目录下创建一个名为 static 的目录。
将以下代码放入样式表(polls/static/polls/style.css)：

    li a {
        color: green;
    }
下一步，在 polls/templates/polls/index.html 的文件头添加以下内容：

    {% load static %}
    
    <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
    {% static %} 模板标签会生成静态文件的绝对路径。





















---
title: '''Python-单例模式'''
tags:
  - Python
categories:
  - 前端
comments: false    // 是否开启评论
date: 2019-06-27 16:08:04
---
1. 使用__new__方法
   ```python
    class Singleton(object):
      def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
          orig = super(Singleton, cls)
          cls._instance = org.__new__(cls, *args, **kw)
        return cls._instance
    class MyClass(Singleton):
      a = 1
   ```
2. 装饰器版本
   ```python
   def singleton(cls):
    instances = {}
    def getinstance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

    @singleton
    class MyClass:
      ... 
   ```
3. 加线程锁
   ```python
    import threading
    class Singleton(object):
      _instance_lock = threading.Lock()  #添加类属性线程锁

      @classmethod
      def instance(cls, *args, **kw):
        with Singleton._instance_lock:  #创建实例时加线程锁，保证线程安全
          if not hasattr(Singleton, '_instance'):
            Singleton._instance = Singleton(*args, **kw)
        return Singleton._instance
    
    obj = Singleton.instance()
    print(obj)
   ```











  
---
title: django 添加自定义js
tags:
  - Python
  - django
categories:
  - 前端
comments: false    // 是否开启评论
date: 2019-09-11 17:05:01
---
# 前言
我想使用xadmin在列表页每一行元素添加一个按钮，当点击这个按钮的时候，能发个请求出去，后台执行相关功能

# 参考
https://blog.csdn.net/weixin_30695195/article/details/94980946
http://www.h5w3.com/?p=605

# 参考案例
在xadmin.py代码如下，使用self.vendor('xadmin.list.xxx.js', 'xadmin.form.css')加载自定义的xadmin.list.xxx.js脚本
```python
class ControlImage(object):
    # 显示     作者：上海-悠悠
    list_display = ['name', 'url', 'add_time', '操作']
 
    def 操作(self, obj):
        # button = '<button id="", type="submit" class="default btn btn-primary hide-xs" name="_delete" data-loading-text="删除"><i class="fa fa-save"></i>删除</button>'
        button = '<p id="%s" class="default btn btn-primary hide-xs" onclick="click_action_info(\'%s\')">执行</p>'%(str(obj.name),str(obj.name))
        r = mark_safe(button)
        return r
 
    def get_media(self):
        # media is the parent's return value (modified by any plugins)
        media = super(ControlImage, self).get_media() + self.vendor('xadmin.page.list.js', 'xadmin.page.form.js')
        # if self.list_display_links_details:
        #     media += self.vendor('xadmin.plugin.details.js', 'xadmin.form.css')
 
        # xadmin.list.xxx.js是自己写的js脚本
        media += self.vendor('xadmin.list.xxx.js', 'xadmin.form.css')
        return media
 
        # media = super(ControlImage,self).get_media()
        # media.add_js(('js/content.js',))    # 这种方法行不通，会报找不到.add_js方法
        # return media
  xadmin.site.register(UploadImage, ControlImage)
```

接下来把自己写的javascript脚本放到/xadmin/static/xadmin/js目录下,内容如下：
```js
;function click_action_info(id){
    var name=id
    $.ajax({
        url: '/network/notice/',
        type: 'POST',
        data: {'name': name},
        dataType: 'json',
        // cache: false,
        // processData: false,
        // contentType: false,//设置为false
        success: function (d) {
          console.log(d);
          if(d.success==true){
            alert(d.resDesc);
            setTimeout(function(){
              location.href = 'loginSuccess';
            },200);
          }else{
            alert(d.resDesc);
          }
        },
        error: function (d) {
          console.log(d);
        }
    })
```
注意前面要加个分号(;),要不然不生效

# 按钮响应
在views.py中写入按钮响应逻辑，比如发送邮件等
```python
from django.views.decorators.csrf import csrf_exempt
@csrf_exempt
def send_notice(request):
    name = request.POST.get('name')
    print('name:', name)
    return HttpResponse("发送")
```

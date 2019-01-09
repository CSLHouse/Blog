---
title: Python-读取文件
tags:
  - Python
categories:
  - Python
comments: false    // 是否开启评论
date: 2018-07-24 16:24:54
---

### 获取当前路径

    def get_file_path():
        # 获取脚本路径
        path = sys.path[0]
        if os.path.isdir(path):
            return path
        elif os.path.isfile(path):
            return os.path.dirname(path)
### 读取文件

    item_object = open(item_txt_path, "rU")
        try:
            for line in islice(item_object, 1, None):
                id_str = re.split(r"\t", line)
                id_list = {'id': id_str[0], 'name': id_str[1]}
                self.idArr.append(id_list)
        finally:
            item_object.close()
islice(item_object, 1, None)跳过第一行

[github](https://github.com/CSLHouse/Python-ReadFile-UploadMysql.git)

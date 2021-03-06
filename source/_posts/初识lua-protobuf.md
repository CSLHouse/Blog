---
title: 初识lua-protobuf
tags:
  - lua
  - lua-protobuf
categories:
  - 后端
comments: false    // 是否开启评论
date: 2019-01-23 15:25:07
---

### 资源下载
[源代码网址](http://www.github.com/starwing/lua-protobuf)

[lua-protobuf源代码文档](http://www.github.com/starwing/lua-protobuf/wiki)

[lua-protobuf源代码下载](http://www.github.com/starwing/lua-protobuf/releases)

Git Clone代码到本地: 复制代码

> git clone http://www.github.com/starwing/lua-protobuf

Subversion代码到本地: 复制代码
> svn co --depth empty http://www.github.com/starwing/lua-protobuf

Checked out revision 1.
> cd repo

> svn up trunk
###  用法
#### protoc 模块
函数返回描述

```angular2html
protoc.new()	Proroc对象	创建新的编译器实例
protoc.reload()	true	将所有google标准消息重新加载到 pb 模块中
p:parse(string)	表格	将架构转换为 DescriptorProto 表
p:parsefile(string)	表格	类似 p:parse()，但接受文件名
p:compile(string)	字符串	将架构转换为二进制 *.pb 格式数据
p:compilefile(string)	字符串	类似 p:compile()，但接受文件名
p:load(string)	true	将架构加载到 pb 模块中
p:loadfile(string)	true	类似 pb:loadfile()，但接受文件名
p.loaded	表格	包含所有已经解析的DescriptorProto 表
p.paths	表格	表包含导入搜索目录
p.unknown_module	请参见下面	处理架构导入错误
p.unknown_type	请参见下面	处理架构中的未知类型
p.include_imports	bool	自动加载导入的Prototype
要分析文本文件，你应该首先创建一个编译器实例：

```


#### pb 模块
pb 模块具有高级例程，可以用于 maniplate protobuf消息。

在下面的函数中，有几种类型具有特殊的含义：

type: 指示protobuf消息类型的字符串，".Foo" 表示未声明 package 语句的原始文件中的类型。 "foo.Foo" 表示原始文件中声明 package foo;的类型

data: 可以是字符串，pb.slice 值或者 pb.buffer 值。

iterator: 可以在 Lua for in 语句中使用的函数，e.g.

复制代码
for name in pb.types() doprint(name)end
当遇到错误时，所有函数都返回 nil, errmsg 。
函数返回描述

```angular2html
pb.clear()	无	清除所有类型
pb.clear(type)	无	删除特定类型
pb.load(data)	true	将二进制模式数据加载到 pb 模块中
pb.loadfile(string)	true	pb.load() 相同，但接受文件名
pb.encode(type, table)	字符串	将消息表编码为二进制形式
pb.encode(type, table, b)	缓冲区	将消息表编码为二进制形式以缓冲
pb.decode(type, data)	表格	将二进制消息解码为Lua表
pb.decode(type, data, table)	表格	将二进制消息解码为给定的Lua表
pb.pack(fmt,.. .)	字符串	buffer.pack() 相同但返回字符串
pb.unpack(data, fmt,.. .)	值。	slice.unpack() 相同但接受数据
pb.types()	迭代器	迭代 pb 模块中的所有类型
pb.type(type)	请参见下面	返回特定类型的信息
pb.fields(type)	迭代器	迭代消息中的所有字段
pb.field(type, string)	请参见下面	返回类型特定字段的信息
pb.enum(type, string)	号码	按名称获取 enum的值
pb.enum(type, number)	字符串	按值获取 enum的名称
pb.defaults(type[, table])	表格	获取类型的默认表
pb.option(string)	字符串	将选项设置为解码器/编码器
```
你可以使用 pb.(type|field)[s]() 函数来检索已经加载消息的类型信息。

pb.type() 为指定类型返回多个信息：

名称：类型的完整qualitier名称，比如 。package 。typename"
basename: 没有软件包前缀的类型名称，比如"typename""
："|"枚举"|"消息" 类型是否为map_entry类型，enum 类型或者消息类型。
pb.types() 返回一个迭代器，如所有消息类型上的调用 pb.type() 。

> print(pb.type"MyType")-- list all types that loaded into pbfor name, basename, type in pb.types() doprint(name, basename, type)end

pb.field() 为一种类型返回指定字段的信息：

名称：字段名称
编号：架构中的字段数
类型：字段类型
默认值：如果没有默认值，则为 nil
可选"|"重复"|"：字段标签，可选或者重复，要求不支持
如果这是一个字段，这是一个名称和索引
然后 pb.fields() 迭代消息中的所有字段：

> print(pb.field("MyType", "the_first_field"))-- notice that you needn't receive all return values from iteratorfor name, number, type in pb.fields"MyType"doprint(name, number, type)end

pb.enum() 映射来自 enum 名称和值：

> protoc:load[[enum Color { Red = 1; Green = 2; Blue = 3 }]]print(pb.enum("Color", "Red")) --> 1print(pb.enum("Color", 2)) -->"Green"

使用 pb.defaults()，你可以获得一个包含消息中所有默认值的表：

> check_load[[ message TestDefault { optional int32 defaulted_int = 10 [ default = 777 ]; optional bool defaulted_bool = 11 [ default = true ]; optional string defaulted_str = 12 [ default ="foo" ]; optional float defaulted_num = 13 [ default = 0.125 ]; } ]]print(require"serpent".block(pb.defaults"TestDefault"))-- output:-- {-- defaulted_bool = true,-- defaulted_int = 777,-- defaulted_num = 0.125,-- defaulted_str ="foo"-- } --[[table: 0x7f8c1e52b050]]


转载自：[lua-protobuf,使用protobuf得Lua模块](https://www.helplib.com/GitHub/article_143162)
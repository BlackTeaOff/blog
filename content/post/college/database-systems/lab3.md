---
title: "数据库概论 - Lab3"
description: 记录一下 Lab3 的一些思路
date: 2026-10-26T22:08:35+08:00
image: 
math: 
license: 
comments: true
draft: false
build:
    list: always    # Change to "never" to hide the page from the list
categories:
- "数据库概率"
- "大学课程"
tags:
- "实验"
- "数据库"
---

# 前言

还没弄完，有点忙，周四还考试，周五俩实验。

忙，忙点好啊......

> 老实学习，很枯燥吧？(笑
>
> 快来用 Claude Code 吧！
>
> 试一下不会怎么样的。
>
> 一段时间后....
>
> 当事人: 一开始以为，只试一次不会怎么样的(.jpg)，结果就.....（悲
>
> 珍惜生命，远离 Vibe Coding。

# 数据库概论 - Lab3

## 概念

### 元组

数据库表中的一行。

### 关系与关系模式

大概就是指表和表里面的结构吧。（都存什么东西之类的）

### 属性

数据库表中的一列

### 数据字典

存放各个表的信息，例如有多少表，表名叫什么。

表里都有哪些属性，主键外键，是否设置非空等信息。

### for i in range(5)

仍然左闭右开。


## 思路

### 在 Python 中读取文件

```python
# with - 退出缩进时自动关闭文件, as 给这个文件对象起别名
with open("fileName.txt", "r", encoding="utf-8") as f:
	for line in f:
		# strip 可以去掉每一行末尾自带的换行符 \n 和空格
		print(line.strip())
	# 或者, 返回一个 List
	lines = f.readlines()
	# 或者, 解析 json 存到字典里
	dict = json.load(f)
```

### 数据字典

用 json 存储。（键值对）

存到 data_dict.json 里面，里面存各个表与该表的属性，属性还有属性名和类型。

使用**嵌套字典**，假如一个表叫 student，那就在这个字典里加上一个键叫 student，

里面的值仍然是一个字典，这个字典里面又有多个键值对，分别是属性名和类型。

例子：

> 这个是嵌套字典，外面的字典存多个键，这些键是不同的表
>
> 这些键对应的值又是一个字典，里面存放这个表的各种属性
>
> 读取方式 data_dict["student"\]["id"]
>
> data_dict["student"] 取出的是 student 这个键对应的字典，
>
> 再加 ["id"] 取出 id 键对应的值

```json
{
    "student": {
    	"id": "int",
    	"name": "str",
    	"age": "int"
    }
}
```

> 下面这个不是嵌套字典！！是字典里面值的部分放了个列表
>
> 列表里面又有多个字典。

```json
{
    "student": [
        {"name": "id", "type": "int"},
        {"name": "name", "type": "str"},
        {"name": "age", "type": "int"}
    ],
    "department": [
        {"name": "dnumber", "type": "str"},
        {"name": "dname", "type": "str"}
    ]
}
```

Python 中能把字典转为 json，使用 dump 方法。

把 json 转换为字典，使用 json.load。


### 表

用 txt 存储，一行就代表表里的一个元组，不同属性用逗号分隔。（类 CSV）



### 遍历字典

使用 `字典名.items` 能返回字典内所有键值对的元组。

> for key, value in 字典.items() 遍历每一个元组



### strip

用于去掉字符串两端的符号。

> 可能有的时候先 strip() 之后再 strip("某个具体字符") 更好，
>
> 因为可能左面右面可能有空格什么的。

### split

用某个字符把字符串分割成若干部分。

### join 方法

用于将可迭代的对象（列表，元组，字符串等）中的元素用指定的分隔符连接成一个新的字符串。

例:

```python
# 用逗号和空格连接列表元素
words = ["Apple", "Banana", "Orange"]
result = ", ".join(words)
print(result)  
# 输出: Apple, Banana, Orange

# 直接拼接，不使用任何分隔符
chars = ["P", "y", "t", "h", "o", "n"]
result = "".join(chars)
print(result)  
# 输出: Python
```



## 代码

### 全局变量

数据字典：

data_dict: dict = {}

> 名字: 类型
>
> Python 中不初始化就无视。

### load_dict

从文件读取数据字典，加载到内存。

> 修改全局变量的值必须加 global，否则会当成一个局部变量。
>
> 使用 json.load

### save_dict

把内存中的字典写回文件。（数据字典文件）

> 使用 json.dump

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



累了，我想说的都在注释里了。



### create_table

```python
# "CREATE TABLE student (id int, name str);"
def create_table(sql: str):
    # 先去首尾空格和分号
    # strip 去首尾空格(不写参数就是空格), rstrip 只清理右侧
    sql = sql.strip().rstrip(";")

    # 按 ( 分割, 拿到 CREATE TABLE student 和 id int, name str)
    front_part = sql.split("(")[0]
    # 后面部分去掉右面的 ) 就是后半部分, id int, name str
    # 可以用 , 直接分开各个属性
    back_part = sql.split("(")[1].rstrip(")")
    # print(front_part)
    # print(back_part)

    # 前半部分用空格分开, 第二个就是表名(从0开始)
    table_name = front_part.split()[2]
    # print(table_name)

    # 检查数据字典里有没有同名表, 有就不行
    if table_name in data_dict:
        print("存在同名表, 无法创建")
        return

    # 建立该表文件
    open(f"{table_name}.txt", "w", encoding="utf-8").close()

    # 向字典插入一个键, 值暂时是一个空字典(里面存多种属性)
    data_dict[table_name] = {}

    # 用 , 分开各个属性, 得到 'id int', 'name str'
    # split 返回的是 List, 可以遍历每一个属性
    # 按照空格分开, 得到属性名和属性类型
    properties = back_part.split(",")
    print(properties)

    # 遍历每一个属性
    for item in properties:
        # 通过空格分开, 得到名字和属性
        name = item.split()[0]
        type = item.split()[1]

        if type not in SUPPORTED_TYPES:
            print(f"不支持类型 {type}")
            # 在字典中删除刚才建的键和文件
            del data_dict[table_name]
            os.remove(table_name + ".txt")
            return

        # name 作为内层字典的键, 因为键不重复, 数据库里的属性名也不重复
        data_dict[table_name][name] = type

    save_dict()
```

### insert_into

```python
# "INSERT INTO student VALUES (1, '张三');"
def insert_into(sql: str):
    # 去首位空格与后面的;
    # INSERT INTO student VALUES (1, '张三')
    sql = sql.strip().rstrip(";")

    # INSERT INTO student VALUES
    front_part = sql.split("(")[0]
    # 1, '张三'
    back_part = sql.split("(")[1].rstrip(")")
    # print(front_part)
    # print(back_part)

    table_name = front_part.split()[2]
    # print(table_name)
    
    if table_name not in data_dict:
        print(f"没有名为 {table_name} 的表")
        return

    # ['1', " '张三' "]
    properties = back_part.split(",") 

    # 去掉空格
    for i in range(len(properties)):
        properties[i] = properties[i].strip()
    # print(properties)

    if len(properties) != len(data_dict[table_name]):
        # len(字典) 返回字典的键值对数量
        print(f"表中属性数量: {len(data_dict[table_name])} 与提供属性数量: {len(properties)} 不一致")
        return

    # 按序遍历 properties
    i = 0
    # 遍历该表的属性, 与其输入的属性对应(插入属性顺序与建表顺序相同)
    # key 是 name, value 是 type
    for key, value in data_dict[table_name].items():
        # print(key, value)
        if (value == "int"):
            if not (properties[i].isdigit()):
                print(f"第 {i + 1} 个属性应为数字")
                return

        # 是字符串就去掉两个单引号
        if (value == "str" or value == "varchar"):
            # print(properties[i])
            # 必须先去掉空格, 要不然单引号不在最左边/最右边, strip 去不掉
            # 这个空格产生的原因是(1, '张三'), 张三的前面有个空格
            # 前面代码遍历 properties 的时候逐个去掉空格就好了
            properties[i] = properties[i].strip("'")
            # print(properties[i])
                   
        i = i + 1

    # print(properties)
    # 把插入的属性存入文件
    # "1, 张三\n"
    row: str = ""
    # range 左闭右开
    for i in range(len(properties)):
        row += properties[i]
        # 最后一个属性不加逗号
        if (i != len(properties) - 1):
            row += ","
    row += "\n"
    print(row)
    with open(f"{table_name}.txt", "a", encoding="utf-8") as f:
        f.write(row)
```

### pad_str

```python
# 传入一个字符串和一个目标宽度, 返回一个和目标宽度一样长的字符串(补空格)
def pad_str(text: str, target_width: int) -> str:
    # ljust 把汉字和英文字符都当成长度是 1 的字符
    # 但是在控制台输出的时候, 汉字显示的宽度是英文字母的 2 倍
    # ord(c) > 127 代表是汉字，宽度算 2, 否则是 ASCII 英文字符，算 1
    # ord - 计算一个字符的 Unicode(0-127和 ASCII 完全相同)
    actual_width = 0
    for c in text:
        if ord(c) < 127:
            actual_width += 1
        else:
            actual_width += 2
    # 补空格, max 是为了防止出现负数(实际长度比表长还长)
    # 如果是负数就不补
    return text + " " * max(0, target_width - actual_width)
```


### select_from

```python
# SELECT * FROM STUDENT;
def select_from(sql: str):
    # 去除空格和后面的 ;
    sql = sql.strip().rstrip(";")

    # 空格分割
    parts = sql.split()
    table_name = parts[3]
    # print(table_name)

    # 检查是否存在
    if table_name not in data_dict:
        print(f"不存在名为 {table_name} 的表")
        return

    # 表头存到列表中
    headers = []
    for key, value in data_dict[table_name].items():
        headers.append(f"{key}({value})")
    header_line = " | ".join(headers)
    header_line = "| " + header_line + " |"
    # | 的位置, 用于把上下分隔线里面和 | 对齐的位置改成 +
    pipe_index = []
    for i in range(len(header_line)):
        if (header_line[i] == '|'):
            pipe_index.append(i)
    # print(pipe_index)
    # +-----+-----+
    # 分隔线
    divider = ""
    for i in range(len(header_line)):
        if (i in pipe_index):
            divider += "+"
        else:
            divider += "-"
    print(divider)
    print(header_line)
    print(divider)

    '''
    # 打印表的属性名及其类型
    # 遍历 data_dict[table_name], 即遍历该表里面的字典(存了该表的所有字段)
    for key, value in data_dict[table_name].items():
        print(key, value, end="")
        print("|", end="")
    print()
    '''
    # 打印文件(表数据)各行数据
    with open(f"{table_name}.txt", "r", encoding="utf-8") as f:
        for line in f:
            # 因为 print 默认会输出换行符, 所以去掉文件中的换行符
            # 按照 , 分开变成列表
            line_list = line.strip().split(",")

            # 给每个元素对齐上面的表头长度
            for i in range(len(line_list)):
                line_list[i] = pad_str(line_list[i], len(headers[i]))

            # 左右加上 |
            # 中间把对齐好的每一项用 | 分隔开
            print("| " + " | ".join(line_list) + " |")

    print(divider)
```

### drop_table

```python
# DROP TABLE student;
def drop_table(sql: str):
    # 去前后空格与 ;
    sql = sql.strip().rstrip(";")

    parts = sql.split()
    table_name = parts[2]

    if (table_name not in data_dict):
        print(f"不存在名为 {table_name} 的表")
        return

    # 删除数据字典该表的键并存回文件
    del data_dict[table_name]
    save_dict()

    os.remove(f"{table_name}.txt")
```

### delete_from

```python
# DELETE FROM student;
# DELETE FROM student WHERE id = 1;
def delete_from(sql: str):
    sql = sql.strip().rstrip(";")
    parts = sql.split()
    table_name = parts[2]

    if table_name not in data_dict:
        print(f"不存在名为 {table_name} 的表")
        return

    # 没有 WHERE 条件直接清空
    if "WHERE" not in sql:
        open(f"{table_name}.txt", "w", encoding="utf-8").close()
        print(f"已清空表 {table_name} 的所有数据")
        return

    col_name = parts[4]
    target_value = parts[6]

    # 看表里有没有这个属性
    if col_name not in data_dict[table_name]:
        print(f"表 {table_name} 中没有名为 {col_name} 的属性")
        return

    # 找到这个属性的索引(在文件中是第几列)
    # 拿出该表所有的键放在 list 里, list 的 index 方法查找某个元素在序列里第一次出现的索引位置
    index = list(data_dict[table_name].keys()).index(col_name)

    # 一行一行读取文件
    # 属性1, 属性2, .....
    # 用逗号分开
    rewrite_lines = ""
    with open(f"{table_name}.txt", "r", encoding="utf-8") as f:
        for line in f:
            # 用 , 分开改行的所有属性
            properties = line.strip().split(",")
            # 看要找的这个属性等不等于要找的这个值
            if properties[index] == target_value:
                # 等于就不写入这一行了(算删除)
                continue
            else:
                # 不等于就写入
                rewrite_lines += line

    # w 清空再写入
    with open(f"{table_name}.txt", "w", encoding="utf-8") as f:
        f.write(rewrite_lines)
```



### 源代码

```python
import json, os

SUPPORTED_TYPES = ["int", "str", "float", "char", "varchar"]
DICT_FILE = "data_dict.json"
# 数据字典
data_dict: dict = {}

def load_dict():
    try:
        with open(DICT_FILE, "r", encoding="utf-8") as f:
            global data_dict
            data_dict = json.load(f)
            print("成功从文件加载数据字典")
    except FileNotFoundError:
        print("文件不存在, 新建文件")
        save_dict()
    except json.JSONDecodeError:
        print("文件不是有效的 json 格式")


def save_dict():
    with open(DICT_FILE, "w", encoding="utf-8") as f:
        # 自动缩进
        json.dump(data_dict, f, indent=4)
        print("数据字典保存成功")
 
# "CREATE TABLE student (id int, name str);"
def create_table(sql: str):
    # 先去首尾空格和分号
    # strip 去首尾空格(不写参数就是空格), rstrip 只清理右侧
    sql = sql.strip().rstrip(";")

    # 按 ( 分割, 拿到 CREATE TABLE student 和 id int, name str)
    front_part = sql.split("(")[0]
    # 后面部分去掉右面的 ) 就是后半部分, id int, name str
    # 可以用 , 直接分开各个属性
    back_part = sql.split("(")[1].rstrip(")")
    # print(front_part)
    # print(back_part)

    # 前半部分用空格分开, 第二个就是表名(从0开始)
    table_name = front_part.split()[2]
    # print(table_name)

    # 检查数据字典里有没有同名表, 有就不行
    if table_name in data_dict:
        print("存在同名表, 无法创建")
        return

    # 建立该表文件
    open(f"{table_name}.txt", "w", encoding="utf-8").close()

    # 向字典插入一个键, 值暂时是一个空字典(里面存多种属性)
    data_dict[table_name] = {}

    # 用 , 分开各个属性, 得到 'id int', 'name str'
    # split 返回的是 List, 可以遍历每一个属性
    # 按照空格分开, 得到属性名和属性类型
    properties = back_part.split(",")
    print(properties)

    # 遍历每一个属性
    for item in properties:
        # 通过空格分开, 得到名字和属性
        name = item.split()[0]
        type = item.split()[1]

        if type not in SUPPORTED_TYPES:
            print(f"不支持类型 {type}")
            # 在字典中删除刚才建的键和文件
            del data_dict[table_name]
            os.remove(table_name + ".txt")
            return

        # name 作为内层字典的键, 因为键不重复, 数据库里的属性名也不重复
        data_dict[table_name][name] = type

    save_dict()

# "INSERT INTO student VALUES (1, '张三');"
def insert_into(sql: str):
    # 去首位空格与后面的;
    # INSERT INTO student VALUES (1, '张三')
    sql = sql.strip().rstrip(";")

    # INSERT INTO student VALUES
    front_part = sql.split("(")[0]
    # 1, '张三'
    back_part = sql.split("(")[1].rstrip(")")
    # print(front_part)
    # print(back_part)

    table_name = front_part.split()[2]
    # print(table_name)
    
    if table_name not in data_dict:
        print(f"没有名为 {table_name} 的表")
        return

    # ['1', " '张三' "]
    properties = back_part.split(",") 

    # 去掉空格
    for i in range(len(properties)):
        properties[i] = properties[i].strip()
    # print(properties)

    if len(properties) != len(data_dict[table_name]):
        # len(字典) 返回字典的键值对数量
        print(f"表中属性数量: {len(data_dict[table_name])} 与提供属性数量: {len(properties)} 不一致")
        return

    # 按序遍历 properties
    i = 0
    # 遍历该表的属性, 与其输入的属性对应(插入属性顺序与建表顺序相同)
    # key 是 name, value 是 type
    for key, value in data_dict[table_name].items():
        # print(key, value)
        if (value == "int"):
            if not (properties[i].isdigit()):
                print(f"第 {i + 1} 个属性应为数字")
                return

        # 是字符串就去掉两个单引号
        if (value == "str" or value == "varchar"):
            # print(properties[i])
            # 必须先去掉空格, 要不然单引号不在最左边/最右边, strip 去不掉
            # 这个空格产生的原因是(1, '张三'), 张三的前面有个空格
            # 前面代码遍历 properties 的时候逐个去掉空格就好了
            properties[i] = properties[i].strip("'")
            # print(properties[i])
                   
        i = i + 1

    # print(properties)
    # 把插入的属性存入文件
    # "1, 张三\n"
    row: str = ""
    # range 左闭右开
    for i in range(len(properties)):
        row += properties[i]
        # 最后一个属性不加逗号
        if (i != len(properties) - 1):
            row += ","
    row += "\n"
    print(row)
    with open(f"{table_name}.txt", "a", encoding="utf-8") as f:
        f.write(row)

# 传入一个字符串和一个目标宽度, 返回一个和目标宽度一样长的字符串(补空格)
def pad_str(text: str, target_width: int) -> str:
    # ljust 把汉字和英文字符都当成长度是 1 的字符
    # 但是在控制台输出的时候, 汉字显示的宽度是英文字母的 2 倍
    # ord(c) > 127 代表是汉字，宽度算 2, 否则是 ASCII 英文字符，算 1
    # ord - 计算一个字符的 Unicode(0-127和 ASCII 完全相同)
    actual_width = 0
    for c in text:
        if ord(c) < 127:
            actual_width += 1
        else:
            actual_width += 2
    # 补空格, max 是为了防止出现负数(实际长度比表长还长)
    # 如果是负数就不补
    return text + " " * max(0, target_width - actual_width)


# SELECT * FROM STUDENT;
def select_from(sql: str):
    # 去除空格和后面的 ;
    sql = sql.strip().rstrip(";")

    # 空格分割
    parts = sql.split()
    table_name = parts[3]
    # print(table_name)

    # 检查是否存在
    if table_name not in data_dict:
        print(f"不存在名为 {table_name} 的表")
        return

    # 表头存到列表中
    headers = []
    for key, value in data_dict[table_name].items():
        headers.append(f"{key}({value})")
    header_line = " | ".join(headers)
    header_line = "| " + header_line + " |"
    # | 的位置, 用于把上下分隔线里面和 | 对齐的位置改成 +
    pipe_index = []
    for i in range(len(header_line)):
        if (header_line[i] == '|'):
            pipe_index.append(i)
    # print(pipe_index)
    # +-----+-----+
    # 分隔线
    divider = ""
    for i in range(len(header_line)):
        if (i in pipe_index):
            divider += "+"
        else:
            divider += "-"
    print(divider)
    print(header_line)
    print(divider)

    '''
    # 打印表的属性名及其类型
    # 遍历 data_dict[table_name], 即遍历该表里面的字典(存了该表的所有字段)
    for key, value in data_dict[table_name].items():
        print(key, value, end="")
        print("|", end="")
    print()
    '''
    # 打印文件(表数据)各行数据
    with open(f"{table_name}.txt", "r", encoding="utf-8") as f:
        for line in f:
            # 因为 print 默认会输出换行符, 所以去掉文件中的换行符
            # 按照 , 分开变成列表
            line_list = line.strip().split(",")

            # 给每个元素对齐上面的表头长度
            for i in range(len(line_list)):
                line_list[i] = pad_str(line_list[i], len(headers[i]))

            # 左右加上 |
            # 中间把对齐好的每一项用 | 分隔开
            print("| " + " | ".join(line_list) + " |")

    print(divider)

# DROP TABLE student;
def drop_table(sql: str):
    # 去前后空格与 ;
    sql = sql.strip().rstrip(";")

    parts = sql.split()
    table_name = parts[2]

    if (table_name not in data_dict):
        print(f"不存在名为 {table_name} 的表")
        return

    # 删除数据字典该表的键并存回文件
    del data_dict[table_name]
    save_dict()

    os.remove(f"{table_name}.txt")

# DELETE FROM student;
# DELETE FROM student WHERE id = 1;
def delete_from(sql: str):
    sql = sql.strip().rstrip(";")
    parts = sql.split()
    table_name = parts[2]

    if table_name not in data_dict:
        print(f"不存在名为 {table_name} 的表")
        return

    # 没有 WHERE 条件直接清空
    if "WHERE" not in sql:
        open(f"{table_name}.txt", "w", encoding="utf-8").close()
        print(f"已清空表 {table_name} 的所有数据")
        return

    col_name = parts[4]
    target_value = parts[6]

    # 看表里有没有这个属性
    if col_name not in data_dict[table_name]:
        print(f"表 {table_name} 中没有名为 {col_name} 的属性")
        return

    # 找到这个属性的索引(在文件中是第几列)
    # 拿出该表所有的键放在 list 里, list 的 index 方法查找某个元素在序列里第一次出现的索引位置
    index = list(data_dict[table_name].keys()).index(col_name)

    # 一行一行读取文件
    # 属性1, 属性2, .....
    # 用逗号分开
    rewrite_lines = ""
    with open(f"{table_name}.txt", "r", encoding="utf-8") as f:
        for line in f:
            # 用 , 分开改行的所有属性
            properties = line.strip().split(",")
            # 看要找的这个属性等不等于要找的这个值
            if properties[index] == target_value:
                # 等于就不写入这一行了(算删除)
                continue
            else:
                # 不等于就写入
                rewrite_lines += line

    # w 清空再写入
    with open(f"{table_name}.txt", "w", encoding="utf-8") as f:
        f.write(rewrite_lines)


def main():
    load_dict()

    while (1):
        sql: str = input()

        if (sql.startswith("CREATE TABLE")):
            create_table(sql)
        elif sql.startswith("INSERT INTO"):
            insert_into(sql)
        elif sql.startswith("SELECT"):
            select_from(sql)
        elif sql.startswith("DELETE FROM"):
            delete_from(sql)
        elif sql.startswith("DROP TABLE"):
            drop_table(sql)
        else:
            print("语法错误")

    '''
    create_table("CREATE TABLE student (id int, name str);")
    insert_into("INSERT INTO student VALUES (1, '张三')")
    insert_into("INSERT INTO student VALUES (2, '李四')")
    select_from("SELECT * FROM student")
    delete_from("DELETE FROM student WHERE id = 1")
    select_from("SELECT * FROM student")
    # drop_table("DROP TABLE student")
    # select_from("SELECT * FROM student")
    # print(data_dict)
    '''

if __name__ == "__main__":
    main()
```



>非最终版本，查完实验会更新的。

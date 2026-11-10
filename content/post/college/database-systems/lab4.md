---
title: "数据库概论 - Lab4"
description: 
date: 2026-11-06T21:24:18+08:00
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

# 数据库概论 - Lab4

## 列名二义性

SELECT 一个表的时候，后面的属性可以不写 `表名.属性名`，因为一个表的属性不重复。

但是用 WHERE 连接两个表的时候，如果两个表有同名属性，SELECT 后面重名的属性名就要加上表名。

### 处理方法

如果发现属性里有 `.`，直接去对应表里找。

如果属性里没有，就去所有表里找，如果找到两个及以上，就报错。

## 分离参数

```mysql
SELECT student.id, student.name, course.id, course.name, grade FROM student, course, sc
WHERE student.id = sc.stuId AND course.id = sc.courId AND grade > 60;
```

### find()

find 方法会返回子字符串第一次出现的起始索引。

不存在返回 -1

```python
text = "Hello world"

# index = 6
index = text.find("word")
```

### 切片（Slicing）

从字符串中提取某个索引到某个索引的字符。

范围左闭右开。

```python
text = "Hello world"

# result = "world"
result = text[6:11]

# 从 6 到最后
rusult = text[6:]
```

### replace()

replace(“被替换的内容”, “要替换成什么”)

在这里用来去掉空格，strip 只能去掉两端的。



emmmm，废弃掉了。

因为他会把 WHERE 里的各种条件都粘在一起。

带空格的字符串也无了。

## 执行顺序

### FROM - 连接 / 笛卡尔积

单表直接读出所有行，多表就笛卡尔积拼成大表。

### WHERE - 选择

按 AND 拆开条件，判定比较符

条件分为两种。

连接条件（属性 = 属性）和选择条件（属性 = 常量）。

### SELECT - 投影

如果 * 就全保留，指定列就只要这几列的数据。

## 各阶段思路

### FROM 阶段

all_headers = []，存放大表的列名：["student.id", "student.name", ...]

`['student.id', 'student.sname', 'course.id', 'course.cname']`

做完笛卡尔积之后，两个表合并的属性。

tables_data = []，存每个表的行数据：[[student 的所有行], [course 的所有行]]

`[[['2', 'man'], ['1', 'test']], [['2', 'computer'], ['1', 'test']]]`

最外层括号是整个数据库的数据，里面一层是一个表的数据，在里面就是一行的数据。

用 `,` 分离各表名，检查表是否存在。

遍历每个表，把属性（在数据字典中）都写入 all_headers 里。（加上 `表名.属性名`）

把每一行的数据都写到 tables_data 里，不同表之间用嵌套列表隔开，表中不同行也隔开。

### WHERE 阶段

用 AND 把 WHERE 后面的条件分成若干条条件

每个条件由 左边（Left），操作符（Op），右边（Right）组成

**左面**必定是属性，去找这个属性在数据字典的索引，存到 `left_idx`。

> 为什么要找索引？
>
> 属性的索引能定位到该属性在什么位置，就能取出他的值。

找不到就报错。

判断**右面**是属性还是常量值（连接条件还是选择条件）

- 带引号就是字符串常量，去掉引号用

- 纯数字就是数字常量，转为 int 用

- 不带引号也不是纯数字，就去 headers 里看看是不是某个列名。

  找到了就是连接条件，记下索引 `right_idx`。

  找不到就是输入错误，报错。

遍历每个条件，把结果存在字典里。

格式：`{"type": "JOIN/VALUE", "left_idx": ..., "right_idx": ..., "op": "...", "val": ..., "val_type": "..."}`

实际上是把每个条件翻译成**键值对**，这样后面好读取。

#### find_col_index

用来找属性在表中是第几个属性，这个索引能直接在数据中定位到对应的值。

可能会有多个表，里面的属性名会重复，所以会检查格式是不是 `表名.属性名`

如果格式是 `表名.属性名`，直接去 all_headers 里找对应属性，并用 index 返回索引。

all_headers 里存的格式也是 `表名.属性名`。

如果没写表名，只有属性名，需要看看是不是有重名的属性。

如果有就不行。（count  > 1）

最后返回。

#### 根据条件保留/删除行

filtered_result 存储根据条件要保留的行。

遍历 result（笛卡尔积之后得到的所有数据，格式是[[属性一，属性二]，[属性一，属性二]......]）

取出 line（[属性一，属性二]），keep 代表要不要留这一行。

之后遍历每个条件，如果有一个条件不符合，就直接 break 到下一行数据。

因为 result 的信息都是从文件里读取出来的（spilt(","))），所以每个值都是字符串类型。

在判断每个条件符不符合的时候，要看给的条件右面值的类型去判断。

如果右面的类型是 int，需要先把左边的转为 int 之后在比较。（默认是 string）

如果不是 int，就直接比较。（按照 string 比较）

### SELECT 阶段

`final_headers` - 存放投影之后的各属性的列名。（格式：["header1, header2"......]）

`final_rows` - 存放投影之后各属性的值。（格式：[[属性1, 属性2], [属性1, 属性2]）

里面的每一个嵌套列表标识一个行的数据。

如果用户输入 *，直接让 `final_headers = all_headers`，`final_rows = result`

不是的话，用 properties_index 存放用户输入每一个列名在表里的 index，

遍历用户输入的 properties，用之前的 `find_col_index`找到对应的 index，append 到 properties_index。

之后遍历 properties_index，把 all_headers 里对应 index 的值（列名）取出来 append 到 final_headers。

这样就有了所有投影出来的列名。

接下来投影数据。

遍历 result（笛卡尔积之后得到的一行一行数据，格式：[[属性1, 属性2], [属性1, 属性2]）的每一行 row。

row 的格式是一个列表，包含每一行的数据。

用 r 存从 row 里投影完的一行。

遍历 properties_index，把该行下标在 properties_index 里的数据取出来 append 到 r 里。

最后把投影完的一行 r append 到 final_rows 里。

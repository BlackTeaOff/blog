---
title: "人工智能导论 - Lab1"
description: 
date: 2026-11-12T22:30:58+08:00
image: 
math: 
license: 
comments: true
draft: false
build:
    list: always    # Change to "never" to hide the page from the list
categories:
- "人工智能"
- "大学课程"
tags:
- "实验"
- "AI"
---

# 实验一

## 需要知道的知识

### 理论

#### 知识图谱的三元组表示

知识图谱（KG）是结构化知识库，最基本的知识单元是**三元组**。

```
(头实体 Head, 关系 Relation, 尾实体 Tail)
```

例如：（GPT，基于，Transformer）

实体是图中的**节点**，关系是连接节点的**有向边**。

#### 符号主义和连接主义

知识图谱属于**符号主义**，通过符号和逻辑网络来表示世界。

神经网络属于**连接主义**，通过连续向量空间于权重参数模拟人脑。

#### 知识图谱与大模型互补

大模型存在幻觉，黑盒等问题。

知识图谱有显式的事实锚点和可解释的推理链条；

反过来，大模型的文本理解能力也可以自动化从非结构化文本中抽取三元组构建知识图谱。

#### 有向图

神经网络 ->（属于） 连接主义。

但 连接主义 -> （属于）神经网络 是错的。

具有方向性。

用 NetworkX 中的 `nx.DiGraph` 表示有向图。

`nx.Graph` 表示无向图。

#### 出度和入度

- 出边：从当前实体出发指向的目标。
- 入边：指向当前实体的来源。

#### 有向路径的可达性陷阱

```
图灵 ->(提出) 图灵测试 ->(属于) 人工智能 <-(属于) 机器学习
```

虽然两边都连到了人工智能，但是由机器学习推不出图灵。

箭头不可以逆向走。

#### 实体重要性：度中心性

节点的总度数 = 入度 + 出度

拥有极高连接度的节点被称为枢纽实体。

### 代码相关

#### NetworkX 有向图

```python
import networkX as nx

# 有向图
G = nx.DiGraph()

# 添加一条带属性的有向边: 起点 u, 终点 v, 属性 key=value
# 最后一个参数可以穿任意多的，任意键名，任意数据类型的键值对
G.add_edge("GPT", "Transformer", relation="基于")

rel = G["GPT"]["Transformer"]["relation"] # 返回基于
```

整张图是一个分层的字典树。

```python
{
	"头节点 u": {
		"尾节点 v" : {
			"属性键": "属性值"
            "属性键": "属性值"
		}
        "尾节点 v2" : {
			"属性键": "属性值"
		}
	}
    "头节点 u2": {
		"尾节点 v" : {
			"属性键": "属性值"
            "属性键": "属性值"
		}
        "尾节点 v2" : {
			"属性键": "属性值"
		}
	}
}
```

最外层字典是整个图，里面的一层是所有头节点及其对应的信息（键值对，值是另一个字典）。

G["头节点 u"] 返回的就是他的所有出边邻居以及边属性。

再往里一层是这个头节点所连接的所有尾节点及其里面所有键值对（边属性字典）。

G\["头节点 u"][”尾节点 v“] 返回的就是这两个节点的边属性字典。

#### 邻居查询 API

```python
# 查从 node 出发指出去的所有节点 (出度邻居)
# 逻辑上和 G["Transformer"].keys() 一样
outs = list(G.successors("Transformer")) # ['2017', '深度学习']

# 查指向 node 的所有节点 (入度邻居)
inns = list(G.predecessors("Transformer")) # ['GPT', '大语言模型']
```

#### zip

`nx.shortest_path(G, "GPT", "机器学习")` 返回的是一个节点列表。

```python
nodes = ["GPT", "Transformer", "深度学习", "机器学习"]
```

要找到完整关系链，需要知道 GPT 和 Transformer 的关系，Transformer 和深度学习的关系....

N 个节点的路径，包含 N - 1 条边，需要把节点列表两两配对成 `("GPT", "Transformer")` `("Transformer", "深度学习")"` ......

zip() 能把两个可迭代对象按位置一一配对。

```python
names = ["张三", "李四", "王五"]
scores = [90, 85, 95]

print(list(zip(names, scores)))
# 输出：[('张三', 90), ('李四', 85), ('王五', 95)]
```

如果把 nodes 去掉最后一个元素（nodes[:-1]）得到  `nodes = ["GPT", "Transformer", "深度学习"]`

去掉第一个元素得到 `nodes = ["Transformer", "深度学习", "机器学习"]`

```python
nodes = ["GPT", "Transformer", "深度学习"]
nodes = ["Transformer", "深度学习", "机器学习"]
```

把这两个进行 zip 操作，就能把一个节点和后一个节点进行一一配对了。（因为错位了）

#### 列表推导式

```python
[想放进列表的东西 for 变量 in 数据源]
```

从数据源中取出每一个变量，用这个变量来决定想放进列表的东西。

```python
rels = [G[u][v]["relation"] for u, v in zip(nodes[:-1], nodes[1:])]
```

zip 得到 `("GPT", "Transformer")` `("Transformer", "深度学习")"` ......

分别把每组取出来放在 u，v 里，然后去图里查关系放在 rels 数组里。
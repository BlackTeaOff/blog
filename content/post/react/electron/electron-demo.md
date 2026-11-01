---
title: "Electron Demo"
description: 用来练习的 React - Electron 小项目(超小)
date: 2026-11-01T22:27:09+08:00
image: 
math: 
license: 
comments: true
draft: false
build:
    list: always    # Change to "never" to hide the page from the list
categories:
- "React"
tags:
- "Electron"
- "Tailwind CSS"
- "React"
---

# 前言

是时候学点框架了。

为了 cilicili 和 **我的未来**。

和 JavaEE。

> 问题是：还有**未来**吗？
>
> 倒是有**牛来**。
> 
> 这谁能绷住。
>
> 哈哈哈哈哈哈哈哈哈哈哈哈哈哈
> 
> emmmmmmmm......
>
> 好像也没那么好笑。
> 
> 私密马赛，正文**来了**。 


# Electron-demo

## 结构

### src/main

Node.js 后端

### src/renderer/src/

React（状态，点击事件，DOM 结构）在这里。

Tailwind CSS 样式在这里。

本质是浏览器视窗，把数据 “画” 在屏幕上。

### src/preload

负责暴露后端的 API。

前端（Renderer）不能直接和后端（Nodejs）通信，

他就是中间的桥梁。

## 语法

### tsx 与 ts

tsx 文件主要用于 React 框架的开发。

ts 主要用于**后端**逻辑。

核心区别是是否支持 **JSX 语法**（HTML 样式的标签），

ts 不支持 JSX，TSX 支持。


### React

#### useState

在 React 中，以 `use` 开头的函数被统称为 Hook（钩子）

```typescript
const [result, setResult] = useState<null | string>(null)
```

传入的参数 `null` 是**初始值**。

页面第一次加载时，result 的值就是 null。

组件再次刷新的时候，React 会记住修改的新值，忽略这个初始值。

<null | string> 是 TypeScript 里的**泛型**，代表里面的状态可能是 null 也可能是 string，**只允许**这两种类型。

它的返回值是包含两样东西的**数组**，[当前值，修改函数]

[result, setResult] = 返回的 [当前值，修改函数]

这种语法用的是**数组解构**赋值

等价于: 

```typescript
const hookResult = useState(null); // 返回一个数组
const result = hookResult[0];
const setResult = hookResult[1];
```

在 JavaScript 里，函数可以被作为**参数**传递。

setResult 这个函数**不需要**我实现，React 在底层写好了。

调用 **SetResult(新值)** 即可。

不能直接修改 result：

- 因为 React 不知道修改了数据，不会更新 UI
- React 更新界面的方式是把 App() 这个函数从头到尾跑一边，变量也会被重置 

#### 状态是什么？

在通用编程语言里，任何变量的值都可以叫状态。

在 React 里，状态是与**屏幕界面**绑定在一起的动态**数据**。

> 这个值本身叫做状态

UI = f(State)

界面的长相，是由当前的状态投影出来的。

流程：

```
你点击按钮
  ↓
执行 setResult('正面')
  ↓
1. React 内部保存新值 '正面'
2. React 发现状态变了，重新执行 App() 函数（Re-render）
    ↓
    App() 函数执行到 const [result, setResult] = useState(...)
    这一次 React 不会给它初始值 null，而是把刚才存的 '正面' 给 result
    ↓
    return 出来的 JSX 读到了新的 result
    ↓
    屏幕上的 ？ 变成了 正面！
```

### 箭头函数

```python
const flipCoin = () => {...}
```

() 参数列表：如果有参数，就写成（a, b）=> {...}

=> 胖箭头：把左边的参数输入，执行右边的逻辑

{...}：函数体

传统写法：

```python
function flipCoin() { }
```

> 用 function 和 const 声明的函数，都能传入别的函数或者返回给别的东西。


差异：

- function 会 **”自动提升“**，把函数放在最底下，在上方也可以调用
- const 必须**先声明后使用**。

### export default

用来导出某些代码（类似于包和命名空间）

- 默认导出（Default Export）：一个文件只能有**一个**默认导出，别的页面想导入这个直接写 

  `import App from './App'`

- 命名导出（Named Export）：一个文件能有**多个**命名导出，别人想导入必须加花括号`import { App } from './App'`

> 类似于一个 Java 文件能有多个类，但是只有一个 Public 类。 

项目的入口文件（main.tsx / index.tsx）里默认写好了 `import App from './App'`

运行会直接来找当前目录里的 App 文件，`./` 代表当前目录下的。

### return

给 React 框架返回一个网页蓝图（不是真正的网页，JSX - JavaScript XML）

### 要用到的 HTML 语法

- `<div>`：万能打包箱，想把某几个元素放在一起居中，用这个。
- `<span>`：适合包一小截字
- `<h1-h6>`：标题
- `<p>`：段落，用来放一段文本
- `<button>`：按钮
- `<input>`：输入框
- `<ui> + <li>`：列表

### Tailwind CSS

#### 颜色

- bg-indigo-600
- text-gray-800
- border-amber-400

公式：[作用部位] - [颜色名称] - [深浅程度]

#### Flex 弹性布局

- flex：开启弹性盒子
- flex-col：改成纵向排列；如果不写，默认是横向排列
- items-center：在交叉轴上居中（比如左右居中）
- justify-center：在主轴上居中（比如上下居中）
- gap-6：子元素之间的间距

#### 数字与间距

| 类名 | 实际像素大小 |            计算方法 |
| :--- | :----------: | ------------------: |
| p-1  |     4px      |             1 x 4px |
| p-2  |     8px      |             2 x 4px |
| p-4  |     16px     | 4 x 4px(常用内边距) |

##### 前缀

- `p`：Padding（内边距）/  `m`：Margin（外边距）
- `px-`：水平方向（左右两侧，X轴）
- `py-`：垂直方向（上下两册，Y轴）
- 单个方向：`pt-`，`pb-`，`pl-`，`pr`（上下左右）


#### 文字与形状

##### 文字

- 大小：`text-sm`（小字），`text-base`（正常），`text-x1`，`text-x2`（大标题）
- 粗细：`font-normal`，`font-medium`，`font-bold`

##### 圆角

- `rounded-lg`（大圆角），`rounded-xl`（超大圆角）
- `rounded-full`（正圆形）

##### 阴影

- `shadow`，`shadow-md`，`shadow-xl`

#### 交互状态

- hover:bg-indigo-700，鼠标指上去会变色
- active:scale-95，按下按钮缩小
- transition-all，平滑过渡以上动画


#### 占满整个屏幕

- min-h-screen：高度最小占满整个屏幕
- min-w-screen：宽度最小占满整个屏幕

---

> 我是分隔线（=_=）
>
> 上面基本都是 renderer 里的一些东西，下面来点后端的。

### preload/index.js

#### {}

`function / ()` 后面的 `{}`：函数体

HTML `<div>` 里的 `{}`：装变量，运算

`import { useState } from 'react'`：解构，只把 React 仓库里的一个工具挑出来

变量赋值给大括号 `const obj = {}`：实体数据对象（键值对）

#### const api

实体数据对象，存的是键值对。

键 - 函数名，值 - 函数。

```typescript
// 这是一个存“数据”的普通对象：
const person = {
  name: '小明',     // key 是 name，value 是字符串 '小明'
  age: 18          // key 是 age， value 是数字 18
}

// 这是一个存“函数”的对象（也就是我们的 api）：
const api = {
  saveFlip: (result: string) => { ... }  // key 是 saveFlip，value 是一个箭头函数
}
    
const api = {
  // 简写
  saveFlip(result: string) {
    ipcRenderer.send('coin-flipped', result)
  }
}
```

#### contextBridge.exposeInMainWorld('api', api)

第一个参数，决定了前端用什么名字去访问。

第二个参数，把哪个变量送过去。

#### window

在任何浏览器或网页环境里，只要代码跑在界面端，就会存在一个叫 window 的东西。

- window.document
- window.alert()
- window.setTimeout()
- window.console.log()，平时简写成 console.log

在 preload 里定义的 api 也挂在这里。
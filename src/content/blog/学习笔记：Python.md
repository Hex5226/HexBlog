---
title: 学习笔记：Python
description: 我个人学习python时的笔记
pubDate: 2026-08-31
categories: [学习笔记]
tags: [编程,编程语言,学习笔记]
draft: True
---
# 数据类型关键字
整形 int  
浮点 float  
布尔 bool  
字符串 str

# 列表(List)
增删改查

## 创建列表
四种方式

```python
a = [] # 空列表
a = [1, 2, 3] # 直接字面量

#把可迭代对象拆成元素
a = list("abc") # ['a', 'b', 'c']

a = [0] * 5 # [0,0,0,0,0] 重复乘法创建
```  

## 访问与切片
假设现有一列表 my_list = [1, 2, 3, 4, 5]
下标 （索引）从 0 开始
```python
a[0] # 1 第一个元素
a[-1] # 5 最后一个元素，负数从尾部数

# 切片
a[1:3] # [2, 3] 含头不含尾，从下标1切到2
a[:3] # [1, 2, 3] 从头切
a[3:] # 切到尾

a[:] # 复制一份

a[::2]
```


## 列表操作
末尾添加元素 append
```python
my_list = [1, 2, 3]

my_list.append("4")
# my_list = [1, 2, 3, 4]
```

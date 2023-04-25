---
title: 加强Floyd算法和找零问题贪婪算法求解Python代码实现
author: HWH
tags:
  - Python
  - Floyd
categories:
  - Algorithm
math: true
abbrlink: 19d8122e
date: 2022-12-03 19:31:49
---


## 加强Floyd算法，使得该算法能够求出最短路径本身，而不仅仅是它们的长度。

### 算法设计思想

- 所有路径都由两条路径构成： 一条从$v_i$到$v_k$的路径，路径中每个中间顶点的编号都不大于$k-1$; 一条从$v_k$到$v_j$的路径，路径中每个中间顶点的编号也都不大于$k-1$

- 设路径长度为 $d$ 得到以下递推式，$当k\geq 1, d_{ij}^{(0)}=w_{ij}时， d_{ij}^{(k)} = min\begin{Bmatrix} d_{ij}^{(k-1)}, d_{ik}^{(k-1)} + d_{kj}^{(k-1)} \end{Bmatrix}$

- 同理路径本身也可以得到与上面相同的公式

$$
E=mc^2
$$

### 具体步骤描述

- 初始化邻接矩阵

- 通过邻接矩阵来初始化路径矩阵

- $k$ 从 0 开始循环

- 遍历邻接矩阵，按照公式处理邻接矩阵和路径矩阵
- $当k\geq 1, d_{ij}^{(0)}=w_{ij}时， d_{ij}^{(k)} = min\begin{Bmatrix} d_{ij}^{(k-1)}, d_{ik}^{(k-1)} + d_{kj}^{(k-1)} \end{Bmatrix}$

- 将两个矩阵返回，即得到最短路径的长度以及最短路径本身


### 实现源码

```python
import numpy as np

# 初始化邻接矩阵
W = [
    [0, np.inf, 3, np.inf, np.inf],
    [2, 0, np.inf, np.inf, 5],
    [np.inf, 7, 0, 1, np.inf],
    [6, np.inf, np.inf, 0, 11],
    [np.inf, np.inf, np.inf, np.inf, np.inf],
]

S = []

# 初始化路径矩阵
for i in range(len(W)):
    S.append([chr(97 + i) for j in range(len(W))])


def Floyd(W, S):
    D = W
    for k in range(len(W)):
        for i in range(len(W)):
            for j in range(len(W)):
                if D[i][j] > D[i][k] + D[k][j]:
                    S[i][j] = S[i][k] + S[k][j]
                    D[i][j] = D[i][k] + D[k][j]
                # 给路径矩阵添上路径终点结点
                if k == len(W) - 1:
                    S[i][j] += S[j][0][0]
    return np.array(D), np.
array(S)


W, S = Floyd(W, S)
print("3190608027 黄文焕")
print(W)
print(S)
```

### 运行结果截图
![运行结果](https://img-blog.csdnimg.cn/b89c49a2d8924595baca8c6e90544bf9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)



## 为找零问题写一个贪婪算法的伪代码，它以金额n和硬币的面额d1>d2>...>dm作为输入。

### 算法设计思想

- 将 n 和 d1 相除，商为硬币个数，再 n 取余 d1 赋值给 n，循环下去，直到 dm

### 具体步骤描述

- 输入 n 以及从大到小排列的币值

- 循环处理 li 中的币值，n / li[index],n = n % li[index]

- 将结果保存在字典 d 中，

- 返回结果字典 d


### 实现源码

```python
print("3190608027 黄文焕")
n = int(input("请输入n: "))
li = list(map(int, input("请从大到小输入币值: ").split()))
d = dict()
index = 0

while index < len(li):
    m = int(n / li[index])
    d[li[index]] = m
    n = n % li[index]
    index += 1
print(d)
```

## 运行结果截图
![运行结果](https://img-blog.csdnimg.cn/00217dc58b3e4ebea0ffc6ae02b2731b.png)

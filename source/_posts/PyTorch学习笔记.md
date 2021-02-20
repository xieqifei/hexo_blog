---
title: PyTorch学习笔记
top: false
cover: false
toc: true
mathjax: true
date: 2020-06-16 16:14:08
password:
summary: PyTorch是一个开源的Python机器学习库，基于Torch，底层由C++实现，应用于人工智能领域，如自然语言处理。它最初由Facebook的人工智能研究团队开发，并且被用于Uber的概率编程软件Pyro。PyTorch主要有两大特征：类似于NumPy的张量计算，可使用GPU加速；基于带自动微分系统的深度神经网络。
tags: 
 - 深度学习
 - PyTorch
categories: 学习笔记
---

PyTorch的用处：GPU加速、求导、常用API

## P1：开发环境

> - Python 3.7+Anaconda 5.3.1
>
> - CUDA 10.0
> - Pycharm Community

1. 下载安装Anaconda，勾选“添加ana到路径环境”

cmd输入，查看ana安装的包。

```shell
conda list
```

2. 安装nvidia CUDA

确保有nvidia的GPU

配置系统环境变量，确保CUDA的bin目录在`PATH`行里，在cmd里

```shell
nvcc -v
```

3. pytorch.org里下载pytorch

4. 下载pycharm，这是一个python的IDE

配置编译器为Python

## P2：梯度下降算法

![](https://ae01.alicdn.com/kf/H33b92bc02a41476cb6c9d73410ecf1c4O.jpg)

求解线性方程，如果存在噪声？

求取近似值w,b。

![](https://ae01.alicdn.com/kf/H3b83f138e0734623a1f16fe56ecb2629s.jpg)

假设采样数据输入x和理论真实输出y满足 y=wx+b的线性回归函数。Linear Regression

那么
$$
loss\ function=\sum(wx+b-y)^2
$$

> 梯度下降算法：有若干个wb不同的函数y=wx+b，这些函数能够将采样输入x得出一个若干个输出值w1x+b1、w2x+b2等等，这个输出值wx+b与理论输出值y存在误差，于是我们定义了一个loss function来表示w和b不同时，wx+b与真实值y之间的误差。通过对loss function做运算能找到一组w和b，这组w和b可以使wx+b最接近y。
>
> 对loss function的运算方式叫梯度下降算法，将若干个采样输入和理论输出带入loss function，形成一个诸如L(w,b)的方程。要找使得L(w,b)最小的w和b。

## P3：梯度Ⅱ

对线性回归y=wx+b

计算loss function：

```python
def compute_error_for_line_given_points(b,w,points)：
	totalError=0
    for i in range(0,len(points)):
        x = points[i,0]
        y = points[i,1]
        totalError += (y-(w*x+b))**2
    return totalError / float(len(points))
```

输入上一个w和b值，计算梯度，返回下一个w和b：

```python
def step_gradient(b_current,w_current,points,learningRate):
    b_gradient = 0
    w_gradient = 0
    N = float(len(points))
    for i in range(0,len(points)):
        x = points[i,0]
        y = points[i,1]
        b_gradient += -(2/N) * (y - ((w_current*x) + b_current))
        w_gradient += -(2/N) * x * (y-((w_current*x)+b_current))
    new_b = b_current - (learningRate * b_gradient)
    new_w = w_current - (learningRate * w_gradient)
    return [new_b,new_w]
```

计算最优解：

```python
def gradient_descent_runner(points,starting_b,starting_w,learning_rate,num_iterations):
    b = starting_b
    w = starting_w
    for i in range(num_iterations):
        b,w = step_gradient(b,w,np.array(points),learning_rate)
    return [b,w]
```

## P4：手写数字问题

手写0~9，输出手写数字

## P5：Pytorch基本数据类型

Int: IntTensor of size()

float: FloatTensor of size()

Int array: IntTensor of size [d1,d2……]

> 没有String类型，只能用向量来表示[0,0,1,0……]

------

## P6：张量 Tensor

Tensor或者Numpy的ndarrays

```python
from __future__ import print_function
import torch
```

构造空矩阵,五行三列

```python
x = torch.empty(5,3)
print(x)
```

> tensor(1.00000e-04 *
>        [[-0.0000,  0.0000,  1.5135],
>         [ 0.0000,  0.0000,  0.0000],
>         [ 0.0000,  0.0000,  0.0000],
>         [ 0.0000,  0.0000,  0.0000],
>         [ 0.0000,  0.0000,  0.0000]])

构造一个随机数矩阵，随机数都在0到1之间

```python
x = torch.rand(5, 3) 
print(x)
```

> tensor([[ 0.6291,  0.2581,  0.6414],
>         [ 0.9739,  0.8243,  0.2276],
>         [ 0.4184,  0.1815,  0.5131],
>         [ 0.5533,  0.5440,  0.0718],
>         [ 0.2908,  0.1850,  0.5297]])

构造一个全0矩阵，字符类型为long。

```python
x = torch.zeros(5, 3, dtype=torch.long)
print(x)
```

> tensor([[ 0,  0,  0],
>         [ 0,  0,  0],
>         [ 0,  0,  0],
>         [ 0,  0,  0],
>         [ 0,  0,  0]])

直接输入数据创建一个张量。

```python
x = torch.tensor([5.5, 3])
print(x)
```

> tensor([ 5.5000,  3.0000])

创建一个张量，基于已存在的张量

```python
x = x.new_ones(5, 3, dtype=torch.double)    

# new_* methods take in sizes
print(x)

x = torch.randn_like(x, dtype=torch.float)  

# override dtype!
print(x)                                    

# result has the same size
```

> tensor([[ 1.,  1.,  1.],
>         [ 1.,  1.,  1.],
>         [ 1.,  1.,  1.],
>         [ 1.,  1.,  1.],
>         [ 1.,  1.,  1.]], dtype=torch.float64)
> tensor([[-0.2183,  0.4477, -0.4053],
>         [ 1.7353, -0.0048,  1.2177],
>         [-1.1111,  1.0878,  0.9722],
>         [-0.7771, -0.2174,  0.0412],
>         [-2.1750,  1.3609, -0.3322]])

获取维度

```python
print(x.size())
```

> torch.Size([5, 3])
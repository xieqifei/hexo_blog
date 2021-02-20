---
title: Python数据分析
top: false
cover: false
toc: true
mathjax: false
date: 2020-09-29 17:56:45
password:
summary:
tags:
- Matplotlib
- Numpy
- Pandas
categories: Python
---



# 1：综述

**为什么学习数据分析**

1. 岗位需求
2. python数据科学的基础
3. 机器学习的课程基础

**什么是数据分析**

用适当的方法对收集的大量数据进行分析

帮助人们做出判断，以便采取适当的行动

**jupyter notebook：**

Python笔记软件

# 2：Matplotlib

1. 将数据**可视化**，更**直观**的呈现

2. 使数据更加**客观**、更具**说服力**

最流行的python底层绘图库，主要做数据可视化，模仿matlab构建

![](https://i.loli.net/2020/09/30/LdSCfpJHt2ux7yM.png)

```python
from matplotlib import pyplot as plt	#导入pyplot

x = range(2,26,2)	#数据在x轴的位置，是一个可迭代的对
y = [15,13,14,17,20,25,26,26,25,22,18,15]	#数据在y轴的位置，是一个可迭代的对象
plt.plot(x,y)	#传入x和y，通过plot绘制出折线图
plt.show()	#执行程序的时候展现图像
```

**目前存在的问题：**

1. 图片大小
2. 保存到本地
3. 描述信息，比如x，y轴表示什么
4. 刻度
5. 线条样式
6. 标记特殊点

## 2.1 设置图片大小-figure

```python
fig = plt.figure(figsize=(20,8),dpi=80)
#figure图形图标的意思，这里指画的图
#通过实例化一个figure并且传入参数
#传入像素值，dpi，让图片更清晰
plt.plot(x,y)
plt.savefig("./t1.png")	#保存图片
#可以保存为svg矢量图格式，放大不会失真
```

## 2.2 设置x，y轴步长-xticks/yticks

```python
plt.xticks(x)	#设置步长
plt.xticks(range(2,25))
_xticks_labels = [i/2 for i in range(4,49)]
plt.xticks(_xticks_labels[::3])

plt.yticks(range(min(y),max(y)+1))

#显示字符串
_x = list(x)[::3]
_xticks_labels = ["10点{}分".format(i) for i in range(60)]
_xticks_labels += ["11点{}分".format(i) for i in range(60)]
plt.xticks(list(_x)[::3],_xticks_labels[::3],rotation=90)	#,前为步长列表，，后为字符串列表，与步长列表一一对应，并把刻度旋转90度显示。
```

**上述绘图，无法显示中文**

## 2.3 加入中文字体支持-FontProperties

方法1：

```python
import matplotlib
font = {'family':'MicroSoft YaHei',
       'weight':'bold',
       'size':'larger'}
matplotlib.rc("font",**font)	#**可以把字典转为xx=xx格式
matplotlib.rc("font",family='MicroSoft YaHei',weight='bold')
```

方法2：

```python
from matplotlib import font_manager
my_font = font_manager.FontProperties(fname="/System/Library/Fonts/PingFang.ttc")	#设定字体位置
#查找系统字体存放位置
plt.xticks(list(_x)[::3],_xticks_labels[::3],rotation=90,fontproperties=my_font)
#使用字体
```

## 2.4 加入描述信息-xlabel/ylabel/title

```python
plt.xlabel("时间",fontproperties=my_font)
plt.ylabel("温度 单位（℃）",fontproperties=my_font)
plt.title("10点到12点的请问变化",fontproperties=my_font)
```

## 2.5 设置网格-grid

```python
plt.grid()
plt.grid(alpha=0.4)	#设置网格透明度，1不透明，0透明
```

## 2.6 绘制两个图形-legend

```python
plt.plot(x,y1,label="自己",color="orange",linestyle=":")
plt.plot(x,y2,label="同桌",color="r",linestyle="--")

#添加图例，解释哪个线表示哪个数据集，显示中文，格式不再是FontProperties
#prop字体，loc位置：0最好的位置，不设置参数则默认0
plt.legend(prop=my_font,loc=0)

```

> color='r'	颜色
>
> linestyle='--'	线条风格：-实线；--虚线；-.点划线；:点虚线；留空或空格，表示无线条
>
> linewidth=5	线粗细
>
> alpha=0.4	透明度

## 2.7 绘制散点图-scatter

```python
plt.scatter(x, y)
plt.show()
```

## 2.8 绘制柱状图-bar

```python
#绘制电影与其对应票房
a=["战狼2","速度与激情8","功夫瑜伽","西游附魔篇"]
b=[56,61,26,94]
plt.figure(figsize=(20,15),dpi=80)
#绘制柱状图
plt.bar(range(a),b,width=0.3)
#设置横轴字符串
plt.xticks(range(a),a,rotation=45)
plt.show()
```

## 2.9 绘制条形图-barh

```python
plt.barh(range(len(a)),b,height=0.3,color='orange')
plt.yticks(range(len(a)),a)
```

![](https://i.loli.net/2020/10/01/EFdtgolApGN7qPa.png)

## 2.10 绘制直方图-hist

有250部电影的时长，统计时长分布的数量，比如100到120分钟的电影数。

组数=极差/组距。

```python
a = [120,76,124,161……]	#250部电影的时长。
#计算组数
theory_d = 3	#假定组距
num_bins = (max(a)-min(a))//d	#计算组数

plt.figure(figsize = (20,8),dpi=80)
plt.hist(a,num_bins)	#频数分布直方图
plt.hist(a,num_bins,normed=True)	#频率分布直方图
#设置x刻度
plt.xticks(range(min(a),max(a)+d,d))
plt.grid()
plt.show()
```

> 当（最大值-最小值）/假定组距 无法除尽时，根据假定组距绘制的x刻度会偏移。

2.11 绘制其他图形

https://matplotlib.org/gallery/index.html

前端画图库js——echarts：

https://echarts.apache.org/examples/zh/index.html

图表代码托管——Plotly:
https://plot.ly/python

# 2：Numpy

**什么时numpy？**

一个再python中做科学计算的基础库，重在数值计算，也是大部分python科学计算库的基础库，多用于在大型、多维数组上执行数值计算。

## 2.1 创建数组：

```python
import numpy as np
a = np.array([1,2,3])
b = np.array(range(10))
c = np.arange(10)	#作用与b的表达式相同

print(a)
print(type(a))

```

> 结果：
>
> [1 2 3]
>
> <class 'nmpy.ndarray'>

## 2.2 数据类型

```python
d = np.array(range(1,4),dtype="float32")	#dtype指定数组中数据的数据类型
e = np.array([1,1,0,0,0,1],dtype=bool)
print(e.dtype)	#打印数组中数据的数据类型

#调整数据类型
f = d.astype("int8")
```

修改浮点型的小数位数

```
a = np.array(random.random() for i in range(10))
np.round(a,2)
```

## 2.3 数组形状

```python
t1 = np.array([[1,2,3],[3,4,5]])
t1.shape	
#out:(2,3)
```

**数组的形状**

```python
t2 = np.arange(12)	#新建一维数组
t3 = t2.reshape(3,4)	#变为二维的
t4 = t2.reshape(24,)	#变一维数组
t5 = t2.flatten()	#变一维数组
```

## 2.4 数组计算

**数组和数**

```python
t6 = np.arange(24)
t7 = t6.reshape(4,6)
t7+2	#数组中每个数都加2
t7*2	#每个数都乘2
t7/2	#每个数都除2，会得到浮点数
```

**数组和数组**

形状相同：(4,6)与(4,6)

```python
t8 = np.arange(100,124).reshape(4,6)
t7+t8	#形状相同，行列相同的数值进行运算
t7*t8
```

(4,6)与(,6)

![](https://i.loli.net/2020/10/03/FXmBQap341rgTOf.png)

(4,6)与(4,1)

![](https://i.loli.net/2020/10/03/7iFszdVEr3KNZYf.png)

**广播原则：**

如果两个数组的后缘维度，即从末尾开始算起的维度的轴长度相符或其中一方的长度为1，则认为他们是广播兼容的。广播会在缺失和或长度为1的维度上进行

## 2.5 读取数据

**轴**

一维数组只有一个轴，即0轴

二维数组有两个轴，0和1轴。(2,5)，2是0轴长度，5是1轴长度

三维数组有三个轴，0，1，2轴

**读取数据**

```python
np.loadtxt(frame,dtype=np.float,delimiter=None,skiprows=0,usecols=None,unpack=False)
```

frame：文件、字符串或产生器

dtype：数据类型

dlimiter：分割字符串，默认是空格

skiprows：跳过前x行

usecols：读取指定的列

unpack：True表示，读入属性将分别写入不同的数组变量，False 读入数据只写入一个数组变量，默认False。**转置效果**。

读取的文件内容格式：

```
1241,1241,15346,2451
125415,235,4135,1351
12516,15326,1361,616
……
```

读取时可以按`,`分割。按行划分

## 2.6 矩阵转置

```python
t2.transpose()
t2.T
t2.swapaxes(1,0)	#交换轴，也能实现转置效果
```

## 2.7 索引和切片

取第二行：

```python
t2[2]
```

取连续的多行：

```python
t2[2:]
```

取不连续的多行：

```python
t2[[2,8,10]]
```

取第一列：

```python
t2[:,0]
```

取连续多列：

```python
t2[:,2:]
```

取不连续的多列：

```python
t2[:,[0,2,5]]
```

取3行四列的值：

```python
t2[3,4]
```

取多行多列：3行到5行，2列到4列，因为`:`左右是左闭右开区间，行数为索引+1

```python
t2[2:5,1:4]
```

取多个不连续的点：(0,0)  (2,1)  (5,5)

```
t2[[0,2,5],[0,1,5]]
```

## 2.8 修改数值

```python
t[:,2:4] = 0
```

**布尔索引**

```python
t2<10	#相同的矩阵，矩阵值为布尔类型，对应位置满足值小于10，则为1，否则为0
t2[t2<10] = 3	#t2中小于10的位置，赋值为3
t2[t2<10]	#取t2中小于10的数组成新矩阵
```

**三元运算符**

```python
np.where(t<10,0,10)	#如果小于10，替换为0，否则替换为10
```

**裁剪**

```python
t.clip(10,18)	#小于10的替换为10，大于18的替换为18
```

```python
t2 = t2.astype(float)
t2[3,3] = np.nan
```

## 2.9 拼接数组

```python
np.vstack(t1,t2)	#竖直拼接
np.hstack(t1,t2)	#水平拼接
```

![](https://i.loli.net/2020/10/11/XpmuQc9dIC8jEws.png)

## 2.10 行列交换

行交换：

```python
t[[1,2],:] = t[[2,1],:]
```

列交换：

```python
t[:,[0,2]] = t[:,[2,0]]
```

## 2.11 其他方法

```python
#获取最大最小值
np.argmax(t,axis=0)
np.argmin(t,axis=1)
#创建一个全0的数组
np.zeros((3,4))
#创建全1数组
np.ones((3,4))
#创建一个对角线为1的正方形数组（方阵）
np.eye(3)
```

## 2.12 生成随机数

```
np.random.rand(3,4)	#均匀分布
np.random.randn(3,4)	#标准正太分布的随机数，平均数为0，标准差为1
np.random.randint(low,high,(shape))	#产生[low,high)之间的数
```

种子：

```python
np.random.seed(10)
t = np.random.randint(0,20,(3,4))	#每次随机的结果相同
```

## 2.13 copy和view

1. a=b完全不复制，a和b相互影响。
2. a = b[:]，视图操作，创建新对象a，但a的数据完全由b保管，两者数据变化一致。
3. a = b.copy()复制，a和b互不影响。

## 2.14 nan和inf

nan(NAN,Nan)：not a number表示不是一个数字

inf(-inf,inf)：infinity，正无穷，-inf表示负无穷

一个数字除以0，python中会报错，numpy中是一个inf

**nan：**

1. 两个nan不相等

```python
np.nan !=np.nan
#True
```

2. 计算nan个数

```python
np.count_nonzero(t2!=t2)	#t2!=t2，得到新矩阵，每个位置是bool类型，true表示该位置是nan
np.count_nonzero(np.isnan(t2))	#
```

3. 计算矩阵和

```python
np.sum(t3)
np.sum(t3, axis=0)	#数组元素个数，与列数相同。，计算对应列上的和
```

## 2.15 常用统计函数

求和：t.sum(axis=0)

均值：t.mean(axis=0)

中值：t.median(axis)

最大值：t.max

最小值:t.min

极值：np.ptp(t,axis=None)	最大值和最小值之差

标准差：t.std(axis=None)

**默认返回多维数组的全部统计结果**
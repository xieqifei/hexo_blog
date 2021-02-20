---
title: Pandas知识总结
top: false
cover: false
toc: true
mathjax: false
date: 2020-10-14 18:45:54
password:
summary:
tags: 
- Pandas
- 数据处理
categories: 学习笔记
---

# 1：简介

在我看来，Pandas是一款数据处理工具，它相比于numpy而言，更加具有针对性。比如说，Pandas提供了两种数据结构，Series和DataFrame，分别表示一维数据和二维数据，而numpy则还有更多维数据。Pandas的特色是，它可以使用自定义的索引来表示数据的特点，这和Excel表格很类似。而numpy只有固定的整数索引。

在数据中，我们可能会有这种情况，一个二维表格，每一行，表示不同的学生，每一列表示学生的不同信息。如下表所示，

|      | 年龄 | 班级 |
| ---- | ---- | ---- |
| 小明 | 19   | 4班  |
| 小张 | 15   | 2班  |
| 小华 | 17   | 1班  |

由年龄和班级构成的二维数组，在numpy中是无法建立的，因为numpy无法建立班级的字符串格式数据，且数组内不能含有多种属性的数据，比如年龄的数据格式是int，而班级则是string。

此时Pandas的作用就出来了。Pandas可以指定上述数据每一行的意义为名字，所以其行索引`Index`可以设置为['小明','小张','小华']，而其纵坐标的索引`columns`设置为['年龄','班级']。然后再向这个DataFrame中添加数据即可。

# 2：入门

## 2.1 生成对象

引入Pandas：

```python 
import numpy as np
import pandas as pd
```

生成Series：

```python
s = pd.Series([1,4,1,np.nan,8])
```

生成DataFrame：

```python
dates = pd.date_range('20130101',periods=6)
#生成六个以20130101开始，间隔为一天的时间序列
df = pd.DataFrame(np.random.randn(6,4), index=dates, columns=list('ABCD'))
```

> date_range函数可以生成一个时间序列，参数有，开始时间，结束时间，序列个数，间隔单位等。

df为：

```
                   A         B         C         D
2013-01-01  0.469112 -0.282863 -1.509059 -1.135632
2013-01-02  1.212112 -0.173215  0.119209 -1.044236
2013-01-03 -0.861849 -2.104569 -0.494929  1.071804
2013-01-04  0.721555 -0.706771 -1.039575  0.271860
2013-01-05 -0.424972  0.567020  0.276232 -1.087401
2013-01-06 -0.673690  0.113648 -1.478427  0.524988
```

使用字典对象生成DataFrame：

```python
df2 = pd.DataFrame({'A':1,
                    'B':pd.Timestamp('20130102'), 
                    'C':pd.Series(1, index=list(range(4)),dtype='float332'),
                    'D':np.array([3]*4,dtype='int32'),
                    'E':pd.Categorical(["test","train","test","train"]),
                    'F':'foo'})
```

## 2.2 查看数据

查看DataFrame头部和尾部数据：

```python
df.head()
df.tail(3)
#可指定查看头尾部的数据行数
```

显示索引和列名：

```python
df.index
df.columns
```

`df.to_numpy()`将DataFrame数据转为numpy数据

`df.describe()`查看数据统计摘要

`df.T`转置

`df.sort_index(axis=1,ascending=False)`按轴上升排序

`df.sort_values(by='B')`按轴排序

## 2.3 选择数据

产生`Series`

```python
df['A']
df.A
```

切片：

```python
df[0:3]
#选择1到3行
```

```python
df['20130102':'20130104']
```

**按标签选择**：

```python
df.loc[dates[0]]
```

用标签选多列：

```python
df.loc[:,['A','B']]
```

用标签切片：

```python
df.loc['20130102':'20130104',['A','B']]
```

提取标量：

```python
df.loc[dates[0],'A']
df.at[dates[0],'A']
```

**按位置选**：

```python
df.iloc[3]
```

用整数切片：

```python
df.iloc[3:5,0:2]
```

用整数列表按位置切片：

```python
df.iloc[[1,2,4],[0,2]]
```

按位置访问标量：

```python
df.iloc[1,1]
df.iat[1,1]
```

**布尔索引**：

用单列的值：

```python
df[df.A>0]
```

```python
Out[39]: 
                   A         B         C         D
2013-01-01  0.469112 -0.282863 -1.509059 -1.135632
2013-01-02  1.212112 -0.173215  0.119209 -1.044236
2013-01-04  0.721555 -0.706771 -1.039575  0.271860
```

选择满足条件的值：

```python
df[df>0]
```

```python
Out[40]: 
                   A         B         C         D
2013-01-01  0.469112       NaN       NaN       NaN
2013-01-02  1.212112       NaN  0.119209       NaN
2013-01-03       NaN       NaN       NaN  1.071804
2013-01-04  0.721555       NaN       NaN  0.271860
2013-01-05       NaN  0.567020  0.276232       NaN
2013-01-06       NaN  0.113648       NaN  0.524988
```

`isin()`筛选

```python
df2 = df.copy()
df2['E'] = ['one','two','three'……]
df2[df2['E'].isin(['one','two'])]
```

## 2.4 缺失值Nan

`reindex`重建索引，不会修改源数据

```python
df1 = df.reindex(index = dates[0:4],columns=list(df.columns)+['E'])
df1.loc[dates[0]:dates[1],'E'] = 1
```

```python
Out[57]: 
                   A         B         C  D    F    E
2013-01-01  0.000000  0.000000 -1.509059  5  NaN  1.0
2013-01-02  1.212112 -0.173215  0.119209  5  1.0  1.0
2013-01-03 -0.861849 -2.104569 -0.494929  5  2.0  NaN
2013-01-04  0.721555 -0.706771 -1.039575  5  3.0  NaN
```

删除含缺失值的行：

```python
df1.dropna(how='any')
```

填充缺失值：

```python
df1.fillna(value=5)
```

提取缺失值的布尔掩码：

```python
pd.isna(df1)
```

## 2.5 运算

一般，运算时会排除缺失值

在列上计算**平均值**：

```python
df.mean()
```

在行上计算：

```python
df.mean(1)
```

```python
Out[62]: 
2013-01-01    0.872735
2013-01-02    1.431621
2013-01-03    0.707731
2013-01-04    1.395042
2013-01-05    1.883656
2013-01-06    1.592306
Freq: D, dtype: float64
```

**减法：**

```python
s = pd.Series([1,3,5,np.nan,6,8], index=dates).shift(2)
df.sub(s,axis='index')
```

s与df的index轴是相同的，或者说在行上他们数据的个数相同，所以，sub第二个参数填写index或者0，计算时，df每列都会减去s。

`shift`函数是平移函数，s各个位置上的数向下平移，由于向下平移了两个位置，0和1位置上的数没有内容可以补，用nan自动替补。

**Apply函数**：

`DataFrame.apply()` 函数则会依次将每行或列取出来作为一个Series传递到apply指定的函数中。

```python
matrix = [
    [1,2,3],
    [4,5,6],
    [7,8,9]
]

df = pd.DataFrame(matrix, columns=list('xyz'), index=list('abc'))
df.apply(np.square)
```

具体使用方法：

[pandas apply() 函数用法](https://www.jianshu.com/p/4fdd6eee1b06)

**直方图与离散化：**

```python
s = pd.Series(np.random.randint(0,7,size=10))
s.value_counts()	#统计s中整数的分布情况，比如4有5个
```

**字符串方法：**

```python
 s = pd.Series(['A', 'B', 'C', 'Aaba', 'Baca', np.nan, 'CABA', 'dog', 'cat'])
 s.str.lower()	#字符串小写
```

## 2.6 合并

**结合：**

`contact()`将零散的df合并，合并原则根据index和columns，相同合并，不同连接。

```python
df = pd.DataFrame(np.random.randn(10, 4))
pieces = [df[:3], df[3:7], df[7:]]
pd.concat(pieces)
```

连接：

`join()`直接将连个df连接起来。

```python
In [79]: left
Out[79]: 
   key  lval
0  foo     1
1  foo     2

In [80]: right
Out[80]: 
   key  rval
0  foo     4
1  foo     5

In [81]: pd.merge(left, right, on='key')
Out[81]: 
   key  lval  rval
0  foo     1     4
1  foo     1     5
2  foo     2     4
3  foo     2     5
```

```python
In [84]: left
Out[84]: 
   key  lval
0  foo     1
1  bar     2

In [85]: right
Out[85]: 
   key  rval
0  foo     4
1  bar     5

In [86]: pd.merge(left, right, on='key')
Out[86]: 
   key  lval  rval
0  foo     1     4
1  bar     2     5
```

**追加：**

`append()`为df追加行

```python
df.append(s,ignore_index=True)
```

## 2.7 分组

按照某个column或columns，将df分组，然后结合df的函数分别计算各个分组。

```python
In [92]: df
Out[92]: 
     A      B         C         D
0  foo    one -1.202872 -0.055224
1  bar    one -1.814470  2.395985
2  foo    two  1.018601  1.552825
3  bar  three -0.595447  0.166599
4  foo    two  1.395433  0.047609
5  bar    two -0.392670 -0.136473
6  foo    one  0.007207 -0.561757
7  foo  three  1.928123 -1.623033

In [93]: df.groupby('A').sum()
Out[93]: 
            C        D
A                     
bar -2.802588  2.42611
foo  3.146492 -0.63958

In [94]: df.groupby(['A', 'B']).sum()
Out[94]: 
                  C         D
A   B                        
bar one   -1.814470  2.395985
    three -0.595447  0.166599
    two   -0.392670 -0.136473
foo one   -1.195665 -0.616981
    three  1.928123 -1.623033
    two    2.414034  1.600434
```

## 2.8 重塑
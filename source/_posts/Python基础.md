---

title: Python基础
top: false
cover: false
toc: true
mathjax: false
date: 2020-07-22 13:51:02
password:
summary: 在网络编程中，Python和爬虫是非常有用的辅助工具。许多网站后端需要用到的工具都基于Python编写，而许多数据的采集发布，也离不开Python，Python也是目前爬虫程序使用最多的机器语言。之前在学习机器学习，Python作为其不可或缺的学习部分，由于我的知识缺乏，使之变得特别困难。因此，我希望通过爬虫学习，重新系统地学习Python，同时巩固并亲手实践编写爬虫程序。本文是Python基础，爬虫的准备工作。
tags: 
 - Python
categories: 学习笔记
---

# 1: Hello World

windows `win`+`R`打开运行菜单，输入`CMD`，进入终端。输入`python`进入python终端程序。输入`print("hello,world")`打印文字。

`import this`打印python之禅

结束命令提示符：

`exit()`

`ctrl`+`z`

> 命令提示符中的每一行是相关联的。

执行以`.py`结尾文件。

命令提示符中输入`python hello.world.py`

# 2: 基础

## 2.1 注释

`#注释`：单行注释

`***注释***`：多行注释 

## 2.2 标准化输出 

```python
#常规输出
print("标准化输出字符串")
a = 10
#字符连接变量
print("这是变量", a)
#变量占位
print("我的年纪是：%d 岁"%a)
#多变量占位
print("我的名字是%s，我的国籍是%s"%("小张","中国"))
#多个字符串，空格隔开
print("aaa","bbb","ccc")
#多个字符串，指定.隔开
print("www","baidu","com",sep=".")
#无字符串结尾
print("hello",end="")
#以空格结尾
print("world",end="\t")
#以回车符结尾
print("python",end="\n")
```

输出：

> 标准化输出字符串
> 这是变量 10
> 我的年纪是：10 岁
> 我的名字是小张，我的国籍是中国
> aaa bbb ccc
> www.baidu.com
> helloworld      python

占位符：

![image-20200722151300301](https://i.loli.net/2020/07/22/57QSZPcBOEqXw2m.png)

## 2.3 标准化输入

```python
password = input("请输入密码")
print("您刚才输入的密码是",password)
#测试变量类型
print(type(password))
```

输出：

> 请输入密码123
> 您刚才输入的密码是 123
> <class 'str'>

**即使输入数字，input也是字符串**

如果想要输入整型数。使用强制类型转换

```python
a = int("123")
print(type(a))
print("输出的是整型%d"%a)
```

## 2.4 运算符

![image-20200722153157538](https://i.loli.net/2020/07/22/IPZJTEy8KjevLF4.png)

![image-20200722153241400](https://i.loli.net/2020/07/22/xa3uNCHrgq5fwsh.png)

![image-20200722153320987](https://i.loli.net/2020/07/22/MQloegHN3BLOYsK.png)

# 3: 判断和循环语句

## 3.1 判断语句：

```python
if conditino1:
	codes
elif condition2:
	codes
else:
	codes
```

:warning:**缩进**、**冒号**很重要

## 3.2 随机生成数：

```python
import random
#随机生成[0,2]之间的整数，0、1、2
x = random.randint(0,2) 
```

## 3.3 导入模块和函数

导入整个模块：`import module`

从某个模块中导入某个函数：`from module import function`

从某个模块导入多个函数：`from module import function1, function2`

将某个模块的函数全部导入：`from module import *`

## 3.4 循环

### 3.4.1 for循环

```python
#[0,4]
for i in range(5):
	print(i)

#步长为3，[0,10]
for i in range(0,10,3):
    print(i) 

#打印每个字符 
name = "chengdu"
for x in name:
    print(x,end="\t")

#打印列表
a = ["aa","bb","cc"]
for i in range(len(a)):
	print(i,a[i])
```

### 3.4.2 while循环

```python
i=0
while i<5 :
	print(i)
	i += 1
else:
	print("while结束")
```

### 3.4.3 break和continue

break：结束所有循环；

continue：结束本次循环，进入下次循环。

pass：占位，什么也不执行，但pass后的同级语句会执行，和空行没区别。

# 4: 核心数据类型

## 4.1 字符串

可使用：单引号、双引号和三引号（三个单或双引号），使用反斜杠`\`转义

### 4.1.1 字符串作为数组

```python
str = "chengdu"
print(str)
print(str[0])
print(str[5:])      #从索引5到最后
print(str[0:5:2])   #[起始位置:结束位置:步进值]
```

> chengdu
>
> c
>
> du
>
> ceg

### 4.1.2 字符串连接

```python
print(str+",nihao")   #chengdu,nihao
print(str*3)   #连续打印三次
print("hello\nchengdu")    #\n转义为换行
print(r"hello\nchengdu")     #转义字符不起作用，直接显示
```

### 4.1.3 字符串操作函数

`bytes.decode`字符串解码

`isalnum()`包含至少一个字母或数字 

`isalpha()`至少一个且全为字母

`isdigit()`至少一个且全为数字

`join(seq)`指定字符串做分隔符，合并新的字符串

`split()`拆分字符串

`lstrip()`截掉字符串左边的空格或指定字符

`rstrip()`截掉右边

## 4.2 列表

### 4.2.1 定义列表

```python
list = []
namelist = ["zhang","wang",3]   #列表中可以存储混合类型
```

### 4.2.2 打印

```python
print(namelist[0])
print(namelist[1])
print(namelist[2])
```

### 4.2.3 遍历列表

for:

```python
for name in namelist:
	print(name)
print(len(namelist))   #len()获取列表长度
```

while:

```python
i = 0
while i<len(namelist):
	print(namelist[i])
	i++
```

### 4.2.4 列表操作函数

`[ : : ]`列表切片

`append`新增数据到列表尾部

```python
namelist.append("qian")

a=[1,2]
b=[3,4]
a.append(b)
#输出[1,2,[3,4]]   将列表作为一个元素加入到a中

a.extend(b)    #将b中的每个元素逐一追加到a中
#输出[1,2,[3,4],3,4]
```

`insert`指定下标位置插入元素，

```python
a = [0,1,2]
a.insert(1,3)	#第一个表示索引下标，第二个表示插入的元素（对象）
#输出[0,3,1,2]
```

`del list[index]` 在指定位置删除元素

`list.pop()`弹出末尾最后一个元素

`list.remove("钢铁侠")`直接删除第一个指定内容的元素，还有的同名元素不会被删除。

```python
movieName = ["加勒比海盗","复仇者","钢铁侠","蜘蛛侠"]
del movieName[2]	#删除钢铁侠
```

`in`某元素是否在列表中

`not in`不在列表中

```python
name = "wang"
if name in namelist:
	print("找到名字wang")
else:
	print("没找到")
```

`index`查找指定下标范围的元素，返回元素在列表中的索引下标，范围区间，左闭右开

```python
a = ["a","b","c","d","e"]
a.index("c",1,4)   #范围是[1,4)
#输出2
#如果没找到会报错
```

`count()`查找有多少个元素

```python
a.count("c")   
#输出1
```

`reverse()`将列表元素从后向前反转

```python
a = [1,4,2,3]
a.reverce()
#输出[3,2,4,1]
```

`sort()`排序

```python
a.sort()
#输出[1,2,3,4]
a.sort(reverse=True)
#输出[4,3,2,1]
```

## 4.3 元组

tuple(元组):与list相似，但tuple元素不能修改。

元组的元素不能改，但包含的对象可以修改。

```python
tup1 = ()	#创建空元组，带引成tuple类型
tup2 = (58)	#会打印成整型
tup3 = (58,)	#打印成tuple类型
```

元组寻址：

```python
tup1 = ("abc","def",1421)
print(tup1[0])
print(tup1[-1])	#倒数第一个
```

元组不能修改：

~~tup[0] = 1;~~

元组增：

```python
tup1 = (12,34,56)
tup2 = ("abc",xyz)
tup3 = tup1 + tup2
```

元组删：

```python
del tup
```

## 4.4 字典

无序的对象集合，使用键值储存。

```python
info = {"name":"吴彦祖","age":18}
print(info["name"])
```

访问可能不存在的键：

```python
print(info["gender"])
#报错
print(info.get("gender"))
#输出None
print(info,get("gender","m"))
#没找到默认为m，找到时显示找到的值
```

字典增：

```python
info["id"] = 12
```

字典删：

```python
del info["name"]	#删除键值对。直接访问键会报错。
info.clear	#清空数组，打印：info = {}
```

字典改：

```python
info["name"] = "成龙"
```

字典查：

```python
info.keys()		#得到所有的键，列表形式
info.values()	#得到所有的值
info.items()	#得到所有的项

#遍历
for key in info.keys():
	print(key)
for values in info.values():
    print(value)
for key,value in info.items():
    print("key=$s,value=%s"%(key,value))

mylist = ["a","b","c"]
#enumerate枚举类型对象
for i,x in enumerate(mylist):
    print(i,x)
```

## 4.5 集合

与字典相似，是key的集合，但是key不能重复。

set可以用来去重。



小结：

![image-20200726090258027](https://i.loli.net/2020/07/26/Zs3cgdvoN8UrWqQ.png)

# 5: 函数

```python
def printInfo():
    print("---------------------------")
    print("     人生苦短，我用python    ")
    print("---------------------------")
printInfo()
```

带参数的函数：

```python
def add2Num(a,b):
	c = a + b
    print(c)
add2Num(11,22)
```

带返回值的函数：

```python
def add2Num(a,b):
	return a+b
result = add2Num(1,2)
print(result)
```

返回多个值：

```python
def divid(a,b):
	shang = a/b
    yushu = a%b
    return shang,yushu
sh,yu = divid(5,2)
print(sh,yu)
```

> 函数类，局部变量优先于全局变量

在函数中使用全局变量：

```python
a = 100
def test1():
	global a	#全局变量标识符
    print(a)
    a = 200
print(a)
```

# 6: 文件操作

## 6.1 打开关闭

```python
f = open("test.txt","w")	#打开文件，写模式，不存在文件时，在函数目录路径新建这个文件。
f.close()	#关闭文件
```

| 模式 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| t    | 文本模式 (默认)。                                            |
| x    | 写模式，新建一个文件，如果该文件已存在则会报错。             |
| b    | 二进制模式。                                                 |
| +    | 打开一个文件进行更新(可读可写)。                             |
| U    | 通用换行模式（不推荐）。                                     |
| r    | 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。 |
| rb   | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。一般用于非文本文件如图片等。 |
| r+   | 打开一个文件用于读写。文件指针将会放在文件的开头。           |
| rb+  | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。一般用于非文本文件如图片等。 |
| w    | 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb   | 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。 |
| w+   | 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb+  | 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。 |
| a    | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| ab   | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| a+   | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。 |
| ab+  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。 |

## 6.2 读取字符

读取全部行

```python
f = open("test.txt","r")
content = f.read(5)	#读五个字符，读取后，指针向后移到5的位置
content1 = f.readlines()	#将文档全部读取出来，按行作为一个元素放在列表里。
f.close()
```

读取一行：

```python
f = open("test.txt","r")
content1 = f.readline()	#读取一行，指针后移
f.close()
```

## 6.3 os库

```
import os
os.rename("test.txt","abc.txt")
```



# 7: 错误与异常

```python
f = open("123.txt","r")	#如果没有这个文件，就会报错
```

```python
try:
	print("---1---")
	f = open("123.txt","r")
	print("---2---")
except IOError:	#文件没找到属于IO异常
    pass	#捕获异常后的处理
```

要捕获异常类型，才不会报错

捕获多个异常：

```python
try:
	print("---1---")
	f = open("123.txt","r")
	print("---2---")
except (IOError,NameError):	
    pass
```

获取错误描述：

```python
try:
	print("---1---")
	f = open("123.txt","r")
	print("---2---")
except IOError as result:
    print(result)
```

捕获所有异常：

```python
try:
	print("---1---")
	f = open("123.txt","r")
	print("---2---")
except Exception as result:
    print(result)	#捕获异常后的处理
```

try finally:

```python
try:
	f = open("123.txt","r")
except Exception as result:
	print(result)
finally:
	f.close()
```


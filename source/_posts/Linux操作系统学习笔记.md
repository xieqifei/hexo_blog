---
title: Linux操作系统
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-20 17:40:28
password:
summary: 作为服务器最流行的操作系统，linux在网站方面意义重大，了解linux系统的编程、操作和系统结构，就显得尤为重要。
tags: Linux
categories: 学习笔记
---

# 1：Linux简介

开源软件：

- 使用自由
- 研究自由
- 散布及改良的自由

LAMP：Linux+Apache+Mysql+PHP

网络配置：

- 桥接：像一台真实电脑一样直接连接互联网
- NAT：类似于使用真实电脑的热点
- host only：只允许和真实机通行

`ifconfig eth0 192.168.118.2`//配置网卡和ip，如果配置为桥接，需要设置为局域网相同网段。此设置重启后失效。

注意事项：

- Linux严格区分大小写
- Linux所有内容以文件形式保存，包括硬件。

**Linux目录的作用**

| 目录名       | 目录的作用                                                   |
| ------------ | ------------------------------------------------------------ |
| /bin/        | 存放系统命令的目录，普通和超级用户都可以执行                 |
| /sbin/       | 保存和系统环境设置相关的指令，只有超级用户root能使用，但普通用户可以查看 |
| /usr/bin/    | 单用户模式下不能使用，类似于win的安全模式                    |
| /usr/sbin/   | 超级用户才能使用                                             |
| /boot/       | 系统启动目录，保存系统启动相关文件，如内核文件和启动引导程序 |
| /dev/        | 设备文件保存位置，                                           |
| /etc/        | 配置文件，默认保存在这                                       |
| /home/       | 普通用户的家目录                                             |
| /lib/        | 系统调用的函数库                                             |
| /lost+found/ | 当系统意外崩溃或关机，产生一些文件碎片放在这里。系统启动时，fsck工具会检测这里。每个分区下都有自己的此文件 |
| /media/      | 挂载目录，媒体设备                                           |
| /mnt/        | 挂在如优盘、移动硬盘                                         |
| /misc/       | 挂在NFS服务的共享目录                                        |
| /opt/        | 第三方安装的软件保存位置。现在的习惯是，放在/usr/local/，类似于win中的Program Files文件夹。 |
| /proc/       | 虚拟文件系统。存放在内存中，保存系统的内核、今后曾、外部设备状态、网络状态。不要再这里面写东西 |
| /sys/        | 虚拟文件系统。内存中，保存内核相关                           |
| /root/       | 超级用户的家目录。普通用户在/home/                           |
| /srv/        | 服务数据目录。                                               |
| /tmp/        | 临时目录。可以清空                                           |
| /usr/        | 系统软件资源目录。unix software resource                     |
| /var/        | 动态数据保存目录。保存缓存、日志以及软件运行产生的文件。     |



# 2：Linux常用命令

**命令格式**

命令 [-选项] [参数]

例： ls -la /etc

说明：

1. 个别命令不遵守此格式

2. 当有多个选项时，可以写在一起

3. 简写选项与完整选项

   -a 等于 --all

## 2.1 目录处理命令

### 2.1.1 展示子目录：ls

命令名称：ls

命令原意：list

```shell
ls	#显示当前目录的非隐藏文件
ls -a	#all，显示隐藏文件和非隐藏文件，隐藏文件文件名格式：.abc
ls /	#查看根目录，/+文件路径可以查看任意位置
ls -l	#long，长格式显示，显示文件的详细信息
ls -h	#human,比如将文件大小单位列出来，人性化显示
ls -d	#direct，只显示目录本身
ls -i	#inode，展示i节点，文件的唯一身份标识
```

ls -l的结果：
```shell
total 31056
-rwxr-xr-x  1 root root     3612 Jun 23 19:17 Aria2Drive.sh
权限 引用次数 文件所有者 文件所属组 文件大小(单位字节) 文件最近修改时间 文件名
```
**权限：-rw-r--r--**

权限第一个参数：

| -    | d      | l      |
| ---- | ------ | ------ |
| 文件 | 文件夹 | 软连接 |

剩余九个参数：

| rw-         | r--         | r--     |
| ----------- | ----------- | ------- |
| u所有者权限 | g所属组权限 | o其他人 |
| r读         | w写         | x执行   |

### 2.1.2 新建目录：mkdir

命令名称：mkdir

命令原意：创建文件目录

```shell
mkdir /tmp/123
mkdir -p /tmp/japan/123	#递归创建目录，japan目录原来也没有
mkdir -p /tmp/japan/123 /temp/japan/456	#创建多个目录
```
### 2.1.3 切换目录：cd

```shell
cd /tmp/japan/123	#切换到指定目录
cd .	#表示当前目录
cd ..	#回到上级目录
```

### 2.1.4 展示当前完整路径：pwd

```shell
pwd	#显示当前位置的完整路径
```

### 2.1.5 删除空目录：rmdir

```shell
rmdir /tmp/japan/123
```

### 2.1.6 复制：cp

```shell
cp /etc/grab.conf /root	#复制文件
cp -r /tmp/japan/123 /root	#复制目录
cp /tmp/japan/123.log /tmp/japan/456.log /tmp	#复制多个文件
cp -p /tmp/abc.log /tmp	#保存文件的属性不变，比如最近修改的时间。
cp /tmp/japan/abc.log /tmp/gaiming.log	#拷贝并改名
```

### 2.1.7 移动、剪切：mv

```shell
mv /tmp/japan/123 /tmp	#把123移动到/tmp目录下
mv /tmp/japan/123 /tmp/us	#移动并改名
mv 123 456	#当前目录下移动并改名，则是重命名
```

### 2.1.8 删除：rm

```shell
rm grab.conf	#删除文件
rm -f grab.conf	#强制删除文件，不会询问是否确认。
rm -r /tmp/japan	#删除目录
```

## 2.2 文件处理命令

### 2.2.1 新建文件：touch

```shell
touch japanlovestory.list	#新建文件
touch "program files" #创建带空格文件，不推荐带空格文件名
```

### 2.2.2 浏览短文件：cat

```shell
cat /etc/issue	#显示内容
cat -n /etc/issue	#显示行号
```

### 2.2.3 按行反向显示浏览：tac

### 2.2.4 分页浏览长文件：more

```shell
more /etc/services	#空格或f向下翻页，按行向下翻，按q退出，帮助信息都是调用more命令。
```

不能往上翻。

### 2.2.5 可向上分页浏览：less

```shell
less /etc/services	#向下翻页和more相同，按pgup向上翻页，上箭头向上一行。
#搜索功能，浏览状态，输入“/service”，按“n”向下找搜索结果。

```

### 2.2.6 浏览文件前几行：head

```shell
head /etc/services	#默认显示前十行
head -n 7 /etc/services	#显示前七行
```

### 2.2.7 浏览文件末尾几行：tail

```shell
tail /etc/services	#显示末尾几行
tail -n 7 /etc/services	#显示指定行
tail -f /var/log/messages	#动态显示文件末尾几行
```

## 2.3 链接命令

**生成链接文件：ln**

```shell
ln -s /etc/issue /tmp/issue.soft	#创建文件的软连接
ln /etc/issue /tmp/issue.hard	#创建文件的硬链接
```

软连接：类似windows的快捷方式

硬链接：硬链接和源文件属性相同，且同步更新，可以向硬链接中写入内容，删除源文件后，硬链接任然存在。硬链接的i节点与源文件相同。

硬链接不能跨分区，生成对象不能是目录

## 2.4 权限管理命令

## 2.4.1 文件权限变更：chmod

命令名称：chmod（change the permissions mode of a file）

语法： chmod \[{ugoa}\]{+-=}\[文件或目录\]

​							\[mode=421\]\[文件或目录\]

​							-R 递归修改

权限只有管理员root和所有者可以修改。

```shell
chmod g+w,o-r japan.list	#所属组加读权限，其他人减少读权限
chmod g=rwx japn.list
```

数字表述法：

|  r   |  w   |  x   |
| :--: | :--: | :--: |
|  4   |  2   |  1   |

rwxrw-r--对应权限：(4+2+1)(4+2)(4)=764

```shell
chmod -R 777 /tmp/abc	#修改/tmp/abc及其下面的的所有文件的权限为777
```

> root用户创建的644权限文件，其他用户能否删除？
>
> 答案取决于用户是否具有文件所属的目录的写权限。

比如：
一个目录/temp权限为777

文件/temp/testfile的权限为644。

那么普通用户任然可以删除/temp/testfile，因为所有人都有/tmp目录的写权限，所以可以再/temp目录下新建文件、目录、删除目录和删除文件。

|           | r读权限                 | w写权限              | x执行权限      |
| --------- | ----------------------- | -------------------- | -------------- |
| file      | cat/more/less/head/tail | vim                  | script command |
| directory | ls                      | touch/mkdir/rmdir/rm | cd             |

## 2.4.2 文件所有者变更：chown

语法： chown [用户] [文件或目录]

```shell
useradd shenchao	#添加一个用户
chown shenchao /tmp/japan	#改变所有者
```

## 2.4.3 文件所属组变更：chgrp

```shell
groupadd lampbrother	#新建组
chgrp lampbrother /tmp/japan	#更改所属组
```

### 2.4.4 显示新建文件的缺省权限：umask

```shell
umask -S	#显示新建目录缺省权限
umask	#显示新建目录缺省权限的与值
umask 077	#修改新建文件的缺省权限为700.原理为，777与077得700
```

0022

0：特殊权限

022：--- -w- -w-

则目录的权限为：777和022逻辑与的结果，等于755

## 2.5 文件搜索命令

### 2.5.1 文件搜索命令：find

语法：find [搜索范围] [匹配条件]

```shell
find /etc -name init	#在/etc目录下搜索名为init的文件，initab不能被搜索到。
find /etc -name *init*	#名字包含init就会被搜索，*作为通配符
find /etc -name int???	#?
find /etc -iname init???	#搜索名字不区分大小写。
```

```shell
find / -size +204800	#查找大于100MB文件，按数据块算，一个数据块=0.5K。204800个数据块=100MB
```

```shell
find / -user shenchao	#根据所有者查找
```

```shell
find /etc -cmin -5	#根据文件属性5分钟内被改的文件查找 change
find /etc -amin -5	#根据访问时间 access
find /etc -mmin -5	#根据文件内容被改变 modify
```

```shell
find /etc -size +163840 -a -size -204800	#查找大于80MB并且小于100MB的文件
find /etc -size +163840 -o -size -204800	#查找大于80MB或者小于100MB的文件
```

```shell
find /etc -name init* -a -type f	#查找类型为文件的init*对象
```
-exec/-ok 对搜索结果进行处理。-ok比-exec多了询问是否操作过程

```shell
find /etc -name inittab -exec ls -l {} \;	#-exec 命令 {} \;为固定格式
```

```shell
find /etc -inum 3442	#根据i节点查找文件
find /etc -inum 3442 -exec rm {} \;	#删除i节点为3442的文件。可用于名字奇怪的文件
```

### 2.5.2 其他文件搜索命令：locate

在文件资料库中查找文件，速度非常快。但是不能及时更新。

```shell
locate inittab	#查找inittab的位置
locate -i inittab	#查找时不区分大小写
```

更新资料库

```shell
updatedb
```

> locate无法收录/tmp目录下的文件

### 2.5.3 命令文件搜索命令：which

对命令使用

```shell
which cp	#查找命令绝对路径的位置，并且得到命令是否有别命
```

### 2.5.4 命令文件搜索命令：whereis

```shell
whereis rm	#找到命令绝对路径，和命令的帮助文档所在位置
```

### 2.5.5 文件内容搜索命令：grep

| 参数 | 作用         |
| ---- | ------------ |
| -i   | 不区分大小写 |
| -v   | 排除指定字串 |



```shell
grep multiuser /etc/inittab	#查找文件中multiuser所在的行。
grep -v ^# /etc/inittab	#排除指定字串所在的行，即以#开头的行，不希显示。
```


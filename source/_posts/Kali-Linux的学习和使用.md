---
title: Kali Linux的学习和使用
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-19 12:05:29
password:
summary: Kali Linux是linux下的一个发行版本，它的特殊是包含了众多安全渗透相关的软件，最基础的如nmap，从事安全方面工作的工程师，可以直接下载kali而无需再在linux上安装所需的软件包。同时，kali还被广泛用于黑客攻击和网站安全测试。
tags: Kali Linux
categories: 学习笔记
---

# 0：Kali简介

Kali作为最常用的网络渗透系统，内置了网络安全人员和黑客最常用的各类工具。

# 1：Kali安装

**安装途径：**

1. 在VMWare虚拟机上安装Kali，[Kali Linux 系统安装详细教程](https://blog.csdn.net/xfyangle/article/details/81045116)

2. 在Vultr上上传自定义的IOS。将Kali部署到服务器上。

**常见问题：**

- 切换图形界面和命令界面，当主机内存小于2g时，使用图形界面体验更好。

1. 切换命令界面：Ctrl+Alt+F3

2. 切换图形界面：Ctrl+Alt+F1

- 在新版本Kali Linux2020中默认系统登陆为非超级管理员权限，也就是说不是root权限。在安装过程中，只有普通用户的用户名和密码设置。不会涉及root密码的设置。现在为root设置密码。

1. 命令界面输入`sudo passwd root`

2. 输入新密码，重复输入新密码。密码设置完成。

- 开启SSH，如果你在服务器中安装了Kali，这个操作是必须的，他能让你使用其他终端，登陆Kali。

1. 命令界面输入`sudo vim /etc/ssh/sshd_config`。

2. 修改部分内容为如下：

```
#Authentication
LoginGraceTime 2m
PermitRoootLogin yes
StrictModes yes
MaxAuthTries 0
MaxSessions 10
```

3. 保存退出

4. 重启ssh `service ssh restart`
5. 查看ssh是否启动，`service ssh status`
6. 如果显示active则已生效，现在可以使用ssh登陆了。

# 3：被动信息收集

## 3.1 常用查询指令和工具

`Ctrl+L`:清屏

`ping sci.ci`:查看域名的访问速度

`nslookup baidu.com`:域名服务器查询。

`dig @8.8.8.8 baidu.com any`:查询，@8.8.8.8域名服务器记录的baidu.com域名解析记录。any:查询所有记录。不加只查A记录。+noall +answer只输出域名记录结果。

`dig -x 35.201.203.235`：查询反向解析，ptr记录，需要有解析记录才行

`dig txt chaos VERSION.BIND @ns3.dnsv4.com`:查询Dns服务器版本。通过版本信息查找漏洞

`whois baidu.com`:查询注册信息

查询接口：

- 备案信息：`http://www.beianbeian.com`

- 天眼查：`https://www.tianyancha.com`

## 3.2 子域名挖掘

1. 子域名挖掘工具：Maltego子域名挖掘机
2. 搜索引擎：`site:qq.com`
3. 第三方网站：`http://tool.chinaz.com/subdomain、https://dnsdumpster.com`

## 3.3 Maltego使用

## 3.4 Shodan搜索技巧

`https://www.shodan.io`

搜索技巧：

`webcam`//网络摄像头

`city:beijing webcam`//位于北京的摄像头

`net:35.201.203.235`//查询到ip绑定的域名等信息

`port:9200`//搜索开启9200端口的服务器

## 3.5 Google搜索引擎的使用技巧

- `inurl:qq.txt`文件
- `inurl:admin_login.asp`登陆界面
- `intitle:index.of .bash_history`表示网站目录对我们开放，我i们可以看到该目录下所有文件的信息。`.文件名`表示隐藏目录。
  - 查找mysql的配置文件，my.cnf
  - 查找discuz论坛中储存mysql密码的配置文件confg_global.php

- `cache:baidu.com`搜索缓存的页面，虽然某些页面被删了
- `Kali filetype:torrent`Kali时关键词，搜索Kali相关的指定文件类型
- `apache site:baidu.com `某网站下的关键词
- `intext:user.sql intitle:index.of`查询包含user.sql用户信息数据的网站，并且网站目录可见
- `s3 site:amazonaws.com filetype:xls password`s3关键字，亚马逊的一种服务器类型。

**总结：谷歌黑客数据库：https://www.exploit-db.com/google-hacking-database**

# 4: 信息收集

## 4.1 主动采集

- `ping 192.168.1. -c 1`-c 1发一个ping包就停止，默认一直发

- `traceroute baidu.com`检测路由

**ARP：将ip解析为MAC物理地址**

- 使用`apring`命令查看局域网中的IP是否有冲突

如何在Kali中排查黑客是否冒充网关进行中间攻击？

伪装机配置：`ifconfig ens33 192.168.1.1`

Kali探测：`arping 192.168.1.1 -c 1`//查看局域网中是否存在arp攻击，出现两个MAC地址对应IP地址，就有ARP攻击了。

- `netdiscover -i eth0 -r 192.168.1.0/27`查看局域网中存活的设备。

-i 是网络设备，网卡。-r 表示扫描范围

## 4.2 被动采集

`netdiscover -p`扫描局域网中的设备，不容易被管理员发现

## 4.3 用Hping3对服务器进行DOS攻击

`hping3 -c 1000 -d 120 -s -w 64 -p 80 --flood --rand-source baidu.com`

-c 发送包的数量

-d 发送到目标机器的每个数据包的大小。单位是字节

-s 只发送SYN数据包

-w TCP窗口大小

-p 目的地端口（80是web端口）

--flood 洪水攻击模式，尽可能快地发送数据包

--rand-source 使用随机性的源头ip地址。只是在局域中伪造

## 4.4 使用Nmap进行半连接扫描

`nmap -sS 101.200.128.35 -p 80,81,21,25,110,443`看某个端口是否开启

`nmap -sS 101.200.128.35`查看其打开的端口，但不会完全扫描65535个端口

`nmap -sS 101.200.128.35 -p 1-1024`指定端口范围

## 4.5使用nc扫描端口

`nc -nv -w 1 -z 192.168.1.1 1-100`

1. 实现任意TCP/UDP端口的监听，nc可以作为server以TCP或UDP方式监听指定端口。
2. 端口的扫描，nc可以作为client发起TCOP或UDP连接。
3. 机器之间传输文件。
4. 机器之间网络测速

nc参数：

-nv 表示扫描的目标是个IP地址不做域名解析

-w 表示超时时间，超过时间则不再扫描

-z 表示进行端口扫描

## 4.6 Scapy定制ARP和ICMP协议数据包

伪装网络报文

`scapy`如果报错弹出：`Wont be able to plot`

`apt install python-matplotlib`

`exti()`//退出Scapy

`ARP().display()`//列出arp相关参数
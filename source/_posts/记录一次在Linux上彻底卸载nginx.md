---
title: 记录一次在Linux上彻底卸载nginx
top: false
cover: false
toc: true
mathjax: false
date: 2020-09-10 21:34:01
password:
summary:
tags: Nginx
categories: Linux
---

刚想通过nginx做反向代理，以实现内网穿透，但是80端口始终无法ping通，查看linux端口是开着的。思想简单的我，尝试重新安装nginx，然而无论如何都无法卸载干净。首先，nginx可能不是通过apt-get安装的，使用`apt remove`指令没有卸载掉，于是尝试`find / -name nginx -exec rm -rf {} \;`来删除所有以nginx命名的目录，然而`apt-get install nginx`安装后，任然没有把删除的目录重新安装。

查阅资料后，重新进行了删除和安装

**dpkg指令**

```shell
dpkg -l	#查看安装的软件包
```

尽管已经通过删除目录移除了nginx，在软件包列表里仍然存在三个包含nginx的软件，`nginx`、`nginx-core`、`nginx-common`

使用指令

```shell
sudo dpkg -r 软件名	#分别删除三个软件
```

其中`nginx-common`无法使用上述命令删除，

```shell
sudo apt-get --purge remove nginx-common
```

至此nginx以彻底移除。

使用

```shell
apt-get install nginx
```

重新安装nginx。

安装结束，访问ip可出现welcome to nginx网页。

如果没有出现，

```shell
curl http://ip
```

如果主机上出现html文本，而浏览器外部无法访问，则是防火墙问题。

我在ubuntu上安装了firewall软件，因此导致外部无法访问80端口，使用指令

```shell
systemctl stop firewall.service
```

关闭firewall。也可能是iptables开启了，相同的办法可以关闭iptables防火墙。



**参考文献：**

《[Linux之Ubuntu下如何查看已安装的软件/库文件](https://www.shuzhiduo.com/A/kvJ3Grm7Jg/)》
---
title: SSR负载均衡实现自动切换端口
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-16 01:09:41
password:
summary: 如果使用ssr自带的协议和加密算法连接外网，会经常性被封锁端口。多次端口封锁后，会被封锁ip。后来我引入了tls协议，在国内服务器上对ssr流量使用ssl证书加密，伪装成常见的https流量，再转发给国外ssr服务器。这种方案，极大地降低了端口被封锁的风险。为了确保ssr长期存活，我又决定利用负载均衡技术，在国内中转服务器上加一个代理软件haproxy。haproxy能自动检测国外服务器端口是否被封锁，如果被封锁则切换端口。我在国外服务器上预留了三个长期开放的端口，只有某个端口被封时，另外两个端口才可能被使用。这种方法也适用于多个ip。可在一个ip被封锁后，切换为另一个ip。
tags: 
 - SSR
 - haproxy
 - 负载均衡
categories: Linux
---

# 1：准备工作

- 一台国内服务器。

国内服务器可以监控到国外的服务器ip或者端口是否被封锁。如果你用国外的服务器，也能做到负载均衡，但是如果这台服务器被封锁，那SSR还是会断开连接。国内服务器不会被封锁。

- SSR添加多个端口或者多台SSR服务器

修改SSR配置文件，监听多个端口《[SSR添加多用户多端口教程](https://ssr.tools/194)》，我监听的是34567、34568、34569。

> 注意：不同端口或者IP的密码和协议等等都必须相同。

# 2：haproxy下载和配置

在Ubuntu上下载：

```shell
apt-get -y install haproxy
```

在centos上：

```shell
yum -y install haproxy
```

安装完毕后，打开haproxy配置文件`/etc/haproxy/haproxy.cfg`，修改配置为：

```
global
    log /dev/log local0
    log /dev/log local1 notice
    user root
    group root
    daemon

defaults
    log global
    mode tcp
    timeout connect 5s
    timeout client 5s
    timeout server 5s
    option      dontlognull
    option      redispatch
    retries     3

listen status
  bind *:1111
  mode  http
  stats refresh 30s
  stats uri /status
  stats realm Haproxy  
  stats auth admin:admin
  stats hide-version
  stats admin if TRUE

frontend shadowsocks-in
    mode tcp
    bind *:8388
    default_backend shadowsocks-out

backend shadowsocks-out
    mode tcp
    option      tcp-check
    balance roundrobin
    server  servername1    xxxxx1.com:8088  check
    server  servername2    xxxxx2.net:8080  check
    server  servername3    12.34.56.78:9999  check
    server  servername4    123.234.234.123:443  check
```

配置分为五大部分：

- **global**：全局配置，这里主要是配置了日志
- **默认值**：默认配置，也是一些分布式配置
- **听**：监听配置，我在`1111`端口配置了http监控页面，访问`ip:1111/status`，认证用户名和密码都是`admin`，具体含义参考博客[“ haproxy监控页面和页面详细参数”](https://www.centos.bz/2018/01/haproxy监控页面-和页面详细参数/)
- **frontend**：前端配置，Shadowsocks数据流从这里进入，流向指定标题，注意这里的端口号，即为ss客户端的输入端口
- **backend**：调整配置，`balance`指定负载平衡方式，每个服务器写一个服务器行，服务器支持域名或IP。如果使用域名，重启服务出现问题，可以参考博客[“解决Haproxy用Systemd启动失败的问题”](https://www.solarck.com/systemd-wait-network-online.html)。

# 3：其他负载均衡方式

《[shadowsocks借助nginx实现负载均衡，自动切换“零”宕机](https://project-gutenberg.github.io/Pincong/post/16c147e99745aee701fe6e9fee34e466/)》介绍了使用nginx的方法。也可以尝试。

我之前使用过apache来做负载均衡，但是无法连接，查看apache日志后提示如下：

```verilog
[Tue Jun 16 00:07:30.557609 2020] [core:debug] [pid 25543:tid 140127116449536] protocol.c(1335): [client 127.0.0.1:57310] AH00566: request failed: malformed request line
```

意思是请求行格式错误。刚开始以为配置错了。阅读apache的相关资料后才知道。

> apache在做负载均衡时，只是对子目录进行了替换，比如说，你监听1234端口，将发送给主机1234端口的数据均衡发送给192.1.1.1:5678、192.1.1.2:6789。那么你在访问baidu.com/test 时，将你的请求发送给1234端口，而apache做的仅仅只是将请求HOST替换掉。真实请求地址是192.1.1.1:5678/test 或者192.1.1.2:6789/test 。。而ssr加密后的数据，是没有http请求头的，这便导致了，日志所显示的请求行格式错误。所以apache并不适合做单纯的数据转发。

# 4：参考文献

《[用Haproxy实现多VPS线路负载均衡](https://tianws.github.io/skill/2019/07/11/gfw/)》

《[shadowsocks借助nginx实现负载均衡，自动切换“零”宕机](https://project-gutenberg.github.io/Pincong/post/16c147e99745aee701fe6e9fee34e466/)》

《[Haproxy（二）之负载均衡配置详解](https://www.jianshu.com/p/a7e3199a0a09)》

《[Apache 正向代理与反向代理配置](https://blog.csdn.net/sforiz/article/details/79651643)》


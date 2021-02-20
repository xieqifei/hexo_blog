---
title: SSR加速和降低服务器端口被封风险
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-08 15:22:38
password:
summary: 在使用ssr进行外网连接时，可能会遇到访问特别慢，youtube 1080P缓冲缓慢的问题。而如果用的服务器是自己租用的，ssr也是自己搭建的。按照服务器给的带宽，不应该存在如此慢的播放速度。如果存在，你需要查看，你正在连接ssr的手机或wifi网络是不是中国移动。https://speedtest.cn 可以帮助你检查你正在使用的是不是移动网络，如果是，那你很有必要阅读此文章对ssr进行加速。非BBR内核加速，需要一台国内电信优化的服务器。
tags: 
  - SSR
  - Stunnel
categories: Linux
---

另外需要说明的是，购买的国内服务器，不需要进行备案，你可以帮定一个域名到此服务器上，填写ssr客户端也只需要使用这个域名，ssr会自动帮你查询dns，此后即便国外ssr出现端口或ip被封，也不需要更改ssr客户端的配置信息。而只需要另起一台国外服务器，修改国内中转服务器的配置信息。

如果你懂得更多编程知识，你可以在国内国外的两台服务器上进行编程，实现，当国外服务器端口被封，国内和国外服务器自动检测，若检测到端口被封，则国外服务器修改ssr配置端口，而国内服务器修改stunnel配置信息。这样，无需人为干预，也能实现长久而稳定的连接。如果有空的话，我会去探索这个方案。

# 1：原理说明

简单了解，可以提高你对网络的认识。

## 1.1 ssr网络加速原理

由于中国移动对普通用户的海外流量随机高概率丢包，导致国外流量进入国内时，走移动线路，就会有很高的丢包率。所以我们希望借助一台，属于移动vip级别的国内服务器（这样移动出海线路优先级高，不容易丢包），或者出入海走联通电信线路的服务器。通常，阿里云、腾讯云服务器都能满足要求。

国内服务器上安装stunnel，并配置为客户端。国外服务器安装stunnel服务端和ssr服务端。

![](https://ae01.alicdn.com/kf/Hb7bba28a9bda457899c0f56c82042376m.jpg)

## 1.2 降低端口被封的风险原理

stunnel是一个将流量利用ssl证书加密的软件，加密后的流量，出国时将会被识别为tls协议，因此不再具有ssr流量特点，我们常使用的https请求便基于ssl证书，此时国外服务器端口被封锁的几率将大大减小。出国后，stunnel将流量解密，再传递给ssr服务端。

通过抓取ssr流量，可以看到，ssr流量在传输过程中，由于协议加密，它不具备任何常见的网络请求特征，而你在使用ssr与海外服务器进行数据往来时，长时间的使用不具有网络请求特征的流量进行连接，很容易被防火墙识别为翻墙软件。因此被封。使用tls协议，就能够伪装为https请求，https(http协议+tls协议)。当然为了更有迷惑性，在ssr服务器端口加一个能被访问的网站是最好的。因为虽然tls协议作为https的一部分，但在实际使用中，由于没有http协议的流量，那么是能被推断出使用stunnel这类加密隧道的。如果长时间大流量访问，也有被封锁的风险。

而使用中转服务器，所付出的代价，仅仅是国内月租几块钱的服务器(阿里云、腾讯云等有学生服务器，一年一百块左右)，和相当低的stunnel加解密过程所导致的延迟，此延迟几乎不可见。

# 2：stunnel和ssr安装

## 2.1 stunnel安装和配置

stunnel安装，请参见我之间的文章《[利用Stunnel+squid代理实现国内无客户端访问外网](https://sci.ci/2020/05/28/stunnel-squid-dai-li-jia-su-ya-chen-jiao-xue-shi-pin-jia-su/)》。

> 3.2节是在国外服务器上安装stunnel服务端。第四章，在国内服务器上安装stunnel客户端。

将ssr的流量通过stunnel加密，你需要修改配置文件。在《[利用Stunnel+squid代理实现国内无客户端访问外网](https://sci.ci/2020/05/28/stunnel-squid-dai-li-jia-su-ya-chen-jiao-xue-shi-pin-jia-su/)》安装后的stunnel基础上修改。



修改国外服务器上的stunnel服务端配置文件，在`/etc/stunnel/stunnel.conf`文件里，

删除

```
[squid-proxy]
; 服务监听的端口，client要连接这个端口与server通信
accept = 3129
; 服务要连接的端口，连接到squid的3128端口，将数据发给squid
connect = localhost:3128
```

添加

```shell
[tls2socks]
;服务端监听一个端口，这个端口用于接收stunnel客户端传过来的数据，建议设置在10000~60000之间，务必在linux防火墙和阿里云这类的服务商安全组开放，例如设置为：accept = 12345
accpet = your accept port 1
;连接到，ssr的服务器端口上，与ssr服务端配置的端口保持一致
connect = localhost:your ssr server port
```



修改国内中转服务器上的stunnel客户端配置文件，在`/etc/stunnel/stunnel.conf`文件里，

删除

```shell
[squid-proxy]
; 监听3128端口，那么用户浏览器的代理设置就是 stunnel-client-ip:3128
accept = 3128
; 要连接到的stunnel server的ip与端口，修改xx.xx.xx.xx为国外的服务器
connect = xx.xx.xx.xx:3129
```

添加

```shell
[socks2tls]
;服务端监听一个端口，这个端口用于接收ssr客户端传过来的数据，建议设置在10000~60000之间，务必在linux防火墙和阿里云这类的服务商安全组开放。例如 accept = 34567
accpet = your accept port 2
;连接到，stunnel服务端接收端口上。按照该服务端的例如配置，这里应该为：connect = xx.xx.xx.xx:12345。xx表示国外服务器ip地址。
connect = stunnel server ip:your accept port 1
```

## 2.2 ssr安装和配置

ssr安装，可以参考《[SSR 一键安装脚本](https://ssr.tools/31)》，在国外的服务器上安装。

ssr服务端端口需要和stunnel服务端的`connect`端口保持一致。

ssr客户端配置，客户端地址，应该填写为国内服务器ip，服务端口，应填写为stunnel客户端的`accept`端口(国内服务器上配置的accept = your accept port 2)，例子中给的34567端口。

配置完成后，就可以连接上网了。

即便使用移动网络，也能产生非常好的连接效果，而且应该在非常久得时间里，端口都不会被封。

# 3：可能出现的问题

如果配置后无法上网，请检查你设置得端口，通过 https://port.ping.pe 网址检测你的端口是否开启，如果测试未开启，请修改防火墙配置，在《[利用Stunnel+squid代理实现国内无客户端访问外网](https://sci.ci/2020/05/28/stunnel-squid-dai-li-jia-su-ya-chen-jiao-xue-shi-pin-jia-su/)》中第4章有介绍。若任然没有导通，请检查云服务商的安全组是否开启了端口。

如果端口开放，而无法连接，请尝试，终止stunnel进程并重新启用。

方法1：

```shell
servicer stunnel restart
```

方法2:

检查stunnel的进程

```shell
ps aux | grep -i stunnel
```

找到进程号pid，在第二列

杀掉进程

```
kill -9 替换为pid
```

然后重新启用stunnel

```shell
stunnel
```

如果没有任何显示，则stunnel重启成功，且配置文件没有任何问题。否则将显示错误信息。
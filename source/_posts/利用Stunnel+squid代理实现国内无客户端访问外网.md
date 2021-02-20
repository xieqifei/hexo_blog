---
title: 利用Stunnel+Squid代理实现国内无客户端访问外网
top: false
cover: false
toc: true
mathjax: false
date: 2020-05-28 23:26:23
password:
summary: 国内服务器使用tls协议加密代理请求，免于被GFW过滤。国外服务器代理访问。访问结果最终返回给浏览器。此方法无法解决移动过滤http请求中对youtube、fb等的访问。也就是说，移动用户无法访问youtube、fb等。最好的办法还是v2ray+ws+tls或者ssr+tls，将http请求加密。
tags: 
 - Stunnel
 - Squid
categories: Linux
---

# 1：加速原理

## 1.1 squid介绍

squid是一个用于代理的开源软件，用户主机将http请求发送到部署了squid的服务器，squid可以帮助用户转发http请求。利用squid，我们可以让服务器替我们请求数据，再由服务器转发给我们。

按找squid原理，我们就已经可以进行翻墙了，在国内能够访问的国外服务器上部署squid，将访问谷歌的请求发送给squid，squid替我们请求谷歌数据，再将谷歌数据转发给我们。这就是一个完整的工作过程。

但是由于squid请求报头未加密，在squid服务器向浏览器返回数据时，能够被GFW检测到访问数据来自于谷歌。从而将返回数据过滤掉。之后通过squid返回的数据，都会被GFW过滤掉，即使我们访问的是百度，也会无法收到返回数据。

## 1.2 Https介绍

这篇文章详细介绍了加密的原理：https://blog.csdn.net/clh604/article/details/22179907

![SSL](https://ae01.alicdn.com/kf/Hdb6994b2de5f42e7adca8620e915331aQ.png)

## 1.3 stunnel介绍

stunnel是一个开源软件，使用stunnel可以为我们使用tcp协议传输的数据使用SSL协议加密，这样我们的请求，除了客户端和服务器，其他人将无法从数据中提取到有用信息。

为了让GFW无法识别出主机访问谷歌和返回的数据来自谷歌。这里使用stunnel将主机和squid之间的通信数据加密，将http请求的报头也加密。

## 1.4 代理过程

在windows主机上设置全局代理，将windows的tcp请求，转发到国内服务器server 1上，国内服务器server 1与国外服务器server 2搭建stunnel加密隧道连接，国外服务器server 2利用squid，发送主机的访问请求，并将访问结果返回给server 1，server 1在发送给主机浏览器。加速原理如下图。

通常可以只利用一个国外服务器，在国外服务器上部署stunnel和squid，在本地主机上搭建stunnel客户端就可以了。如下图

![](https://ae01.alicdn.com/kf/H4ae21783bed543d7b6102e79eb735c89n.png)

但是，为了访问方便，即任何用户只需要设置windows上现有的代理服务器就能访问外网，而不需要下载stunnel客户端和配置客户端参数。

相比于主机使用Stunnel客户端，将stunnel客户端部署到国内服务器的缺点是：这需要国内外均有服务器。成本会更高。

更重要的是，我们与国内服务器之间的信息被抓取后，能够获取到我们访问的信息，比如是谷歌、使用的浏览器版本等，但是无法知道具体访问内容。由于是与国内服务器发送信息，因此被探测的几率很小。



# 2：主机设置代理访问

为了让阅读本文的读者，提前的看到成果，将server1和server2都配置好后，先介绍如何利用你搭建好的服务器访问外网。

1. 打开`代理服务器色设置`

![](https://ae01.alicdn.com/kf/H70a59c178af24750b89d0d045ee16845R.jpg)

2. 在代理服务器上输入国内服务器server1的IP和stunnel客户端监听的端口号。

![](https://ae01.alicdn.com/kf/Hcc9e9c1620b24b3da68519ffef92fd10N.jpg)

3. 使用浏览器登陆google

![](https://ae01.alicdn.com/kf/H2000b41723ff444bb46c057eee0bc5635.jpg)

实际上使用就是这么简单。当然为了你搭建的隧道不会被其他人使用，你也可以对能够访问这条隧道的IP进行限制，或者设置一个密码。会在后续中介绍到。

# 3：国外服务器配置

确保在服务商安全组里开放了3128和3129端口，这两个端口，将用于接收和转发数据。

## 3.1 安装配置Squid

1. 安装squid

ubuntu使用：

```shell
apt-get install squid
```

centos使用：

```
yum install squid
```

使用`squid -v`检查squid是否安装好。

2. 配置squid

打开squid配置文件`/etc/squid/squid.conf`。没有就新建此文件

将文件内容修改为：

```shell
# http_port 设置监听端口，默认为3128
http_port 3128

# access_log 设置access日志，daemon表示在后台将日志写入/var/log/squid/access.log文件, 
# combined是一个预定义的logformat，也可以使用自定义的logformat
access_log daemon:/var/log/squid/access.log combined

# visible_hotname 设置代理服务器的主机名
# 默认取本机的hostname
visible_hostname funway.aliyun.proxy

# ACLs all, manager, localhost, and to_localhost are predefined.
acl SSL_ports port 443
acl Safe_ports port 80    # http
acl Safe_ports port 21    # ftp
acl Safe_ports port 443    # https
acl Safe_ports port 70    # gopher
acl Safe_ports port 210    # wais
acl Safe_ports port 1025-65535  # unregistered ports
acl Safe_ports port 280    # http-mgmt
acl Safe_ports port 488    # gss-http
acl Safe_ports port 591    # filemaker
acl Safe_ports port 777    # multiling http
acl CONNECT method CONNECT

# 拒绝所有非Safe_ports的请求
http_access deny !Safe_ports
# 拒绝所有非SSL_prots的CONNECT请求
http_access deny CONNECT !SSL_ports

# Only allow cachemgr access from localhost
http_access allow localhost manager
http_access deny manager
# 允许来自本地的请求
http_access allow localhost

# 拒绝所有请求，最后兜底的规则
http_access deny all
```

更多详细的配置参数解释，请参考博文《[使用squid搭建代理服务器](https://www.hawu.me/operation/852)》

配置squid只能本地地址访问。

3. 运行squid

```shell
service squid start //运行
service squid status //查看squid运行状态
```

## 3.2 安装配置stunnel服务端

1. 生成自签名证书，如果没有openssl需要先安装。

```shell
openssl req -new -x509 -days 3650 -nodes -out stunnel.pem -keyout stunnel.pem
```

将生成的证书，复制到/etc/stunnel文件夹下

```shell
cp stunnel.pem /etc/stunnel
```

这个证书还需要下载到主机，之后上传到国内服务器，用于为stunnle客户端设置的公钥。

1. 下载安装stunnel

```shell
apt-get install stunnel
```

2. 设置stunnel配置文件

```shell
; 设置工作目录，没有目录需要先创建
chroot = /var/run/stunnel/
; 设置stunnel的pid文件路径（在chroot下）
pid = /stunnel.pid
; 设置stunnel工作的用户（组）
setuid = root
setgid = root

; 开启日志等级：emerg (0), alert (1), crit (2), err (3), warning (4), notice (5), info (6), or debug (7)
; 默认为5
debug = 7
; 日志文件路径（我的server的版本有个bug，这个文件也被放在chroot路径下了，client的版本则是独立的=。=#）
output = /stunnel.log

; 证书文件，就是在本文2.2中用openssl生成的自签名证书（server端必须设置这两项）
cert = /etc/stunnel/stunnel.pem
; 私钥文件
key = /etc/stunnel/stunnel.pem

; 设置stunnel服务，可以设置多个服务，监听同的端口，并发给不同的server。
; 自定义服务名squid-proxy
[squid-proxy]
; 服务监听的端口，client要连接这个端口与server通信
accept = 3129
; 服务要连接的端口，连接到squid的3128端口，将数据发给squid
connect = localhost:3128

; **************************************************************************
; * 下面这些配置我都注释掉了，但也需要了解下 *
; **************************************************************************

; 设置是否对传输数据进行压缩，默认不开启。
; 这是跟openssl相关的，如果你的openssl没有zlib，开启这个设置会导致启动失败（failed to initialize compression method）
;compression = zlib

; 设置ssl版本,这个也是跟安装的openssl有关的
;sslVersion = TLSv1

; Authentication stuff needs to be configured to prevent MITM attacks
; It is important to understand that this option was solely designed for access control and not for authorization
; It is not enabled by default!
; 下面这些配置用来定义是否信任对方发过来的证书。就好比浏览器访问https的时候，浏览器默认会信任那些由权威CA机构签发的证书，
; 对于那些自签名证书，浏览器就会弹出对话框提醒用户这个证书可能不安全，是否要信任该证书。
; 这是有效防止中间人攻击的手段
; verify 等级2表示需要验证对方发过来的证书（默认0，不需要验证，都信任）
; 因为这个配置是server端的，我们不需要理会client的证书（client也不会没事发证书过来啦）
;verify = 2
; CAfile 表示受信的证书文件，即如果对方发过来的证书在这个CAfile里，那么就是受信任的证书；否则不信任该证书，断开连接。
;CAfile = /etc/stunnel/stunnel-client.pem
```

3. 启动停止stunnel

```shell
service stunnel start //开启stunnel
service stunnel restart //重启
service stunnel stop //停止
service stunnel status  //查看状态
```

# 4：国内服务器配置Stunnel客户端

国内服务器，就是在浏览器代理设置中，填入的服务器IP，而端口，则是stunnel接收代理数据的端口。

1. 在服务器提供商网页中的安全组设置里开启stunnel接收数据的端口。

本人使用的阿里云轻量级应用服务器，进入阿里云控制台相应页面后，找到安全组->防火墙->添加新规则。在新规则里，开放，TCP协议的3128接口。也可以是其他接口。

2. 配置好安全组后，阿里云服务器本身还有一个防火墙，还需要在服务器里开放3128端口。

查看开放端口，如果没有firewall，请先安装

```shell
firewall-cmd --list-all
```

设置需要开放的端口

```shell
firewall-cmd --zone=public --add-port=3128/tcp --permanent//设置
firewall-cmd --reload//生效
```

此时再查看开放的端口，就能看到3128端口已开放。

3. 安装stunnel客户端。

ubuntu使用:

```shell
apt-get install stunnel
```

centos使用：

```shell
yum install stunnel
```

4. 设置stunnel客户端配置文件

```shell
; stunnel工作目录,如果没有/var/run/stunnel文件夹，需先创建
chroot = /var/run/stunnel/
; stunnel工作的用户组
setuid = root
setgid = root
; stunnel工作时候的pid
pid = /stunnel.pid
; 日志等级
debug = 7
; 日志文件
output = /var/log/stunnel/stunnel.log
; 表示以client模式启动stunnel，默认client = no，即server模式
client = yes
; 定义一个服务
[squid-proxy]
; 监听3128端口，那么用户浏览器的代理设置就是 stunnel-client-ip:3128
accept = 3128
; 要连接到的stunnel server的ip与端口，修改xx.xx.xx.xx为国外的服务器
connect = xx.xx.xx.xx:3129
; 需要验证对方发过来的证书
verify = 2
; 用来进行证书验证的文件（里面有stunnel server的证书）
CAfile = /etc/stunnel/stunnel-server.pem
; 客户端不需要传递自己的证书，所以注释掉
;cert = /etc/stunnel/stunnel.pem
;key = /etc/stunnel/stunnel.key
```

5. 将stunnle公钥证书拷贝一份到stunnel客户端配置文件的指定地址上。

配置文件里`CAfile = /etc/stunnel/stunnel-server.pem`写明了我们需要将公钥保存的位置和公钥的文件名。

我们只需要将，3.2节生成的证书打开，复制已`BEGIN CERTIFICATION`开头到`END CERTIFICATION`结束的公钥。然后，新建`/etc/stunnel/stunnel-server.pem`文件，将公钥粘贴进去。

这样stunnel客户端就能将数据加密后发送给stunnel服务器了。

6. 启动和停止stunnel

```
service stunnel start //开启stunnel
service stunnel restart //重启
service stunnel stop //停止
service stunnel status  //查看状态
```

# 5：不足和优化

## 5.1 代理方法的不足

1. 由于客户端的stunnel直接监听3128端口，而没有任何的验证和访问限制，因此，任何知道ip和端口的人都能通过设置代理，使用你的代理接口。

2. stunnel服务端没有设置对客户端的证书验证，任何通过自建stunnel客户端的人，都有可能使用你的stunnel服务，进行通信。

## 5.2 优化

1. 配置squid用户授权，即在用户使用代理上网时，弹出输入用户名和密码，输入正确，才能使用squid

- 修改配置文件`/etc/squid/squid.conf`，在`http_access deny all`之前追加以下内容，并注释掉`http_access allow localhost`

```shell
# auth_param 设置代理的认证方式
# 用法：auth_param scheme parameter [setting]
# scheme表示认证机制，squid支持Basic，Digest，NTLM，Negotiate四种认证机制

# program 设置认证程序为ncsa_auth（squid自带不少认证程序以及其他拓展程序），程序所需密码文件passwd。。如果linux是32位系统，认证程序/usr/lib/squid/目录下。认证程序名称有可能是basic_ncsa_auth。需要具体打开文件夹后查看。
auth_param basic program /usr/lib64/squid/ncsa_auth /etc/squid/passwd
# children 设置后台启动几个认证程序进程
auth_param basic children 3
# credentialsttl 设置认证失效时间，过期后需重新认证
auth_param basic credentialsttl 10 hours
# realm 设置认证时返回头里夹带的信息“wlecome to using my proxy”
auth_param basic realm welecome to using my proxy


# ！！！上面只是设置了需要认证，并未将认证生效，还需设置acl与http_access

# 添加名为AuthUsers访问列表，表示通过认证的用户
acl AuthUsers proxy_auth REQUIRED
# 允许AuthUsers的代理请求
http_access allow AuthUsers
```

- 新建密码文件

```shell
htpasswd -c /etc/squid/passwd qifei
```

如果没有htpasswd，则按提示安装。输入密码即可。

- 重启squid

```shell
service squid restart
```

2. 添加stunnel对客户端的证书验证，需要再新建一个证书文件，同时修改服务端和客户端的配置文件。

# 6：问题和调试

## 6.1 国内squid能接通，但无法访问网络

这可能是服务器端口没开

```
http://port.ping.pe/xx.xx.xx.xx:port
```

将上述网址，修改ip地址和端口后，通过浏览器打开，看看服务器端口是否能够接通，如果显示failed，那么参照第四章内容，安装firewald，并打开对应端口。

## 6.2 中国移动只能访问谷歌而无法访问YouTube

### 5.30日更新：

在经过一段时间的使用后，上种方法存在一些弊端。

在开启代理后，可以访问谷歌，和一些其他外网网站。但是，无法访问youtube、facebook等网站。反复刷新，还是可以访问到youtube。手机上的许多依赖外网的软件比如谷歌商店，无法使用。

利用wireshark对无线网卡进行抓包。发现，在浏览器访问youtube时，浏览器向国内的中转代理服务器，发送了http协议数据。此数据带有明显的请求头url`youtube.com`。猜测可能是国内路由，将含youtube等url的请求头过滤掉了。这样，请求youtube的数据根本无法到达中转服务器。

而多次刷新youtube网站，还是能够连同，说明国内路由并没有完全丢弃youtube请求，而是部分丢弃了我们的请求。

之所以谷歌能完全无阻地接通，我猜测，是因为国内许多网站，有依赖谷歌的各种api，因此屏蔽的损失远大于封锁的损失。因此只在国际出口上，对谷歌进行了封锁，而国内服务器之间的通信没有问题。

查阅https协议的工作原理不难知道。浏览器需要首先使用http协议，向youtube服务器发送请求，youtube服务器返回给浏览器SSL证书公钥，之后浏览器和youtube服务器才会使用SSL加密后的数据进行通讯。

在youtube服务器和浏览器之间添加代理，也只是转发数据，并没有改变浏览器访问youtube服务器时，发送包含包头的http数据的事实。

因此，即便我们在国内stunnel客户端和国外的stunnel客户端之间的数据进行了加密，也无法规避掉浏览器到国内stunnel服务器之间有http请求，造成请求被丢弃的问题。

最好的办法还是，在浏览器所在主机按转stunnel客户端。直接使用stunnel客户端向国外stunnel服务器发送数据，将浏览器的所有出口流量加密，直到发送到国外再解密，并转发。

这样做，其实和使用ss或者ssr原理相同。但是，缺少一些伪装。

### 5.31日更新：

我使用ping.pe网站提供的端口测试，发现，当我访问国内服务器3128端口时，除了中国移动的网络，其他包括中国电信和联通，外网在内的所有运营商均能ping同3128端口。

我猜测可能中国移动会对3128端口进行随机丢包。来阻止squid代理的使用。

我修改了接收代理的端口号。再次在Ping.pe上测试，移动已经能够ping通端口。然而，仍然存在谷歌能够访问，而youtube和fb不能访问的问题。

猜测，可能中国移动会随机丢弃请求地址是youtube的http请求数据。于是我更换了我的联通手机卡，来代理上网。发现谷歌、youtube和其他外网网站，均能很好的访问。我又测试了电信网络，发现结果也是好的。

**结论**，中国移动，在我们使用代理上网时，会对请求url为youtube.com之类的http请求进行人为随机丢包，而且丢包率极高。预测超过百分之八十。。了解移动国际带宽保有率，不难发现，移动的国际带宽占有率是三大运营商里最低的，却有第二的国际用户。可见为了解决带宽不足问题，他在国内便对youtube流量进行了丢弃，而不是到国际出口再丢弃。。

这么做的好处是，减少了youtube这类在国际出口会被拦截的流量占用传递时的带宽，降低国际出口过滤设备的投入。缺点是，会加大在国内路由过滤设备的投入。

通过对https原理和移动过滤youtube流量的分析，我们应该知道，在使用国内中转方式进行外网访问时，运营商能够知道你正在访问外网。虽然流量流向了国内服务器，如果网警对拦截的流量进行分析，是能够知道你访问了哪些外国网站的。。反向溯源，如果你使用了手机4G网络访问，那么，营运商配合，能够定位到你本人。如果使用wifi访问，能够定位到你的家庭。如果网警查看路由器日志，不难找到，是哪个手机访问了这些外网。

而http请求头中包含的设备信息，比如是什么电脑，浏览器品牌和版本等，无疑降低了找到你的难度。

总的来说，为了网络安全，降低被审查的风险。在本地主机部署stunnel客户端，比在国内服务器中转更加安全。但是长时间的加密连接国外服务器，不难让人猜出，你正在使用vpn之类的软件访问外网。使用wifi的代理比使用手机网络的代理更加安全。

## 6.3 使用用户名和密码手机软件不能弹出登陆框

在实际代理过程中，如果使用wifi或手机网络的软件，无法弹出squid的代理认证框，从而无法输入账号和密码。因此，软件会没办法访问外网，只有浏览器可以访问。

### 方法1：完全开放代理

修改`squid.conf`关闭用户名和密码验证。允许本地ip登陆。此时所有知道IP和端口的用户都能使用代理访问。。这种方法是极不安全的，因为，有许多爬虫软件，能够通过扫描ip和端口，收集能够使用代理的ip和对应端口。短时间的使用方法一是可以的，但长期下来，容易被攻破。

### 方法2：IP限制

在国内中转服务器上也安装squid。用户连接squid端口，squid将内容发送到中转服务器的stunnel接口，stunnel加密传输到国外，国外服务器，stunnel解密再分发请求。

完整拓扑结构：用户主机->squid（国内）->stunnel（国内）->stunnel（国外）->squid（国外）->网站服务器

添加squid的目的是，squid可以验证用户主机ip。通过squid设置能够连接squid的ip池。stunnel是无法验证的。我们之前的用户名和密码验证，是通过国外squid验证的，国外squid无法验证ip，是因为，国外squid接收的信息，全部来自localhost的端口。也就是国外stunnel发出的。

### 方法3：网页验证加自定义ACL辅助器验证

第一次登陆跳转网页验证用户名和密码，之后永久或缓存保存ip，免于此ip验证。

**设计思路**

1. 用户设置ip和端口，无密码连接代理服务器。
2. 用户打开浏览器，访问任意网页
3. 利用squidSGuard重定向无访问权限ip的访问url，转到认证网页。输入用户名和密码。
4. 网页后台验证用户输入的用户名和密码。并保存用户ip。
5. 编写squid ACL辅助器，读取并验证查找是否有这个ip。存在则通过，不存在，则进行重定向到验证页面。

### 方法4：自定义验证器加ACL辅助器验证

第一次登陆，弹出代理登陆框，通过后保存ip

1. 用户设置ip和端口，连接代理服务器。
2. 代理服务器通过ACL辅助器验证此IP是否在白名单中。
3. 不在白名单则，进入自定义验证器，在，则通过代理。
4. 进入验证器，验证器验证用户名和密码，如果通过，则保存IP到白名单，否则不通过。

我采用方法四进行用户验证，其中蛮多细节，于是另起一文讲解。
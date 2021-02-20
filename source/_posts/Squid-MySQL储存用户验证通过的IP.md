---

title: Squid+MySQL储存用户验证通过的IP
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-02 12:02:54
password:
summary: 在文章《[利用Stunnel+squid代理实现国内无客户端访问外网](https://sci.ci/2020/05/28/stunnel-squid-dai-li-jia-su-ya-chen-jiao-xue-shi-pin-jia-su/)》中，提到了，当使用代理验证实现代理访问时，手机的软件无法连接网络，而且切换浏览器需要重新验证用户名和密码。为了解决这个问题，我提出了四种方案。前两种漏洞太大，第三种，开发比较麻烦。于是，我采用了第四种方法。下面将对代码和具体细节进行说明。
tags: 
 - Squid
 - Mysql
categories: 其他
---

# 0：前文

在文章《[利用Stunnel+squid代理实现国内无客户端访问外网](https://sci.ci/2020/05/28/stunnel-squid-dai-li-jia-su-ya-chen-jiao-xue-shi-pin-jia-su/)》中，提到了，当使用代理验证实现代理访问时，手机的软件无法连接网络，而且切换浏览器需要重新验证用户名和密码。为了解决这个问题，我提出了四种方案。前两种漏洞太大，第三种，开发比较麻烦。于是，我采用了第四种方法。下面将对代码和具体细节进行说明。

阅读本文，请确保你已经安装了squid 3.5以上版本。`squid -v`可以查看你的安装版本。

# 1：配置基本环境MySQL和Python

## 1.1 数据库

Mysql被广泛用于网站数据库，网上的支持文档很多，重要的是，MySQL在python编程上具有非常好的支持。也就是mysqldb库。

使用数据库，主要是用来存放通过验证器验证的客户端ip，ACL辅助器可以通过读取这个ip，从而acl辅助器通过的ip不再需要验证器验证密码。所以，你使用任何其他数据库都是可以的。建议你选用你自己熟悉的数据库。因为本文对于如何在mysql下，创建数据表，只做大概说明。

我建议使用宝塔面板[bt.cn](bt.cn)安装mysql。这样可以可视化的安装mysql和创建数据库和数据表。也可以规避许多由于数据库权限问题导致的不可控因素。宝塔面板安装和在宝塔面板中安装mysql数据库，可以自行百度。非常简单。

在完成数据库安装后，请你在mysql里添加一个，库名为：squid。用户名为：squid。的数据库。用于存放squid数据。

![](https://ae01.alicdn.com/kf/H9601fdba1d3f4c70add1ada5fbad87ddl.jpg)

数据库新建成功后，点击右边的管理。进入phpmyadmin，新建一个数据表。表名为：sq_client_ip。

![](https://ae01.alicdn.com/kf/Hd7fb2278aa8e402cb347108ead39c32fZ.jpg)

表的结构：

![](https://ae01.alicdn.com/kf/Ha096c13cec1c4defa80d6a23e12ec289b.jpg)

注意，id项，新建表时，勾选`AI`，AI表示设置id为自增主键。这样每添加一个数据进去，id都会自行增加。

## 1.2 Python

安装Python，简单的安装python，你只需要输入下面指令

centos:

```shell
yum -y install python
```

Ubuntu:

```
apt-get -y install python
```

安装完python，需要安装python的mysqldb库。

```
pip install mysqldb
```

# 2：自定义基本验证器

新建一个python文件`AuthPro.py`。在里面添加如下内容：

```python
#!/usr/bin/python
# coding=UTF-8
import sys
import MySQLdb
""""""
def matchpasswd(login,passwd):
    #用户名和密码，用于验证输入的用户名和密码。
    #你可以自定义验证通过的要求，验证通过，返回True，验证不通过返回False
    if (login == '修改为你的用户名' and passwd == '修改为你的验证密码'):
        return True
    else:
        return False
def write_client_ip_to_db(username,client_ip):
    #数据库地址，数据库名，数据库密码，数据库用户名，数据库编码格式
    db=MySQLdb.connect("localhost","squid","修改为你的密码","squid",charset='utf8')
    # 使用cursor()方法获取操作游标 
    cursor = db.cursor()
    # 向sq_client_ip表中插入用户名和用户ip
    sql = "INSERT INTO sq_client_ip(username, client_ip) VALUES ('%s', '%s')" % (username,client_ip)
    try:
       # 执行sql语句
       cursor.execute(sql)
       #Ok 后面的print语句可以向cache日志中输出提示。/var/log/squid/cache.log查看
       print("执行sql语句")
       # 提交到数据库执行
       db.commit()
    except:
       #打印错误内容
       info = sys.exc_info()  
       print( info[0], ":", info[1]) 
	   #发生错误时回滚
       db.rollback()

    # 关闭数据库连接
    db.close()
    return
while True:
    # read a line from stdin
    line = sys.stdin.readline()
    line = line.strip()
    #提取输入参数。
    username,password,client_ip = line.split()
    #判断密码是否匹配
    if matchpasswd(username, password):
        #输出ok表示验证通过
        sys.stdout.write('OK\n 密码验证通过')
      	#将客户端ip添加至数据库ip白名单
      	write_client_ip_to_db(username,client_ip)
      	
    else:
        sys.stdout.write('ERR\n 密码验证失败')
    # Flush the output to stdout.
    sys.stdout.flush()
```

将`AuthPro.py`文件拷贝到squid的配置文件夹`/etc/squid`里

# 3：外部ACL辅助器

新建一个python文件`AclHelper.py`。在里面添加如下内容:

```python
#!/usr/bin/python
# coding=UTF-8
import sys
import MySQLdb
"""当用户连接squid，传递用户客户端ip地址到外部辅助器，辅助器通过标准输入读取。辅助器查询数据库符合客户端ip的所有项，如果查询结果，显示客户ip在数据库中，则输出ok，否则输出ERR"""
def matchclientip(client_ip):
    #数据库地址，数据库名，数据库密码，数据库用户名，数据库编码格式
    db=MySQLdb.connect("localhost","squid","你的数据库密码","squid",charset='utf8')
    # 使用cursor()方法获取操作游标 
    cursor = db.cursor()
    # SQL 插入语句
    sql = "SELECT * FROM sq_client_ip  WHERE client_ip = '%s'" % (client_ip)
    try:
       # 执行sql语句
       cursor.execute(sql)
       result = cursor.fetchone()
       if result:
          exist_ip = True
       else:
          exist_ip = False
    except:
       # 发生错误时回滚
       db.rollback()
       exist_ip = False

    # 关闭数据库连接
    db.close()
    return exist_ip
while True:
    # 从标准输入读取一行，“client_ip”
    line = sys.stdin.readline()
    #提取输入参数。squid，输入给辅助器的字符串是"0 xx.xx.xx.xx"格式
    #利用split分割字符串，得到0和client_ip的list
    client_ip = line.strip().split()[1]
    #判断密码是否匹配
    if matchclientip(client_ip):
        #输出ok表示验证通过
        sys.stdout.write('OK\n')
        #print会将信息输出到/var/log/squid/cache.log中。
        print("Acl辅助器验证成功")
    else:
        sys.stdout.write('ERR\n')
        print("ACL辅助器验证失败")
    # Flush the output to stdout.
    sys.stdout.flush()
```

将`AclHelper.py`文件拷贝到squid的配置文件夹`/etc/squid`里

# 4：修改squid配置文件

```shell
# http_port 设置监听端口，默认为3128
http_port 6666

#父squid配置，配合国外服务器server2使用
nonhierarchical_direct off
#转发请求通过stunnel至父squid
cache_peer localhost parent 3128 0 default
never_direct allow all

# access_log 设置access日志，daemon表示在后台将日志写入/var/log/squid/access.log文件, 
# combined是一个预定义的logformat，也可以使用自定义的logformat
access_log daemon:/var/log/squid/access.log combined
 

# ACLs all, manager, localhost, and to_localhost are predefined.
acl SSL_ports port 443
acl Safe_ports port 80		# http
acl Safe_ports port 21		# ftp
acl Safe_ports port 443		# https
acl Safe_ports port 70		# gopher
acl Safe_ports port 210		# wais
acl Safe_ports port 1025-65535  # unregistered ports
acl Safe_ports port 280		# http-mgmt
acl Safe_ports port 488		# gss-http
acl Safe_ports port 591		# filemaker
acl Safe_ports port 777		# multiling http
acl CONNECT method CONNECT
 
# 拒绝所有非Safe_ports的请求
http_access deny !Safe_ports
# 拒绝所有非SSL_prots的CONNECT请求
http_access deny CONNECT !SSL_ports

# Only allow cachemgr access from localhost
http_access allow localhost manager
#http_access deny manager
# 允许来自本地的请求
http_access allow localhost

######################################
#定义一个外部ACL辅助器
#使用python解释器，negativettl表示验证错误时的缓存时间单位秒，concurrency表示最多接收的认证通过用户，ipv4必须，如果没有ipv4会报错。
external_acl_type MyAclHelper negative_ttl=1 concurrency=30 ipv4 %SRC /usr/bin/python /etc/squid/AclHelper.py
acl MyAcl external MyAclHelper
http_access allow MyAcl

#定义一个验证器
#指定python运行目录，和python程序的目录
auth_param basic program /usr/bin/python /etc/squid/AuthPro.py
#向python程序中输入key_extras变量客户端ip"%>a"
auth_param basic key_extras "%>a"
#允许多少个子程序
auth_param basic children 30
#输入框提示信息
auth_param basic realm 输入用户名和密码
#验证信息的缓存时长
auth_param basic credentialsttl 1 second
#用户名和密码是否区分大小写
auth_param basic casesensitive on
#强制用户验证
acl authenticated proxy_auth REQUIRED
http_access allow authenticated
################################################

# 拒绝所有请求，最后兜底的规则
http_access deny all
```

> 特别需要注意的是：
>
> 1. 必须辅助器配置在前，而验证器配置在后。由于squid特殊的处理机制，当squid从配置文件指定的http_access中找到了符合的条件的allow或deny信息，则后续的判断将不会再生效，也就是说，只有在用户无法通过辅助器验证时，才会进行验证器验证。
>
> 2. 辅助器必须设置negative_ttl项，而且设置的不通过验证时间不易过长，最好在几秒内。原因是，当用户连接squid后，首先通过辅助器查找数据库是否有此ip，如果没有辅助器会缓存此ip信息，并将其标记为未通过验证，再进入验证器。而通常，我们首先于squid连接上网的并不是浏览器，可能收微博软件等，这些软件由于无法弹出验证窗口，而导致验证器验证失败。此后即便你打开浏览器，而由于上一次的验证失败而缓存了失败信息，致使，squid不会再进行辅助器和验证器验证，而直接拒绝访问。因此设置验证失败缓存时间短点，可以保证我们打开浏览器时，还能进入辅助器验证和验证器验证。
>
> 3. 验证器必须设置credentialsttl项，且设置时间不易过长，最好在几秒内。之所以验证器验证成功的缓存不能太长，是因为当我们首次通过验证器验证后，squid缓存成功验证的用户信息，下次用户则无需再次经过验证器密码验证，直接读取用户名和ip。但是由于squid缓存的是用户的ip和用户名，如果你使用了微博等没有验证框的软件，由于没有向squid发送用户名，会导致无法联网。
>
>    所以，我们利用验证器，只是为了保存成功验证的用户ip，然后通过辅助器来验证用户。所以，通过后则清除验证器的缓存。
>
>    用户再次上网，便会首先经过辅助器，此时辅助器已经能从数据库读取到刚才验证器保存的客户ip。辅助器，可以设置比较久的成功ttl时长，如果没有设置，默认是一个小时。

# 5：参考资料

《[squid中文权威指南](http://blog.zyan.cc/book/squid/)》

《[用Python脚本打造Squid权限认证后端程序以及Squid3.5 auth_param key_extras新特性介绍](http://www.site-digger.com/html/articles/20161012/132.html)》

《[使用squid搭建代理服务器](https://www.hawu.me/operation/852)》

《[Feature: Add-On Helpers for Request Manipulation](https://wiki.squid-cache.org/Features/AddonHelpers)》

《[stackoverflow上关于自定义验证器输入客户端IP的回答](https://stackoverflow.com/questions/26601943/how-to-configure-squid-to-pass-client-ip-to-authenticate-program?rq=1)》

> 最后，感谢https://www.hawu.me  站长，提供的一些帮助。


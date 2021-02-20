---
title: Oneindex+YAAW增加后台离线上传
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-25 11:07:03
password:
summary: 将Yaaw嵌入到Oneindex后台，同时，利用在服务器上部署的Aria2+Rclone，实现离线上床文件到Onedrive
tags:
 - YAAW
 - Oneindex
categories: 网站编程
---



# 一：简介

Yaaw是个开源的aria2的WebUI，通过这个UI可以使用aria2的rpc功能，向aria2发送下载某文件的指令。我将yaaw嵌入到了Oneindex后台，同时在前台也部署了yaaw供游客使用。

项目地址：https://github.com/xieqifei/OneindexN

Demo：https://pan.sci.ci

后台预览：

![](https://i.loli.net/2020/06/25/f2SZynlY4DQixwL.png)

# 二：部署网站

网站环境尽量使用nginx。后面会用nginx做反代，可以避免修改yaaw的rpc参数。

在github上将网站下载下来。可以直接下载为zip压缩包

![](https://i.loli.net/2020/06/25/T4RShHIUQ9NVEnx.png)

将文件解压到网站根目录。

进入网站首页。配置网站。

# 三：修改Nginx配置

添加

```
location /jsonrpc {
        proxy_pass http://localhost:6800/jsonrpc;
        proxy_redirect off;
        proxy_set_header        X-Real-IP       $remote_addr;
        proxy_set_header        X-Forwarded-For
        $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
	}
```

这样RPC可以配置为`http://yoursite/jsonrpc`。网站后台默认的RPC就是`http://yoursite/jsonrpc`

![](https://i.loli.net/2020/06/25/9cY2PiBr6usqXen.png)

**关闭aria2远程RPC**

仅本地也就是443转发的请求能到达6800。

```shell
vim /root/.aria2c/aria2.conf
```

修改aria2配置内容

```
# 启用RPC, 默认:false
enable-rpc=true
# 接受所有远程请求, 默认:false
rpc-allow-origin-all=false
# 允许外部访问, 默认:false
rpc-listen-all=false
# RPC监听端口, 端口被占用时可以修改, 默认:6800
rpc-listen-port=6800
```

> 如果不配置nginx，需要自己修改RPC设置。并且aria2配置需要开放远程RPC

# 四：参考资料

《[github YAAW项目](https://github.com/binux/yaaw)》

《[Issue：一旦使用HTTPS协议就无法连接](https://github.com/mayswind/AriaNg/issues/62)》

《[Nginx 反向代理 Aria2 JSONRPC](https://kenvix.com/post/nginx-proxy-aria2/)》
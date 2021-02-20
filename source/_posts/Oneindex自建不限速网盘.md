---
title: Oneindex自建不限速网盘
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-22 11:46:34
password:
summary: 使用免费的000webhost虚拟主机，搭建一个小型的在线网盘。用到github上的开源程序Oneindex，以及微软的onedrive网盘。onedrive，提供5g的容量，通过邀请好友加入可以扩容到最多15G，也可以花钱升级。网盘支持文件夹加密，README，更换主题，后台管理。
tags: Oneindex
categories: 网站编程
---

![](https://i.loli.net/2020/06/22/49JmMaXqfIx13US.png)

不占用服务器空间，不走服务器流量，  

直接列出 OneDrive 目录，文件直链下载。

# 一：注册申请000webhost免费主机

进入 https://www.000webhost.com/

注册后，在我的网站里创建新网站。

![](https://i.loli.net/2020/06/22/CpHtzoaKyFwmNeh.png)

创建成功后，打开网站后台。

点击`File Manager`，或者直接输入网址 https://files.000webhost.com 

![](https://i.loli.net/2020/06/22/SfFj2WzEMk3rxq4.png)

连接你的网站ftp

输入你的网站名称和密码

![](https://i.loli.net/2020/06/22/8dtDAonK3hqpBOS.png)

![](https://i.loli.net/2020/06/22/rImV6MD2tFXvpcB.png)

`/public_html`就是网站的根目录。将网页的index应放在此目录下

# 二：下载和上传网页

下载程序源码：https://wwa.lanzous.com/ikFHMdxgkeb

确保`public_html`目录里没有任何文件。

在网站文件管理页面，点击上传文件(upload files)的图标。将压缩包上传到`public_html`。

选择文件右键选择解压(extract)。将解压后的所有文件即index.php所在的文件目录下所有的文件移动到`public_html`

最终结果应如图所示。

![](https://i.loli.net/2020/06/22/rImV6MD2tFXvpcB.png)

# 三：绑定自己的域名

webhost给的免费域名是`webname.000webhostapp.com`，需要自行设置cname解析。解析步骤就不说了。不会的可自行百度。

cname添加后，还需要在webhost后台设置白名单才能生效。

在000webhost网站管理后台中，点击`Website Settings`->`General`。

![](https://i.loli.net/2020/06/22/7LOBsndZtkCvfxg.png)

在`Website Name`下面有一段英文，`you can change the name of your website here `。点击here。进入修改域名界面

![](https://i.loli.net/2020/06/22/pFHeLsrxBqJAQOo.png)

添加你cname解析的域名

![](https://i.loli.net/2020/06/22/Aw8iXx2IP9HbZdL.png)

# 四：安装配置oneindex

打开你的网盘网页，比如我的 http://pan.sci.ci

按照提示配置。

其中应用密钥和id，需要你点击网站给的链接获取。密钥获取结束后，点击密钥界面的回退按钮，在回退的网页里就能找到应用id。

<img width="658" alt="image" src="https://raw.githubusercontent.com/donwa/oneindex/files/images/install.gif">  

# 五：oneindex其他操作

onedrive文件管理：https://onedrive.live.com

## 5.1 计划任务  

[可选]**推荐配置**，非必需。后台定时刷新缓存，可增加前台访问的速度。  

```
# 每小时刷新一次token
0 * * * * /具体路径/php /程序具体路径/one.php token:refresh

# 每十分钟后台刷新一遍缓存
*/10 * * * * /具体路径/php /程序具体路径/one.php cache:refresh
```

## 5.2 Docker 安装运行

- 请参考[TimeBye/oneindex](https://github.com/TimeBye/oneindex)

## 5.3 特殊文件实现功能  

` README.md `、`HEAD.md` 、 `.password`特殊文件使用  

可以参考[https://github.com/donwa/oneindex/tree/files](https://github.com/donwa/oneindex/tree/files)  

**在文件夹底部添加说明:**  

>在 OneDrive 的文件夹中添加` README.md `文件，使用 Markdown 语法。  

**在文件夹头部添加说明:**  

>在 OneDrive 的文件夹中添加`HEAD.md` 文件，使用 Markdown 语法。  

**加密文件夹:**  

>在 OneDrive 的文件夹中添加`.password`文件，填入密码，密码不能为空。  

**直接输出网页:**  

>在 OneDrive 的文件夹中添加`index.html` 文件，程序会直接输出网页而不列目录。  
>配合 文件展示设置-直接输出 效果更佳。  

## 5.4 命令行功能  

仅能在PHP CLI模式下运行  

**清除缓存:**  

```
php one.php cache:clear
```

**刷新缓存:**  

```
php one.php cache:refresh
```

**刷新令牌:**  

```
php one.php token:refresh
```

**上传文件:**  

```
php one.php upload:file 本地文件 [OneDrive文件]
```


**上传文件夹:**  

```
php one.php upload:folder 本地文件夹 [OneDrive文件夹]
```

例如：  

```
//上传demo.zip 到OneDrive 根目录  
php one.php upload:file demo.zip  

//上传demo.zip 到OneDrive /test/目录  
php one.php upload:file demo.zip /test/  

//上传demo.zip 到OneDrive /test/目录并将其命名为 d.zip  
php one.php upload:file demo.zip /test/d.zip  

//上传up/ 到OneDrive /test/ 目录  
php one.php upload:file up/ /test/
```


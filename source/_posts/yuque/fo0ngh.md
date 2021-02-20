---
title: 语雀+本地hexo博客书写
urlname: fo0ngh
date: '2021-02-20 23:01:07 +0100'
tags: []
categories: []
---

我一直在寻找一种替代 Typora，能够实现在线书写个人博客的方法。一次偶然的阅读，我了解了语雀。在网上检索后发现，已经有不少教程讲解如何通过语雀的钩子+Github Action+云函数的方法，在线同步语雀文章到 github 博客。在这片文章里，我总结了，今天搭建语雀过程中遇到的坑，和解决过程。看这篇文章之前，你应该已经在搭建语雀博客同步过程中遇到了不少阻碍吧。
我本人按照《[Hexo+GitHub+Action+腾讯云函数+语雀搭建自动化个人博客](https://www.kikohk.top/article/3899/)》文章的教程进行搭建，所以细节不再赘述。遇到的坑，将在下面说说。

# 1：Hexo 本地博客

我很早就开始在本地计算机上使用 Typora+markdown 写博客了。早已习惯了 Typora 的风格和快捷键。通过 win+shift+s 截屏，然后直接粘贴到 markdown 中，Typora 通过设置连接图传，自动上传，对于图片的粘贴非常友好。。目前还不知道语雀是否能够做到。
将 Hexo 博客安装到本地，是为了获取博客文件目录，修改主题，安装插件等等。同时也能够本地写作。
安装 yuque-hexo 插件后，需要在 package.json 中添加插件配置。原文推荐如下：

```yaml
"yuqueConfig":
  {
    "baseUrl": "https://www.yuque.com/api/v2",
    "login": "语雀的id",
    "repo": "语雀知识库名称",
    "mdNameFormat": "title",
    "postPath": "source/_posts",
    "onlyPublished": false,
  }
```

但是这个配置，会将我们本地编写的博客覆盖掉，如果你过去在 hexo 本地编写过，那你应该知道，本地博文同样储存在**source/\_posts**。为了避免语雀同步后，覆盖原本地文章，需要将上述配置中的**"postPath": "source/\_posts"**，修改为**"postPath": "source/\_posts/yuque"。**

# 2：Hexo 同步 Github 仓库

将整个博客目录同步到 Github 仓库，这样博客的许多重要配置文件，主题设置，文章 md，页面设置都将得到保存，同时可以调用这个仓库的 actions 功能生成静态文件，将博客页面同步到 github pages。。值得注意的是，Github 免费仓库是公开的，你保存在仓库的 token 将会被别人知道，这会给博客一些依赖 token 功能，比如评论、百度 seo 带来威胁，当然我们希望人们总是善良的。
关于同步，在配置**.gitignore**文件时，建议添加**public**和**node_modules**这两个文件夹，public 是动态生成的，node_module 是 hexo 依赖，也可以动态生成，actions 都能够自动生成，因此不需要额外同步上去。
themes 文件夹下，是博客的主题，主题通常是从一个 github 仓库克隆过来的，在同步 hexo 博客时，如果主题文件夹下存在.git 文件夹，则这个文件夹将被认为是博客仓库的子仓库，因此不会被上传。在 github 仓库中显示为**一个带右箭头的文件夹**。
如果要将主题上传到 github，需要删除主题文件夹下的.git 文件夹。
此外运行 git 指令

```bash
git rm -r --cache .
```

此命令清除 git 缓存
在 push 的时候使用强制 push

```bash
git push -f
```

# 3：Action

通过 Action，github 可以循行 hexo 指令，比如将语雀文章同步到 source/\_post/yuque，生成静态文件，上传 github pages 等等。
目前这个 action 程序，还没有实现将语雀文章同步到 github 储存库的能力，只能本地同步，再本地同步到 github。希望后人能够补全功能。

# 4：云函数

无论是腾讯云、阿里云还是谷歌亚马逊，都有云函数这个功能，使用上面也是大同小异。云函数的功能，在这里主要是为了接收语雀的回调，换句话说，每当我们更新删除新建知识库文章时，语雀就会向一个指定的 url 发起请求，云函数就用来接收这个请求。
云函数接收到请求后，调用 github 的 api，运行 action，action 再执行 hexo 指令部署博客。
云函数内容：

```python
# -*- coding: utf8 -*-
import requests

def main_handler(event, context):
    r = requests.post("https://api.github.com/repos/你的github用户名/源码仓库名/dispatches",
    json = {"event_type": "start"},
    headers = {"User-Agent":'curl/7.52.1',
              'Content-Type': 'application/json',
              'Accept': 'application/vnd.github.everest-preview+json',
              'Authorization': 'token github的token'})

    if r.status_code == 204:
        return "This's OK!"
    else:
        return r.status_code

```

需要注意的是， **'Authorization': 'token github 的 token'**其中 token 是一串乱序字符串，在这串字符串前必须有“**token **”这个字串，否则测试时会返回 404。

> 'Authorization': 'token 77ed8fdca6**\*a00f0\*\*\***d73239790c6323dd'

# 5：设置语雀 hook

在语雀的钩子中，将腾讯云函数的触发 api 填写进去，就可以尽情享受本地+在线写作的快乐了。

# 6：参考资料

《[Hexo+GitHub+Action+腾讯云函数+语雀搭建自动化个人博客](https://www.kikohk.top/article/3899/)》
《[语雀云编辑自动部署 Hexo 博客](https://www.yuque.com/gavinluo/blog/yuque-auto-2-hexo)**》**
**《**[语雀自动同步到 hexo 博客](https://cloud.tencent.com/developer/article/1665753)**》**
**《**[语雀文章用 Serverless 自动部署到 Hexo 博客](https://www.tangruiping.com/post/yque-serverless-github-actions-hexo.html)**》**

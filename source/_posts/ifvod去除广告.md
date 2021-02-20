---
title: ifvod去除广告
top: false
cover: false
toc: true
mathjax: false
date: 2021-02-16 10:55:46
password:
summary:
tags: Javascript
categories: 网站编程
---

ifvod视频会在视频的开始、25分钟、50分钟的时候插入广告，现在仅需简单的两步，就能去除ifvod播放广告。

# 1：安装浏览器插件

点击下面的链接，将ReRes插件`添加至Chrome`。

[https://chrome.google.com/webstore/detail/reres/gieocpkbblidnocefjakldecahgeeica](https://chrome.google.com/webstore/detail/reres/gieocpkbblidnocefjakldecahgeeica)

安装好后，在浏览器拓展程序中，将出现ReRes插件。

在浏览器地址栏输入

```
chrome://extensions/
```

查看插件，确保插件已经激活，激活时，右下角的`滑动按钮`状态如图所示。

![image-20210216094238426](https://i.loli.net/2021/02/16/JsmNcldYvGoenX9.png)

点击浏览器窗口右上角的ReRes图标，应该会出现下图所示的弹窗。当然此时应当还没有任何规则。

![image-20210216093951914](https://i.loli.net/2021/02/16/3OHyDYPJz1WrvCb.png)

# 2：添加规则

如图所示，点击`添加规则`按钮

![image-20210216094503703](https://i.loli.net/2021/02/16/guW3XIzaKTricwv.png)

在`If URL match:`一栏填入

```
https://www.ifvod.tv/7-es2015.5cef6194389ec0967828.js
```

在`Response:`一栏填入

```
https://qn.xieqifei.com/7-es2015.5cef6194389ec0967828.js
```

点击保存。

**至此，你在ifvod观看视频，将不会有任何视频广告插入。**

# 3：原理

ifvod广告是通过js代码嵌入到播放器中的。我仔细阅读了，它的播放器代码，也就是，上面替换掉的js文件，我修改了其中，关于广告投放部分的代码。。

ReRes插件是我在网上随便找的一个插件，它的作用是，当浏览器请求一个资源时，如果它符合我在ReRes上设定的url规则，就将它重定向到另一个资源文件。这样就能实现篡改ifvod的播放器源代码。

也许你可以通过对比修改前后的js文件，来了解我具体是如何修改的。。不过，我并不推荐你阅读这个两万行代码的文件，它会让你崩溃。。我也没有完全阅读这些代码，而是通过一些逆向工程的推断，来找到核心代码，这需要一些耐心。


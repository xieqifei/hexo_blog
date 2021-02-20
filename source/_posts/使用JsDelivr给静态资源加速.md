---
title: 使用JsDelivr给静态资源加速
top: false
cover: false
toc: true
mathjax: false
date: 2020-07-11 22:37:07
password:
summary: 利用JsDelivr使github上托管的静态资源获得CDN加速。
tags: JsDelivr
categories: 网站编程
---



github仓库里的js、css静态资源是无法被直接放在网页的静态资源url里的，因此，需要借助第三方工具来引用。

这里使用JsDelivr。

### 说明

打开[jsDeliver官网](https://www.jsdelivr.com/)，可以看到使用方式：

```
// load any GitHub release or commit

// note: we recommend using npm for projects that support it

https://cdn.jsdelivr.net/gh/user/repo@version/file
```

> user：github用户名
>
> repo：仓库名
>
> version：(Tag)版本名
>
> file：文件路径

### 步骤

第一步：将静态资源push到仓库

第二步：release一个版本

第三步：根据上面的cdn方式，获取静态资源cdn资源。

### 参考资料

《[使用Github托管js和css文件](https://sparkydogx.github.io/2018/10/12/use-github-js-css/)》
---
title: 自定义Volantis主题页脚
top: false
cover: false
toc: true
mathjax: false
date: 2020-10-10 20:04:58
password:
summary:
tags: Volantis
categories: 网站编程
---



修改主题文件下的`_config.yml`

找到页脚部分：

```yaml
############################### Site Footer ############################### > start
site_footer:
  # layout of footer: [aplayer, social, license, info, copyright,analytics]
  layout: [custom]
  #custom
  custom: '<a style="padding-right: 1%;" href="https://hexo.io/"><img src="https://img.shields.io/badge/Powered-Hexo-red"></a><a style="padding-right: 1%;" href="https://volantis.js.org/"><img src="https://img.shields.io/badge/Theme-Volantis-blue"></a><a style="padding-right: 1%;" href="https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh"><img src="https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-success"></a><a style="padding-right: 1%;" href="https://sci.ci"><img src="https://img.shields.io/badge/Copyright-2019--2020%20QF-orange"></a>'
  br: '<br>'
############################### Site Footer ############################### > end
```

修改后效果：

![](https://i.loli.net/2020/10/11/EpAmYXzWi3OGDHc.png)
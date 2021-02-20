---
title: 树莓派Chrome浏览器驱动webdriver
top: false
cover: false
toc: true
mathjax: false
date: 2020-09-21 22:01:57
password:
summary:
tags: Selenium
categories: 树莓派
---

树莓派上的系统是基于Arm内核的Linux，且是32位系统，在Google上面下载的chromedriver在selenium运行时，会报错，可以直接使用apt-get安装chromedriver。

安装命令：

```shell
sudo apt-get install chromium-chromedriver
```

会自动识别树莓派上原有的chrome浏览器版本。

在python程序中，可直接使用`webdriver.Chrome()`而无需再指定驱动路径。
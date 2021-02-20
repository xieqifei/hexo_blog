---
title: httponly的Cookie
top: false
cover: false
toc: true
mathjax: false
date: 2020-07-22 17:24:14
password:
summary: 今天打算写一个网站的爬虫，但是网站需要登陆才能爬取，我想可以直接发送cookie而跳过登陆，但是在浏览器端使用JS指令获取cookie时，居然是空。然后查阅资料后，了解了cookie还存在一种httponly形式的cookie。
tags: Cookie
categories: 网站编程
---





JS获取cookie的程序是`document.cookie`，但是为了网站安全。服务器可以设置某些cookie不能使用js获取。这种类型的cookie即使httponly的cookie，而且只有服务器能发送此类cookie。

```php
setcookie("abc", "test", NULL, NULL, NULL, NULL, TRUE); 
```

在最新php中设置cookie的httponly属性，需要将第七个值设置为true。

在浏览器如何获取httponly属性的cookie呢。需要进入控制台，打开`Application`

![image-20200722173318918](https://i.loli.net/2020/07/22/rxhTneIsZE1cJzq.png)

设置httponly属性，对于cookie安全非常重要，这样可以防止XSS攻击，攻击者通过植入JS代码，获取网站的cookie，从而以用户身份浏览服务器。

另外设置cookie过期时间与会话Session时间相同，也能有效保护用户信息安全。
---
title: BLOG双线部署+https
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-14 13:53:54
password:
summary: 将Hexo博客分别部署到github和coding，并使用dns.la分海外和国内分别解析。之前使用hexo博客时，就打算使用双线部署，但当时在coding申请ssl证书一直失败。加上一直没法把域名dns修改为dnspod（dnspod支持多线解析），所以放弃了。这次换了个dns服务商，并且成功申请了ssl证书。
tags: 网站优化
categories: 网站编程
---

dns.la支持多线解析，而且免费，非常好用。推荐！

关于证书申请的方式，就不多说了。之前在coding申请证书失败，是因为域名cname解析没有配置正确。将域名服务商修改为 https://dns.la 后，需要先将域名海外线路解析到coding。

> 将海外线路解析到coding是，coding证书能够申请成功的关键！
> 在github申请证书时，也需要将海外线路解析到github。

证书申请完毕后，可以在dns.la上修改dns线路，国内解析到coding，海外解析到github。
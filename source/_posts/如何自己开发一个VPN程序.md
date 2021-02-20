---
title: 如何自己开发一个VPN App
top: false
cover: false
toc: true
mathjax: false
date: 2019-12-12 21:34:16
password:
summary: 基于github上的一个开源vpn安卓客户端，修改其样式代码，来个性化自己的VPN。这个VPN基于openvpn库。
tags: VPN
categories: 安卓编程
---

# 第一章 简介

## 1.1 学习条件和要求

学习条件：

- 你不用一定学习过JAVA或其他编程语言。
- 你不用一定了解或精通安卓编程。
- 你不用一定懂得Linux编程。
- 你需要自费支付服务器费用，国外服务器大概一个月30元，也可能有便宜的或更贵但性能更好的，你可自行选择，当然如果愿意用国内服务器练手，腾讯阿里的学生服务器一年只要120，当然这个服务器做出的VPN无法翻墙。
- 你需要消耗一定的时间和耐心。

如果你已经满足以上条件了，那么我希望你在浏览以下文章时，同意我的以下要求：

- 你保证不使用本教程制作的VPN进行牟利。
- 你保证不在中国大陆地区散播和分享制作的VPN。
- 你保证不使用此VPN进行其他违法犯罪活动或在外网发表对国家不利的言论。

> 若你已同意以上要求，请继续向下浏览，若你无法做到上述要求，请停止继续浏览。

## 1.2 功能介绍

软件的实现原理，我不做介绍，有兴趣的同学可自行学习。

这里只教大家如何在我的软件基础上通过简单修改，制作一个自用VPN客户端。当然你也可以使用SS、SSR或V2ray等现成软件，并配置服务器后翻墙。

VPN功能：

- 可配置多条线路，以适用不同的服务器，或同一服务器上的不同用户。
- 客户端主动更改主题颜色。
- 推送更新通知，和推送广告。可在服务端更改是否推送广告。

本软件没有账户登陆等限定条件。只要安装软件，即可翻墙，当然前提条件是，你配置好了服务器。

下面展示我已经制作好的VPN程序截图，测试已能翻墙。

<img src="http://qn.xieqifei.com/static/images/ecaoovpn1.jpg" style="zoom:25%;" />

<img src="http://qn.xieqifei.com/static/images/ecaoovpn2.jpg" style="zoom: 25%;" />

<img src="http://qn.xieqifei.com/static/images/ecaoovpn3.jpg" style="zoom:25%;" />

<img src="http://qn.xieqifei.com/static/images/ecaoovpn4.jpg" style="zoom:25%;" />

如此，我们便开始制作自己的VPN吧。

# 第二章 服务端配置

## 2.1 VPS服务器购买

这里我推荐给大家的是[vultr.com](https://www.vultr.com/?ref=8345070)。为什么这家服务器呢？这家服务器可以按小时收费，如果你不嫌麻烦，可以在需要的使用VPN的时候，再开启一台VPS。不需要的时候关闭，当然重新开启需要重新配置服务器等操作。只是我觉得性价比挺高。最重要的是支持支付宝微信付款！

下面教大家购买VPS服务器。主要是没看到中文翻译，所以决定一步步教大家，毕竟有的人可能并不会。

1. 点击链接打开vultr网站，[www.vultr.com](https://www.vultr.com/?ref=8345070)额外说明一句，通过我的链接打开注册，我和你都可以获得10美元的奖励费用，当然这笔钱，需要你购买服务器之后才会发放。只是给大家说明这点，当然如果大家有其他选择也是可以的。

2. 创建账户。

   ![](http://qn.xieqifei.com/static/images/vultr-step1.jpg)

   填写邮箱，按要求设置密码，密码要求至少一个大写字母，一个小写字母，一个数字，和至少十个字符。点击“create acounnt”即可。

3. Vultr充值。

   注册登录后会进入个人面板，左边栏选择 **“Billing”** ，选择支付方式和金额，付款即可。

   Vultr 支持支付宝付款，很方便。但是个人更推荐有 Paypal 的使用 Paypal。

   ![](http://qn.xieqifei.com/static/images/vultr-step2.jpg)

4. 创建VPS

   选择 **“Servers”** ，点击右边蓝色的 “**+**” 添加 VPS。

   ![](http://qn.xieqifei.com/static/images/vultr-step3.jpg)

   **a.选择节点**

   日本距离最近，延迟最低，但太热门了，白天还好，晚上有时很慢；洛杉矶比日本稳些。

   ![](http://qn.xieqifei.com/static/images/vultr-step4.jpg)

   **b.选择系统**

   选择系统。支持 Windows 和自己上传 ISO 系统镜像。这里请大家选择**CentOS系统7x64**

   ![](http://qn.xieqifei.com/static/images/vultr-step5.png)

   **c.选择套餐**

   价格从 $2.5/mo 到 $640/mo，各种配置都有，最低 $2.5/mo 的套餐现在基本买不到了。要求必须**有IPv4地址**。

   ![](http://qn.xieqifei.com/static/images/vultr-step6.jpg)

   **d.其他选项**

   有 IPv6，私密，主机名、标签等，**无特殊需要全部默认即可。**

   选好后点击右下角的 **“Deploy Now”** ，开始安装 VPS。

   **e.查看VPS**

   已购买的 VPS 都会显示在 “Servers” 面板中，当显示 “Running” 时即表示安装完成。点击 **“Manage”** 进入 VPS 管理面板。

   ![](http://qn.xieqifei.com/static/images/vultr-step7.jpg)

   VPS 管理面板，这里需要记录下 IP 地址（IP Address）、用户名（Username）、密码（Password）供稍后连接 VPS 时使用。

   ![](http://qn.xieqifei.com/static/images/vultr-step8.jpg)

5. 连接VPS

   使用 ssh 工具连接我们的 VPS，Windows 推荐 xshell，Mac 可使用自带的终端。

   Xshell 网盘链接：[https://pan.baidu.com/s/1pMbKLMb](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/1pMbKLMb) 密码：lwir

   **Windows**

   安装 xshell，打开软件。

   点击 “文件” —> “新建” —> “连接”，输入 “名称” 和 “主机” （即VPS ip）。

   ![](http://qn.xieqifei.com/static/images/vps-step1.jpg)

   点击 “用户身份验证”，输入用户名（默认root）和密码。确认。

   ![](http://qn.xieqifei.com/static/images/vps-step2.jpg)

   连接 VPS，接受并保存密匙。

   ![](http://qn.xieqifei.com/static/images/vps-step3.jpg)

   ![](http://qn.xieqifei.com/static/images/vps-step4.jpg)

   出现 `root@vultr:~#` 即连接成功，可以输入代码了进行操作了。

   ![](http://qn.xieqifei.com/static/images/vps-step5.jpg)

   > 很重要的一点，因为不是所有的VPS在国内都能ping通，有的IP地址已经被国内网络屏蔽了，所以如果VPS连接不上，请你到 [ping.chinaz.com]输入你的IP地址测试，若国内连接均提示超时且为红色，请你在vultr中删除vps并重新创建一个可用的ip地址。

   ![](http://qn.xieqifei.com/static/images/vps-step7.jpg)

   **Mac**

   打开终端，输入以下代码登录 VPS，其中 root 即用户名，将 ip 更改为 VPS 的 IP 地址，回车。

   ```java
   ssh root@ip
   ```

   输入 `yes` 确认，粘贴密码，回车。需要提醒的是，密码输入时并不会显示出来，直接复制粘贴，回车即可。出现 `root@vultr:~#` 即连接成功。

   ![](http://qn.xieqifei.com/static/images/vps-step6.jpg)

   

   **最近很忙，没时间更新了**

项目地址：https://github.com/xieqifei/openvpn。然后有英语版本的readme。
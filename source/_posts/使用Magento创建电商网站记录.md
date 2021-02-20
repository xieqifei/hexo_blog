---
title: 使用Magento创建电商网站记录
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-24 23:00:48
password:
summary: Magento是一个开源的购物商城，由于它高度的可重写性，受到了众多商城构建者的青睐。然而由于它庞大的体积，对服务器的要求非常高。本文介绍了，我从magento安装到放弃的全过程。
tags: Magento
categories: 网站编程
---

# 0：绪论

安装教程使用magento中国官网的提示，教程原地址为https://www.magentochina.org/blog/magento-guide/install-magento-2-1-with-composer.html

我是用composer安装magento，composer在宝塔面板安装php时已默认安装了，所以能直接用。若未使用宝塔面板需优先安装composer。使用以下命令下载运行composer。若已有则跳过。

`curl -sS https://getcomposer.org/installer | php`

使用以下命令将composer添加至全局，以便在任意目录下使用composer指令

`mv composer.phar /usr/local/bin/composer`

# 1：php环境配置

一下内容的操作，在宝塔控制面板，软件商店，运行环境，php设置里能找到

**Magento安装时PHP环境需要检查以下扩展：**

- PHP Extension curl.
- PHP Extension iconv.
- PHP Extension mbstring.
- PHP Extension dom.
- PHP Extension hash.
- PHP Extension openssl.
- PHP Extension xmlwriter.
- PHP Extension pcre.
- PHP Extension gd.
- PHP Extension bcmath.
- PHP Extension simplexml.
- PHP Extension spl.
- PHP Extension xsl.
- PHP Extension intl.
- PHP Extension ctype.
- PHP Extension pdo_mysql.
- PHP Extension soap.
- PHP Extension zip.
- PHP Extension phar.
- PHP Extension libxml.

 **取消函数禁用**

- openlog、syslog、symlink、exec等
- 还有些函数需要取消禁用，可根据安装时的错误提示操作

 

# 2：开始安装

打开xshell或其他ssh面板，在使用cd指令，进入网站根目录，例如/var/www，输入指令

`composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition  magento2`

其中magento2指，网站将被放在/var/www/magento2目录下，删除此字段，magento会放在/var/www目录下。

提示输入username和password。

1. 首先您先登录[Magento的官方商店](https://marketplace.magento.com/)。
2. 点击“Sign In”登录到您的账号，如果还没有账号,需要先注册。
3. 当登录之后，点击"My Access Keys".如果找不到，可以使用CTRL+F键进行搜索.
4. 点击“Create a New Access Key”后，找到“Generate New”,为自己的Magento2生成一个Key.
5. 记录下生成的Public key及Private key

其中public key是用户名，private key是密码。

完成安装后在网站目录下就没能看到magento2目录了，由于magento需要文件写权限。使用composer安装时，magetno文件的所有者是运行指令时的用户，也就是说通常是root。那么假定网站用户是www，则在运行安装程序时，www将无法对文件进行写操作，因此，需要更改文件权限，或者所有者。。不建议将文件权限修改，我们只需要修改文件所有者即可。。

使用宝塔面板可直接在文件里修改所有者。xshell使用chmod指令，详细方法请自行查阅。**请注意：文件所有者不更改，打开http://域名/magento2，无法进入安装界面**

修改完毕后就可以直接输入http://域名/magento2/setup进行安装了。如果你遇到这个问题

**长时间安装不上，服务器cpu过载问题**

请尝试使用指令安装

首先将magento添加到环境变量,请自行修改自己的magento2文件夹位置。

`export PATH=$PATH:/var/www/html/magento2/bin`

然后cd到magento2安装目录下

```
php setup/index.php
--db_host=localhost \
--db_name=magento2 \
--db_user=root \
--db_pass=canbempty \
--backend_frontname=admin \
--admin_username=username \
--admin_password=password \
--admin_email=admin@email.local \
--admin_firstname=First \
--admin_lastname=Last \
--base_url=http://magento2.local/ \
--base_url_secure=http://magento2.local/ \
--language=en_US \
--currency=USD \
--use_rewrites=true \
--use_secure=false \
--use_secure_admin=false \
--cleanup_database
```

上面的参数请根据自己的需要进行修改,比如base_url就是您的网址,admin_*将是您后台管理员用户名或密码.

等待安装如果遇到提示

```
ERROR 1419 (HY000): You do not have the SUPER privilege and
binary logging is enabled (you *might* want to use the less safe
log_bin_trust_routine_creators variable)
```

请在你的系统中查找my.cnf文件，通常在/etc/my.cnf下，文件中 **[mysqld]** 标记后加一行内容为**log-bin-trust-function-creators=1**

然后运行

`service mysql restart`

重启mysql。然后重新**php setup/index.php**以及其后指令。完成安装。

```
[SUCCESS]: Magento installation complete.
[SUCCESS]: Magento Admin URI: /admin_xgswzt
Nothing to import.
```

出现此提示，则完成安装。

然后修改网站伪静态规则，Nginx：

```
location / {
        index index.html index.php; ## Allow a static html file to be shown first
        try_files $uri $uri/ @handler; 
        expires 30d;
    }

location /var/export/ { ## Allow admins only to view export folder
    auth_basic           "Restricted"; ## Message shown in login window
    auth_basic_user_file htpasswd; ## See /etc/nginx/htpassword
    autoindex            on;
}
  location  /. { ## Disable .htaccess and other hidden files
    return 404;
}

location @handler { ## Magento uses a common front handler
    rewrite / /index.php;
}

location ~ .php/ { ## Forward paths like /js/index.php/x.js to relevant handler
    rewrite ^(.*.php)/ $1 last;
}
```

# 3： 宝塔面板安装失败

建议不要采用宝塔面板安装，在使用宝塔面板进行安装后，网站无法运行，查看错误日志，提示**http://域名/magento2/magento2文件不存在**

修改网站根目录还是无法进入，提示

`An error has happened during application run. See exception log for details.`


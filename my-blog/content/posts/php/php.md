---
title: "PHP"
date: 2021-11-07T17:30:32+08:00
lastmod: 2021-11-07T17:30:32+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "php"

tags: ["php", "linux"]
categories: ["php"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: false
---

## 设置rpm源

`rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`
`rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm`

## 清理旧PHP

`yum -y remove php*`

## 安装

+ 安装EPEL yum存储库

`yum install epel-release -y`

+ 安装Remi存储库

`rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm`

+ 安装PHP73

```shell
yum install -y php73-php-fpm php73-php-cli php73-php-bcmath php73-php-gd php73-php-json php73-php-mbstring php73-php-mcrypt php73-php-mysqlnd php73-php-opcache php73-php-pdo php73-php-pecl-crypto php73-php-pecl-mcrypt php73-php-pecl-geoip php73-php-pecl-swoole php73-php-recode php73-php-snmp php73-php-soap php73-php-xmll php73-php-simplexml
```

## 服务

+ 开启开机自启

`systemctl enable php73-php-fpm`

## 配置文件

+ 查找php.ini位置：
  `find /etc/opt/remi/php73 -name php.ini`

+ The current PHP memory limit is below the recommended value of 512MB.

        `vim /etc/opt/remi/php73/php.ini`
        memory_limit = 512M

+ 如果你运行的是 nginx 而不是 apache，修改
  `vim /etc/opt/remi/php73/php-fpm.d/www.conf`
  user = apache
  group = apache

+ Replace the values with
  user = nginx
  group = nginx

+ 查找 php 和扩展的安装包：
  `rpm -qa | grep 'php'`

+ 查看 php73-php-pecl-swoole4-4.4.15-1.el7.remi.x86_64 的安装路径：
  `rpm -ql php73-php-pecl-swoole4-4.4.15-1.el7.remi.x86_64`

## 设置PHP

+ 将php73链接到系统环境变量中，就可以使用 php -v
  `ln -s /opt/remi/php73/root/usr/bin/php /usr/bin/php`

+ 将 cgi.fix_pathinfo 设置为 0，潜在威胁
  `sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/' /etc/opt/remi/php73/php.ini`

+ 9000端口占用
  
  ```shell
  netstat -ntlp | grep 9000
  ps -aux | grep pool
  kill [***]
  ```
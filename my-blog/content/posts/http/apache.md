---
title: "Apache"
date: 2021-11-07T17:30:00+08:00
lastmod: 2021-11-07T17:30:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "Apache安装"

tags: ["nginx", "linux"]
categories: ["http"]

lightgallery: true
hiddenFromHomePage: true

toc:
  enable: false
  auto: false
---

## 设置rpm源
`rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm`  
`rpm -Uvh https://mirrors.tuna.tsinghua.edu.cn/ius/ius-release-el7.rpm`

## 安装
`yum -y install httpd24u`

## 查看
`rpm -qa | grep httpd`


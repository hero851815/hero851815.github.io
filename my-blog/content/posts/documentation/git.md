---
weight: 2
title: "Git 常用命令"
date: 2021-11-12T16:26:32+08:00
lastmod: 2021-11-12T16:326:32+08:00
draft: false
author: "hero851815"
authorLink: ""
description: ""
featuredImage: "/featuredImage/git-featured-image.jpg"
featuredImagePreview: "/featuredImage/git-featured-image.jpg"

tags: ["git"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: false

toc:
  enable: false
  auto: false
---

## 新建代码库
### 1.项目目录
`cd /user/mydev`

### 2.创建空的本地仓库
`git init`

### 3.将项目所有文件添加到缓存
`git add .`

### 4.将缓存中的文件提交到git库
`git commit -m "添加此次更新的内容备注"`

### 5.本地库链接到远程仓库
`git remote add origin HTTPS链接`

### 6.上传代码到远程库
`git push origin master`
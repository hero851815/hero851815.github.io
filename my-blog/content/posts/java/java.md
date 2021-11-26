---
title: "Java 学习笔记"
date: 2021-11-18T15:23:00+08:00
lastmod: 2021-11-18T15:23:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "java"

tags: ["java"]
categories: ["java"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: false
---

## 工程项目
### Maven
+ 1. 右键工程创建Maven项目，编辑pom.xml加载第三方依赖
+ 2. 在resources目录下创建application.yml，建议用yml，配置参数
+ 3. 新建Run/Debug Configurations-Application，配置Main Class，Use classpath of modules
+ 4. 在Files-Project Structure修改为适合的Project SDK

### Spring Initializr
+ 1. 输入Group和Artifact，Java Version等
+ 2. 在Web中选择Spring Web
+ 3. 在resources目录下创建application.yml，建议用yml，配置参数

## Lambda
Lambda表达式，只能是接口的创建，并且这个接口只能包含一个方式
### 标准格式
(参数类型 参数名称) -> (代码语句)
+ 1. 一些参数
+ 2. 一个箭头
+ 3. 一段代码
格式说明
+ 小括号内的语法与传统方法参数列表一致；无参则留空；多个参数用逗号分隔
+ ->是引入的语法格式，代表指向动作
+ 大括号的语法与传统方法要求基本一致

## Mybatis
### #和$的区别
+ #{} 防止SQL注入，它会将所有传入的参数作为一个字符串来处理
+ ${} 将传入的参数拼接到SQL上去执行，一般用于表名和字段名参数
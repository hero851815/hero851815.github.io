---
title: "Jupyter Notebook 使用"
date: 2021-11-12T16:26:00+08:00
lastmod: 2021-11-12T16:26:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: ""

tags: ["jupyter notebook", "python"]
categories: ["python"]

lightgallery: true
hiddenFromHomePage: true

toc:
  enable: false
  auto: false
---

{{< admonition tips "什么是Jupyter Notebook">}}
Jupyter Notebook是基于网页的用于交互计算的应用程序。其可被应用于全过程计算：开发、文档编写、运行代码和展示结果。
{{</ admonition >}}

文档是保存为后缀名为.ipynb的JSON格式文件，不仅便于版本控制，也方便与他人共享

## 环境配置
### 安装
### 使用Anaconda安装
Anaconda一般预安装了Jupyter Notebook，或者在终端安装`conda install jupyter notebook`
### 使用pip安装
+ 将pip升级到最新版本  
`pip3 install --upgrade pip`
+ 安装Jupyter Notebook  
`pip3 install jupyter`

___

## 运行Jupyter Notebook
### 查看
`jupyter notebook --help`

### 启动
#### 默认端口启动
`jupyter notebook`
#### 指定端口启动
`jupyter notebook --port <port_number>`

### 设置
#### 生成配置文件
`jupyter notebook --generate-config`该命令用于创建文件并显示配置文件路径，若已经生成此文件再次运行时选择"N"，否则会覆盖原有配置
#### 编辑配置文件
编辑文件jupyter_notebook_config.py，搜索"c.NotebookApp.notebook_dir"，取消注释，自定义目录路径
#### 生成密码
+ 打开notbook，执行以下命令
  ```
  from notebook.auth import passwd
  p = passwd()
  print(p)
  ```
+ 输入两次密码，生成sha1字符串，将整段字符串复制到配置文件中"c.NotebookApp.password"
+ 重启notebook

### 扩展
#### 安装nb_conda
关联Jupyter Notebook和conda的环境和包, 安装后会多一个"conda"类目  
`conda install nb_conda`
#### 安装Markdown扩展
安装后会多一个"Nbextensions"类目，并勾选"Table of Contents"  
`conda install -c conda-forge jupyter_contrib_nbextensions`  
若安装后发现没有很多配置项  
`jupyter contrib nbextension install --user --skip-running-check`

___

## JupyterHub
### 安装
#### 更新pip
`python -m pip install --upgrade pip`
#### pip安装(推荐)
`python -m pip install jupyterhub`  
`npm install -g configurable-http-proxy`
#### conda安装
`conda install -c conda-forge jupyterhub`

### 设置
#### 生成配置文件
`jupyterhub --generate-config`
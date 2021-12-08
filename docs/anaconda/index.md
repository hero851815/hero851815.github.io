# Anaconda 使用


{{< admonition tips "什么是Anaconda">}}
Anaconda ([清华镜像](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)) 就是可以便捷获取包且对包能够进行管理，同时对环境可以统一管理的发行版本。Anaconda包含了conda、Python在内的超过180个科学包及其依赖项。
{{</ admonition >}}

## 环境配置

### 安装

#### Windows

##### 使用Anaconda安装

从清华镜像中下载最新版Anaconda，安装时**不要**勾选**Add Anaconda to my PATH environment variable**，勾选可能会影响其他应用使用。

##### 终端

安装完成后，右键管理员方式打开Anaconda Prompt终端

#### Linux

在[官网](https://www.anaconda.com/download/#macos)下载客户端，安装完成后，配置环境变量

`vim ~/.bash_profile`中添加`EXPORT PATH=$PATH:/opt/anaconda3/bin`

### python环境

#### 新建一个python环境

`conda create -n myenv python=3.6`

#### 进入环境

`activate myenv`

#### 离开环境

`deactivate`

#### 列出清单

`conda env list`

#### 删除环境

`conda env remove -n myenv`

### 包

#### 使用conda命令安装包

`conda install xxx`

#### 卸载包

`conda remove xxx`

#### 使用pip命令

`python -m pip install xxx`

#### 卸载包

`pip uninstall xxx`

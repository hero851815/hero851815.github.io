# MyBlog

#### 软件架构
Hugo +  Giteepages搭建的Blog,主题LoveIt


#### 安装教程

1.  安装Hugo  
以windows为例，从[Hugo Releases](https://github.com/spf13/hugo/releases)下载对应版本的hugo压缩包，解压之后将里面的hugo.exe复制到环境变量PATH中任意目录下，或者在PATH中添加hugo.exe所在的路径后，打开cmd，执行hugo version，如果显示hugo的版本号，则已完成hugo的安装。

2.  安装主题  
[下载主题](https://github.com/dillonzq/LoveIt)，解压至themes目录

3.  本地启动  
`hugo serve -e development`  
指定端口启动，`hugo serve -e development <port>`

4.  构建网站  
`hugo -e production`  
也可以使用`hugo -d C:/hugo/path/my-blog-public -e production`指定生成public目录路径

#### 基础配置

1.  修改/path/my-blog/config/development/config.toml

#### 使用说明
1.  友情链接  
示例:  
`{{< friend name="Dillon" url="https://github.com/dillonzq/" logo="https://avatars0.githubusercontent.com/u/30786232?s=460&u=5fc878f67c869ce6628cf65121b8d73e1733f941&v=4" word="LoveIt主题作者" >}}`

#### 发布至git
<!--修改config.toml中的baseURL为Gitee Pages服务地址-->构建网站后上传

# Apache


## 设置rpm源
`rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm`  
`rpm -Uvh https://mirrors.tuna.tsinghua.edu.cn/ius/ius-release-el7.rpm`

## 安装
`yum -y install httpd24u`

## 查看
`rpm -qa | grep httpd`



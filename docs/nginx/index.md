# Nginx


## 安装扩展插件
`apt-get update`  
`apt-get install vim`

## 1 安装nginx(容器方式)
### 拉取镜像
`docker pull nginx:1.21.1`

### 查看本地镜像
`docker image`

### 首次创建ngixn容器
`docker run -d --name nginx-web nginx`

### 创建nginx目录
```shell
mkdir -p /var/log/nginx
mkdir -p /etc/nginx
mkdir -p /usr/share/nginx/html
```

### 复制容器中的配置文件至目录
```shell
docker cp [容器id]:/etc/nginx ./conf
docker cp [容器id]:/usr/share/nginx/html ./html
```

### 停止并删除容器
```shell
docker stop nginx-web
docker rm nginx-web
```

### 创建正式容器
```shell
docker run -d \
  --name nginx-web \
  -p 80:80 \
  -p 443:443 \
  -v /usr/share/nginx/html:/usr/share/nginx/html \
  -v /etc/nginx/conf/conf.d:/etc/nginx/conf/conf.d:ro \
  -v /etc/nginx/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
  -v /var/log/nginx:/var/log/nginx \
  nginx
```
### 查看容器IP
`docker inspect [dockername]`

___

## 2 安装nginx(文件方式)
### 下载  
nginx-1.9.9.tar.gz，移动到/usr/local/下

### 解压
`tar -zxvf nginx-1.9.9.tar.gz`

### 进入nginx目录
`cd nginx-1.9.9`

### 配置
`./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module`

### 编译安装(默认安装在/usr/local/nginx)
```shell
make
make install
```
___

## 3 安装nginx(源方式)
### 安装EPEL yum存储库
`yum install epel-release -y`

### 安装
`yum install nginx`

### 启动nginx服务
`systemctl enable nginx`

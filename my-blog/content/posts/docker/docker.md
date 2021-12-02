---
title: "Docker"
date: 2021-11-07T17:30:00+08:00
lastmod: 2021-11-07T17:30:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "docker"
featuredImage: "/featuredImage/docker-featured-image.png"
featuredImagePreview: "/featuredImage/docker-featured-image.png"

tags: ["docker", "linux", "redis"]
categories: ["docker"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: false
---

## 1 Docker安装
### 安装存储库
`sudo yum install -y yum-utils device-mapper-persistent-data lvm2`  
**若sudo命令无法执行则运行** `yum -y install yum-utils`

### 配置Docker源
`yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`
<!-- `yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo` -->

### 查看仓库中Docker版本
`yum list docker-ce --showduplicates | sort -r`

### 安装最新版本或特定版本
`yum install -y docker-ce docker-ce-cli containerd.io`

{{<admonition>}}
Linux虚拟机若安装时报Error: moby-containerd conflicts with containerd.io-1.2.13-3.2.el7.x86_64
错误原因moby packages路径无法找到  
 1. `curl https://packages.microsoft.com/config/rhel/7/prod.repo >/etc/yum.repos.d/microsoft-prod.repo`  
 2. `rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm`  
 3. 修改/etc/yum.repos.d/microsoft-prod.repo中baseurl=https://packages.microsoft.com/centos/7/prod/
{{</admonition>}}

`yum install -y docker-ce docker-ce-cli containerd.io --exclude=moby-\* `

## 2 启动Docker
1. 启动容器  
`systemctl start docker`  
设置开机启动`systemctl enable docker`
2. 查看容器状态  
`systemctl status docker`

## 3 部署镜像
1. 新建文件夹docker,将Dockerfile和jar包复制到文件夹中
2. 在文件夹目录下运行`docker build -t <image id> .`
3. 运行docker images查看镜像
4. 启动镜像,运行 `docker run -d -p 8088:8088 --name <container name> <image id>`  
>示例：  
 `docker build -t registry.gitlab.ssc.stn.sh.cn .  `  
 `docker run -d -p 1088:8088 --name repair_api a36b6`

## 4 删除镜像
1. 查询容器 `docker ps -a`  
2. 删除容器 `docker rm <container id>`  
3. 删除镜像 `docker rmi <image id>`  

## 安装JDK
1. 运行 `yum -y install java-1.8.0-openjdk*`
2. 配置环境变量


## Docker Redis
1. 查看可用的 Redis 版本  
`docker search redis`
2. 取最新版的 Redis 镜像
`docker pull redis:latest`
3. 运行容器
`docker run -itd --name redis-test -p 6379:6379 redis --appendonly yes`  
将redis数据dump.rdb存放至/docker/目录下,并运行  
`docker run -itd --name redis -v /docker/dump.rdb:/data/dump.rdb -p 6379:6379 redis-bak --appendonly yes`
4. 测试
	```shell
	docker exec -it redis-test /bin/bash
	redis-cli
	set test 1
	get test
	exit
    ```
5. 容器迁移  
1). 导出镜像 `docker export <container_id>  >  <file_name>.tar`  
2). 停止容器 `docker stop <container_id>`  
3). 导入镜像 `docker import <file_name>.tar <container_id>`	

## 清理docker日志
1. 查找容器log日志文件  
`find /var/lib/docker/containers/ -name *-json.log |xargs du -sh`
2. 执行清理
`cat /dev/null > /var/lib/docker/containers/dfe25896671f0def031c5ffeb12dd335f7f54212c6ca5d3aca1c3f50b5e1eec4/dfe25896671f0def031c5ffeb12dd335f7f54212c6ca5d3aca1c3f50b5e1eec4-json.log`

    >设置容器日志大小，通过配置容器docker-compose的max-size选项来实现  
	```
	nginx:  
	image: nginx:1.12.1  
	restart: always  
	logging:  
	driver: “json-file”  
	options:  
	max-size: “5g” 
    ```

	>全局设置  
	新建/etc/docker/daemon.json，添加log-dirver和log-opts参数
	```json
	{
	"registry-mirrors": ["http://f613ce8f.m.daocloud.io"],
	"log-driver":"json-file",
	"log-opts": {"max-size":"500m", "max-file":"3"}
	}
    ```
	>>max-size=500m 意味着一个容器日志大小上限是500M  
	>>max-file=3 意味着一个容器有三个日志，分别是id+.json、id+1.json、id+2.json  

	重启docker  
	`systemctl daemon-reload`  
	`systemctl restart docker`

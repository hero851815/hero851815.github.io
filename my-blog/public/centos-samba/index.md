# Centos Samba配置


{{< admonition note "为什么使用samba">}}
使用linux的ftp协议传输或scp命令拷贝速度都不尽如人意
{{</ admonition >}}

## 步骤
### 安装smb服务
`yum install samba samba-client samba-common -y`

### 复制配置文件做备份
`cp -a  /etc/samba/smb.conf /etc/samba/smb.conf.bak`

### 关闭seliunx cat /etc/selinux/config
设置 SELINUX=disabled

### 添加系统用户（或使用root）
`useradd test passwd test`

### 添加samba用户（必须是系统用户）
`smbpasswd -a 123456`

### 创建共享目录，配置权限，所有者
```
mkdir -p /samba/wwwroot
chmod -R 0755 wwwroot/
chown -R test:test wwwroot/
```

### 配置/etc/samba/smb.cof，添加内容
>[wwwroot]  
    path = /samba/wwwroot  
	browseable = yes  
	writable = yes  
	valid users = root  

### 开启服务
`systemctl start sm`

### 访问共享目录
在windows中输入\\ip，其中wwwroot文件夹就是配置的共享目录

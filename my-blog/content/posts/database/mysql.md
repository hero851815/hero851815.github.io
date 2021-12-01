---
title: "MYSQL"
date: 2021-11-08T11:19:00+08:00
lastmod: 2021-11-08T11:19:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "mysql"

tags: ["mysql"]
categories: ["database"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: false
---

## 系统event
### 查看系统event事件开关
#### 方法一
`select @@event_scheduler;`

#### 方法二
`show variables like 'event_scheduler';`


### 开启系统event事件开关
#### 方法1
set GLOBAL event_scheduler=ON;

#### 方法2
set GLOBAL event_scheduler=1;


### 查看系统event事件
#### 方法一
`select * from mysql.event;`
 
#### 方法二
`SELECT * FROM information_schema.events;`


## 备份/还原数据库
设置数据库sql_mode，在**mysqld**中添加`sql_mode='STRICT_ALL_TABLES,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER'`  
创建用户并且授权  
`CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';`  
`CREATE USER 'pig'@'%' IDENTIFIED BY '123456';`  
`GRANT ALL ON databasetable.* TO 'pig'@'%';`

备份：储存SQL文件为结构和数据 或 cd至bin目录 `mysqldump -u root -p[password] [databasename] > c:/my.sql`  
还原： 
1. 服务器上使用root登录 	
2. `set names utf8;`
3. `source c:\db.sql`


## 创建语法
```sql
CREATE 
    [DEFINER = { user | CURRENT_USER }] --一般不写
    EVENT --event标识
    [IF NOT EXISTS] --如果没有就新建，建议不写这一行，确保每个event独立不冲突
    event_name -- 取个event名字
    ON SCHEDULE --计划任务标识
    schedule --计划任务时间，有两个参数（at 和 every），at代表当前一次，every表示每过多少时间执行一次
    [ON COMPLETION [NOT] PRESERVE] --可以不写，默认是ON COMPLETION NOT PRESERVE 即计划任务执行完毕后自动drop该事件；ON COMPLETION  PRESERVE则不会drop
    [ENABLE | DISABLE | DISABLE ON SLAVE] --可以不写，默认状态，默认是启用状态，可以指定为新建关闭状态 
    [COMMENT 'comment'] --注释event，可以不写
    DO --执行语句标识
    event_body; --执行的sql
 ```
 
 
>计划任务可以定义的时间方式  
>>schedule:  
    AT timestamp [+ INTERVAL interval] ... --at方式  
  | EVERY interval                         --every方式  
    [STARTS timestamp [+ INTERVAL interval] ...] --在at/every后可以指定什么时候开始  
    [ENDS timestamp [+ INTERVAL interval] ...]  --指定什么时候结束  
 
>MySQL官方提供的时间字符，可以参考   
>>interval:  
    quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE |   
              WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |   
              DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}   
			  
___

## 实例
### 实例1
#### 将repair库中的fa_repair_list表根据id转移至新表
创建存储过程backupRepairList
```
DROP PROCEDURE IF EXISTS backupRepairList;
DELIMITER //
CREATE PROCEDURE backupRepairList(out p_out varchar(100))
BEGIN
DECLARE count int DEFAULT 0;
DECLARE tableName varchar(50);
DECLARE thisMonth varchar(10);
DECLARE maxId varchar(20);
DECLARE maxIdnew varchar(20);
DECLARE sqlcreate varchar(1000);
DECLARE sqlselect varchar(1000);
DECLARE sqlinsert varchar(1000);
DECLARE sqldelete varchar(1000);
```

查询备份表是否存在,不存在则创建新表
```
SELECT date_format(now(), '%Y%m') INTO @thisMonth;
SET @tableName = CONCAT('fa_repair_list_',@thisMonth);
SELECT count(*) INTO @count FROM information_schema.tables where table_name=@tableName;
IF @count = 0 THEN
	BEGIN
		SET @sqlcreate = CONCAT('CREATE TABLE ',@tableName,' LIKE fa_repair_list;');
		PREPARE execsql FROM @sqlcreate;
		EXECUTE execsql;
		DEALLOCATE PREPARE execsql;
	END ;
END IF ;
```

#### 迁移数据
取原表及备份表最大id
```
BEGIN
	SELECT ifnull(max(id),'0') INTO @maxId FROM fa_repair_list;
	SET @sqlselect = CONCAT("SELECT ifnull(max(id),'0') INTO @maxIdnew FROM ", @tableName);
	PREPARE execsql FROM @sqlselect;
	EXECUTE execsql;
	DEALLOCATE PREPARE execsql;
END ;
```

根据最大id插入备份表
```
IF @maxIdnew < @maxId THEN
	BEGIN
		
	END ;
END IF ;
```

删除原表数据
```
IF @maxIdnew < @maxId THEN
	BEGIN


	END ;
END IF ;
```

调试
```
SELECT @maxIdnew INTO p_out;

END ;
//
DELIMITER;
```

创建事件
```
CREATE EVENT
ev_repair_repairlist_sync
ON SCHEDULE
EVERY 1 DAY
ON COMPLETION PRESERVE
DO
CALL backupRepairList(@p_out)
```

___

## 安装
### Docker安装
拉取镜像(国内)  
`docker pull hub.c.163.com/library/mysql:5.7`  
`docker images`

创建并启动容器  
`docker run --name mysql -e MYSQL_ROOT_PASSWORD=mysql -p 3308:3306 -d hub.c.163.com/library/mysql:5.7`

或建立目录映射（推荐）  
```
docker run -p 3308:3306 --name mysql \
  -v /usr/local/docker/mysql/conf:/etc/mysql \
  -v /usr/local/docker/mysql/logs:/var/log/mysql \
  -v /usr/local/docker/mysql/data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=mysql \
  -d hub.c.163.com/library/mysql:5.7
```

#### 修改mysql密码
进入容器  
`docker exec -it [容器ID] /bin/bash `  
`mysql -u root -p`

修改密码  
`use mysql`  
`SET PASSWORD FOR 'root' = PASSWORD('xxxxxxxxx');`  
`SET PASSWORD FOR 'root'@'localhost'=PASSWORD('xxxxxxxxx');`  
`flush privileges;`

查看数据文件存放路径  
`show variables like '%datadir%';`

重启容器
`docker stop containerid`
`docker start contatinerid`


### 压缩包安装
#### Linux
+ 1.将mysql-5.7.31-linux-glibc2.12-x86_64.tar文件上传至/opt目录
+ 2.解压 tar -zxvf mysql-5.7.31-linux-glibc2.12-x86_64.tar 并重命名为mysql
+ 3.创建mysql用户组和用户并修改权限  
`groupadd mysql`  
`useradd -r -g mysql mysql`
+ 4.创建数据目录并赋予权限  
`mkdir -p /data/mysql`  
`chown mysql:mysql -R /data/mysql`   
+ 5.进入/opt/mysql目录，初始化mysql  
`bin/mysqld --initialize --user=mysql --basedir=/opt/mysql --datadir=/opt/mysql/data`  
+ 6.编辑my.cnf `vim /etc/my.cnf`  
  ```yml
  [mysqld]
  datadir=/opt/mysql/data
  basedir=/opt/mysql
  socket=/tmp/mysql.sock
  user=mysql
  port=3306
  character-set-server=utf8

  symbolic-links=0
  # 取消密码验证
  # skip-grant-tables 
  # 表名不区分大小写
  lower_case_table_names=1 

  [mysqld_safe]
  log-error=/var/log/mysql.log
  pid-file=/var/run/mysqld/mysqld.pid
  ```
+ 7.将mysql.server放置到/etc/init.d/mysql中  
`cp /opt/mysql/support-files/mysql.server /etc/init.d/mysql`
+ 8.启动服务 `service mysql start`
+ 9.设置开机启动（可选） `chkconfig --add mysql`
+ 10.配置环境变量（可选）`vim /etc/profile`  
  在PATH中添加路径 `export PATH=$PATH:/opt/mysql/bin`  
  `source /etc/profile`使其立即生效


#### Windows
+ 1. `mysqld --initialize --console`  
+ 2. `mysqld install 服务名`
+ 3. `net start 服务名`
+ 4. 首次更换密码(使用初始化时显示的密码登录)：`alter user 'root'@'localhost' identified by '123456';`
+ 5. 修改密码：`update user set authentication_string=password("123456") where user="root";`
+ 6. `flush privileges;`
+ 7. 修改SQL_MODE：`set @@sql_mode='STRICT_ALL_TABLES,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER';`

#### 安装插件
##### 密码策略插件
拷贝validate_password.dll至lib/plugin目录  

在my.ini[mysqld]模块中中添加以下参数，重启服务  
```
early-plugin-load="validate_password.dll"
validate-password = FORCE_PLUS_PERMANENT
```

##### 登录失败插件
windows使用.dll,linux使用.so

`INSTALL PLUGIN CONNECTION_CONTROL SONAME 'connection_control.dll';`

在my.ini[mysqld]模块中添加以下参数，重启服务  
```
connection_control_min_connection_delay = 30000
connection_control_max_connection_delay = 108000
connection_control_failed_connections_threshold = 5
```
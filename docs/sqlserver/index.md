# SQL SERVER


## 数据库备份

### 完整备份作业
<!--完整备份,每周一次-->
```sql
USE Master  
GO  
declare @str varchar(100)  
set @str='D:\DataBase\FullBak-shi15-'+replace(replace(replace(convert(varchar,getdate(),20),'-',''),' ',''),':','')+'.bak'  
BACKUP DATABASE [shi15] TO DISK=@str  
WITH RETAINDAYS=15,NOFORMAT,NOINIT,  
NAME=N'Demo完整备份',SKIP,NOREWIND,  
NOUNLOAD,STATS=10  
GO
```


### 差异备份作业
<!--差异备份,每天一次-->
```sql
USE Master
GO
declare @str varchar(100)
set @str='D:\DataBase\DiffBak-shi15-'+replace(replace(replace(convert(varchar,getdate(),20),'-',''),' ',''),':','')+'.diff'
BACKUP DATABASE [shi15] TO DISK=@str
WITH DIFFERENTIAL,RETAINDAYS=8,NOFORMAT,NOINIT,
NAME=N'Demo差异备份',SKIP,NOREWIND,
NOUNLOAD,STATS=10
GO
```


### 日志备份作业
<!--日志备份,每小时一次-->
```sql
USE Demo
GO
declare @str varchar(100)
set @str='D:\DBtext\jgj\DBABak\logbak'+replace(replace(replace(convert(varchar,getdate(),20),'-',''),' ',''),':','')+'.trn'
BACKUP LOG [Demo] TO DISK=@str
WITH RETAINDAYS=3,NOFORMAT,NOINIT,
NAME=N'Demo日志备份',SKIP,NOREWIND,
NOUNLOAD,STATS=10
GO
```


## 限制IP登录
### 返回登录信息
```sql
SELECT 
a.[session_id],a.[login_time],a.[host_name],
a.[original_login_name],b.[client_net_address]
FROM MASTER.sys.dm_exec_sessions a 
INNER JOIN MASTER.sys.dm_exec_connections b 
ON a.session_id=b.session_id
```

### 限制登录用户和指定IP
```sql
-- =============================================
-- Author:        
-- Create date:    <2021.08.04>
-- Description:    <限制sa用户只能在本机和指定的IP中登陆>
-- =============================================

CREATE TRIGGER [tr_connection_limit]
ON ALL SERVER WITH EXECUTE AS 'sa'
FOR LOGON
AS
BEGIN

--限制sa这个帐号的连接
IF ORIGINAL_LOGIN()= 'sa'
--允许sa在本机和下面的IP登录
AND
(SELECT EVENTDATA().value('(/EVENT_INSTANCE/ClientHost)[1]', 'NVARCHAR(15)'))
NOT IN('<local machine>','192.168.20.93')
     ROLLBACK;
END;
GO

ENABLE TRIGGER [tr_connection_limit] ON ALL SERVER
GO

## 限制本机和指定的IP
--创建IP过滤表
USE MASTER
CREATE TABLE dbo.ValidIP ( 
    [IP] [nvarchar](15) NOT NULL,
	[memo] [nvarchar](100) NULL,
    CONSTRAINT PK_ValidIP PRIMARY KEY CLUSTERED(IP) 
); 
GO
--插入过滤IP
INSERT INTO dbo.ValidIP(IP) VALUES('<local machine>');
```

```sql
-- =============================================
-- Author:        
-- Create date:    <2021.08.04>
-- Description:    <限制本机和指定的IP登陆>
-- =============================================
CREATE TRIGGER [tr_logon_CheckIP] 
ON ALL SERVER 
FOR LOGON 
AS 
BEGIN 
    IF IS_SRVROLEMEMBER('sysadmin') = 1 
    BEGIN 
        DECLARE @IP NVARCHAR(15); 
        SET @IP = (SELECT EVENTDATA().value('(/EVENT_INSTANCE/ClientHost)[1]', 'NVARCHAR(15)')); 
        IF NOT EXISTS(SELECT IP FROM master.[dbo].[ValidIP] WHERE IP = @IP) 
        ROLLBACK;
    END;
END;
GO

ENABLE TRIGGER [tr_logon_CheckIP] ON ALL SERVER
GO
```

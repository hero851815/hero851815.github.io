---
title: "Oracle"
date: 2021-11-07T17:30:00+08:00
lastmod: 2021-11-07T17:30:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "oracle"

tags: ["oracle"]
categories: ["database"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: false
---

## 异常
### 查询死锁
```sql
select l.session_id sid, 
       s.serial#, 
       l.locked_mode, 
       l.oracle_username, 
       s.user#, 
       l.os_user_name, 
       s.machine, 
       s.terminal, 
       a.sql_text, 
       a.action 
  from v$sqlarea a, v$session s, v$locked_object l 
where l.session_id = s.sid 
   and s.prev_sql_addr = a.address 
order by sid, s.serial#;

select a.sid,spid,status,substr(a.program,1,40) prog,a.terminal,osuser,value/60/100 value
from v$session a,v$process b,v$sesstat c
where c.statistic#=12 and c.sid=a.sid and a.paddr=b.addr order by value desc;

SELECT SID, SERIAL#, INST_ID, MODULE,STATUS,S.LAST_CALL_ET
   FROM gv$session S
   WHERE S.USERNAME IS NOT NULL
   AND UPPER(S.PROGRAM) IN ('TOAD.EXE', 'W3WP.EXE')
   --AND S.LAST_CALL_ET >= 2 * 60*60
   AND S.STATUS = 'INACTIVE'
   ORDER BY S.LAST_CALL_ET DESC
```

### 查询连接数和session
```sql
select count(*) from v$process;
select count(*) from v$session;
select * from v$resource_limit;
```

### 查询表空间
```sql
SELECT segment_name, segment_type, blocks
FROM dba_segments
WHERE segment_name='TDB073';

SELECT blocks, empty_blocks, num_rows
FROM user_tables
WHERE table_name = 'TDB073';
```


### 分析表
```sql
begin
dbms_stats.gather_table_stats (
  ownname          => 'TEST',
  tabname          => 'STUDENT',
  estimate_percent => dbms_stats.AUTO_SAMPLE_SIZE,
  degree           => 4,
  cascade          => TRUE);
end;
```

### 分析索引
```sql
begin
dbms_stats.gather_index_stats(
  ownname          => 'TEST',
  indname          => 'IDX_STUDENT_BIRTH',
  estimate_percent => dbms_stats.AUTO_SAMPLE_SIZE,
  degree           => 4);
end;
```

### 定期清理空闲会话
```
CREATE OR REPLACE PROCEDURE DB_KILL_IDLE_CLIENTS AUTHID DEFINER AS
   job_no number;
   num_of_kills number := 0;

BEGIN

  FOR REC IN
  (
     SELECT SID, SERIAL#,MODULE, STATUS
    FROM V$SESSION S
    WHERE S.USERNAME IS NOT NULL
    AND UPPER(S.PROGRAM) IN ('TOAD.EXE', 'W3WP.EXE')
    AND S.LAST_CALL_ET >= 60*60
    AND S.STATUS = 'INACTIVE'
    ORDER BY SID DESC
   ) LOOP
   ---------------------------------------------------------------------------
   -- kill inactive sessions immediately
   ---------------------------------------------------------------------------
   DBMS_OUTPUT.PUT('LOCAL SID ' || rec.sid || '(' || rec.module || ')');
   execute immediate 'alter system disconnect session ''' || rec.sid || ', ' ||
      rec.serial# || '''immediate' ;

  DBMS_OUTPUT.PUT_LINE('. killed locally ' || job_no);
  num_of_kills := num_of_kills + 1;
  END LOOP;

   DBMS_OUTPUT.PUT_LINE ('Number of killed system sessions: ' || num_of_kills);
END DB_KILL_IDLE_CLIENTS;


------ alter system disconnect session '316,2379' immediate
```
___

## 表空间和用户
### 创建表空间和用户
```sql
create tablespace BLDS logging
datafile 'C:\app\Administrator\oradata\BLDS\BLDS.dbf'
size 1024m
autoextend on
next 1024m
maxsize 2048m
extent management local;

drop tablespace 表空间名称 including contents and datafiles cascade constraint;
```

### 用户创建,修改,删除,权限
`create user 用户名 identified by 口令[即密码] default tablespace 表空间;`
`alter user 用户名 identified by 口令[改变的口令];`
`drop user 用户名 cascade;	--加了cascade就可以把用户连带的数据全部删掉`
`GRANT CONNECT,RESOURCE TO 用户名; (GRANT DBA TO ..)`
`REVOKE CONNECT,RESOURCE FROM 用户名;`

### 导出数据库
`exp BLDS/BLDS@127.0.0.1:1521/orcl file='C:/Database/blds20190916.dmp'`

### 导入数据库
cmd中 `imp BLDS/BLDS@BLDS file='d:/back/BLDS2018-10-24.dmp' full=y ignore=y`

### 查看用户权限
`select * from DBA_SYS_PRIVS where grantee='BLDS'`

### SQLPLUS
`sqlplus / as sysdba`

___

## 表
### 创建表
**Create table**
```sql
create table TDB001_EX_TMP
(
  table_id        NUMBER(18) not null,
  customerid      VARCHAR2(30) not null,
  warehouseid     VARCHAR2(30) not null,
  happen_day      DATE not null,
  sku             VARCHAR2(20) not null,
  sku_type        VARCHAR2(30),
  qty             NUMBER(18,8),
  transfer_status CHAR(2),
  main_dist       CHAR(1),
  made_time       DATE,
  maker           VARCHAR2(10),
  update_time     DATE,
  updater         VARCHAR2(10),
  qty_undo        NUMBER(18,8),
  qty_available   NUMBER(18,8),
  qty_transmit    NUMBER(18,8),
  mq_status       CHAR(1),
  primary key (table_id)
)
```

**Create sequence**
```sql
 create sequence TDB001_EX_TMP_SEQUENCE
     minvalue 1
     maxvalue 9999999999999999999999999999
     /*NOMAXVALUE*/
     start with 1
     increment by 1
     nocache;

--表字段默认值 sku varchar2(20) default '123456' not null,
```

### 查询sequence
`select * from user_sequences`

### 创建索引
`create index I_TDB073_O on TDB073(trim(orderno)) online;`
`create index I_TDB076_ODS on TDB076 (trim(ORDERNO), trim(DELIVERYNO),  STATUS_CODE) online;`

## DBLINK
### 创建及删除dblink
`grant create  database link to user; --只有user用户能使用的dblink`
`grant create public  database link to user;--所有用户都可以使用的dblink`
`grant drop public  database link to user;	--删除dblink的权限`
`drop public  database link linkname;`
`create public database link orcl_remote connect to BLDS identified by BLDS USING 'orcl_101.132.35.49'; --创建dblink`

### 查询dblink
`select owner,object_name from dba_objects where object_type='DATABASE LINK';`

___

## JOB
### 创建job
```sql
declare   
job number;      
begin
sys.dbms_job.submit(job,'SP_S101_1003;',sysdate,'sysdate+5/(24*60)');
end;

declare   
job number;     
begin
sys.dbms_job.submit(job,
   'declare s1 varchar(1000); begin SP_HAR101(s1); end;',
    sysdate,
    'sysdate+3/(24*60)');
end;
```

### 查询job
SELECT * FROM DBA_JOBS; 
select * from all_jobs;
select job,
        log_user,
       to_char(last_date,'yyyy-mm-dd hh24:mi:ss') last_date,
         to_char(next_date,'yyyy-mm-dd hh24:mi:ss') next_date,
       interval,
         what
  from user_jobs

### 修改job间隔
```sql
begin
  dbms_job.interval(103,'sysdate+15/(24*60)');
  commit;
end;
```

### 运行,停止,删除job
```sql
begin
--dbms_job.run(44); --启动JOB
dbms_job.broken(44,true,sysdate); --停止JOB
end;

begin
 dbms_job.remove(23);
end;
```

___

## 执行
### 运行存储过程带参数
```sql
declare
       v_result varchar(100);
     begin
       SP_HAR101(v_result);
       end;
```

### 查,删重复数据
+ 查找表中多余的重复记录（多个字段）
`select * from 表 a where (a.Id,a.seq) in (select Id,seq from 表 group by Id,seq having count(*) > 1)`
 
+ 删除表中多余的重复记录（多个字段），只留有rowid最小的记录
`delete from 表 a where (a.Id,a.seq) in (select Id,seq from 表 group by Id,seq having count(*) > 1) and rowid not in (select min(rowid) from 表 group by Id,seq having count(*)>1)`

___

## Docker
### docker中数据备份
1. `docker exec -it <container_id> /bin/bash`
2. `exp account/password@tnsName file=/u01/app/oracle/file.dmp log=/u01/app/oracle/file.log`
>exp 服务器A上数据库账号/密码@本地服务器B配置的tnsnames.ora内的name file=导出路径（此处为Oracle容器内相对路径） log=日志路径（相对路径+1） full=数据库完全导出
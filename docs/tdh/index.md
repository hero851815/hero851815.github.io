# TDH笔记


{{< admonition info "">}}
Transwarp Data Hub是基于Hadoop技术架构产生的大数据平台
{{</ admonition >}}

**TDH产品架构图**
![产品架构图](/images/tdh_architecture_diagram.png "产品架构图")

## 基础概念
### TDH服务
>基于TDH6.0文档整理

| 服务 | 描述 |
| ------ | ----------- |
| Zookeeper | 分布式应用 |
| HDFS | 分布式文件系统 |
| YARN | 分布式资源调度 |
| Inceptor | 分布式SQL引擎 |
| Inceptor-Gateway | 连接客户端和Inceptor服务的中间件 |
| Hyperbase | NoSQL数据库 |
| Discover | 数据科学分析探索工具 |
| Slipstream | 支持事件驱动和微批处理的流处理引擎 |
| Slipstream Studio | 流处理引擎的监控管理平台 |
| Search | 可扩展的分布式全文搜索和分析引擎 |
| Workflow | 工作流调度平台 |
| Transporter | 数据ETL工具 |
| Pilot | 轻量级自助式分析BI工具 |
| Governor | 元数据管理和数据治理工具 |
| Rubik | Cube设计工具 |
| TxSQL | 强一致、高可用的分布式数据库系统 |
| Kafka | 分布式消息队列 |
| Sqoop1 | 分布式ETL工具 |
| Codis | 分布式Redis解决方案 |
| 日志分析组件 | 分布式日志收集和分析系统 |
| Shiva | 内置存储框架 |
| Transwarp Manager | 部署、管理和运维TDH集群的管理组件 |
| Kerberos | 数据认证体系 |
| Guardian | 平台安全管控组件 |
| TCOS Market | 云操作系统市场 |

### 数仓
​数据仓库在企业中核心的定位就是实现统一的数据采集、加工、安全管理以及提供数据服务的中心平台，为各类基于数据的应用体系建设的基础设施工程

___

## 安装
由星环工程师负责安装
### 环境描述


___

## 配置
### Sqoop1
#### 简介
将Hadoop和关系型数据库中的数据相互转移的工具；  
可以将关系型数据库中的数据导到Hadoop的HDFS/HIVE/HBase中，也可以将HDFS的数据导进到关系型数据库中；  
通过Hadoop的mapreduce把数据从关系类型数据导入到HDFS   

#### Sqoop原理
+ 用户的Sqoop脚本最终会变成提交到YARN上的一个M/R任务。
+ 通过Sqoop用户可以将数据从RDB抽取至HDFS。输入端是关系型数据库中的某张表，
+ Sqoop会去一行一行的把数据从这张表里读出来写入到HDFS;
+ 输出端是HDFS上关于这个表的文件的集合。由于整个抽取ETL过程是并行化的，因此输出端会有多个文件。
+ 输出的文件可以指定列分隔符、换行符等分界符
+ 通过Sqoop把HDFS上的文件回传到关系型数据库的过程和第二条类似

#### 安装
To be continued

#### 测试
##### 列出RDB中数据库
>语法范式解析：
>>sqoop list-databases: SQOOP 命令  
--username: RDB用户名  
--password: RDB明文密码  
-P: 需要用户输入密码  
--password-file: 用户密码文件(400权限)  
--connect: 数据库JDBC连接串  

```shell
sqoop list-databases \
--connect jdbc:mysql://localhost:3306 \
--username root \
--password 123456
```

##### 列出RDB中某数据库中的表
>语法范式解析：
>>sqoop list-databases: SQOOP 命令  
--username: RDB用户名  
--password: RDB明文密码  
-P: 需要用户输入密码  
--password-file: 用户密码文件(400权限)  
--connect: 数据库JDBC连接串

```shell
sqoop list-tables \
--connect jdbc:mysql://localhost:3306/db_test \
--username root \
--password 123456
```

#### 数据抽取
##### 全量数据抽取至HDFS
>语法范式解析：
>>sqoop import: SQOOP 命令，从关系型数据库导数到Hadoop  
--username: RDB用户名  
--password: RDB明文密码  
--connect: 数据库JDBC连接串  
--target-dir: HDFS目标文件夹位置  
-m: mapper个数，抽数线程数，默认4  
--null-string: string类型的空值处理  
--null-node-string:非string类型的空值  
--fields-terminated-by:列分隔符  
--hive-drop-import-delims:去除^A,\n,\r等特殊字符

{{< admonition warning "">}}
1.当-m大于1时，需要指定--split-by 字段  
2.--target-dir 后面跟的HDFS目录需要确认用户有写权限
{{</ admonition >}}

```shell

```

##### 增量数据抽取


___


## TDH 数据流转场景
{{< admonition note "">}}
文章内容来自网络
{{</ admonition >}}
### T+1方式数据流转
![T+1方式数据流转](/images/tdh_dataflow_t+1.png "T+1")
T+1模式下数据仓库会在每天固定时间点采集当天或者前一天交易系统数据
>例一：
>>oracle中的表 tableA，需要一次性的全量抽取全部数据，之后的业务需要对该表做精确查询。这个时候选择Transporter直接将数据录入orc表中，之后根据业务的需求，将数据录入hyperbase中。

>例二：
>>oracle中的表tableA，该表每日有数据录入，需要对该表的数据做统计分析。这个时候可以选择sqoop对该表进行增量数据的抽取，放在HDFS上，之后通过inceptor将数据存入Argodb中，方便之后的统计分析。

>例三：
>>每日的系统日志文件，需要根据日志进行ERROR的分析。选用flume对日志的目录进行数据采集，之后通过将数据存入Search中对日志信息进行分词，方便针对报错信息的排查。

>例四：
>>超大量的离线历史数据文件，需要放入大数据平台存储。选用Superput将数据上传进hdfs进行存储。

### 准实时数据流转
![准实时数据流转](/images/tdh_dataflow_semirealtime.png "准实时")
>例一：
>>Oracle中的数据需要准实时的将数据同步写入大数据平台中。搭建一套ogg+transporter的方式，可以将数据准实时的录入到orc事务表中。

>例二：
>>Mysql中的数据需要准实时的将数据同步写入到大数据平台中。这边有很多的工具都可以直接将mysql数据同步到hdfs中，这边推荐使用CDC或者MySQL Applier for Hadoop。

### 实时流处理
![实时流处理](/images/tdh_dataflow_realtime_streaming.png "实时流")
比较多的情况下实时数据通过kakfa接入，数据来源可以是kafka producer/flume等，经过slipstream的处理，之后根据实际业务需求，将数据入库到TDH中
>例一：
>>实时数据通过kafka进来，需要在经过处理后把结果实时显示在大屏上。这个时候的kafka担任着数据总线的功能，数据从kafka接入，通过slipstream处理后再输出给kafka。下游大屏显示工具实时消费kafka数据。

>例二：
>>实施数据通过kafka进来，对结果需要进行统计分析和批处理。可以通过Slipstream接入数据到hyperbase表，之后再通过inceptor或者Hyperbase API，将数据流转到Inceptor ORC表中，从而满足业务需求。

上面抽象的三种典型数据流转综合场景，其原理是非常一致的：数据通过工具接入，根据业务需求，选择写入合适的表。


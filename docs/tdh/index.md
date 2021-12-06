# TDH笔记


{{< admonition info "">}}
Transwarp Data Hub是基于Hadoop技术架构产生的大数据平台
{{</ admonition >}}

**TDH产品架构图**
![产品架构图](/images/tdh_architecture_diagram.png "产品架构图")

## 基础概念

### TDH服务

> 基于TDH6.0文档整理

| 服务                | 描述                   |
| ----------------- | -------------------- |
| Zookeeper         | 分布式应用                |
| HDFS              | 分布式文件系统              |
| YARN              | 分布式资源调度              |
| Inceptor          | 分布式SQL引擎             |
| Inceptor-Gateway  | 连接客户端和Inceptor服务的中间件 |
| Hyperbase         | NoSQL数据库             |
| Discover          | 数据科学分析探索工具           |
| Slipstream        | 支持事件驱动和微批处理的流处理引擎    |
| Slipstream Studio | 流处理引擎的监控管理平台         |
| Search            | 可扩展的分布式全文搜索和分析引擎     |
| Workflow          | 工作流调度平台              |
| Transporter       | 数据ETL工具              |
| Pilot             | 轻量级自助式分析BI工具         |
| Governor          | 元数据管理和数据治理工具         |
| Rubik             | Cube设计工具             |
| TxSQL             | 强一致、高可用的分布式数据库系统     |
| Kafka             | 分布式消息队列              |
| Sqoop1            | 分布式ETL工具             |
| Codis             | 分布式Redis解决方案         |
| 日志分析组件            | 分布式日志收集和分析系统         |
| Shiva             | 内置存储框架               |
| Transwarp Manager | 部署、管理和运维TDH集群的管理组件   |
| Kerberos          | 数据认证体系               |
| Guardian          | 平台安全管控组件             |
| TCOS Market       | 云操作系统市场              |

### 数仓

​数据仓库在企业中核心的定位就是实现统一的数据采集、加工、安全管理以及提供数据服务的中心平台，为各类基于数据的应用体系建设的基础设施工程

### Hadoop、HDFS、Hive、HBase之间的关系
+ **Hadoop**：是一个分布式计算的开源框架
+ **HDFS**：是Hadoop的三大核心组件之一
+ **Hive**：用户处理存储在HDFS中的数据，hive的意义就是把好写的hive的sql转换为复杂难写的map-reduce程序。
+ **Hbase**：是一款基于HDFS的数据库，是一种NoSQL数据库，主要适用于海量明细数据（十亿、百亿）的随机实时查询，如日志明细、交易清单、轨迹行为等。  
  > Hive与HBase的区别和联系
  > 
  > > 区别：
  > > Hive：Hive是基于Hadoop的一个**数据仓库工具**，可以将结构化的数据文件映射为一张数据库表，并提供简单的sql查询功能。
  > > - Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑。hive需要用到hdfs存储文件，需要用到MapReduce计算框架。
  > > - hive可以认为是map-reduce的一个包装。hive的意义就是把好写的hive的sql转换为复杂难写的map-reduce程序。
  > > HBase：HBase是Hadoop的**数据库**，一个分布式、可扩展、大数据的存储。
  > > + hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作
  > > + hbase可以认为是hdfs的一个包装。他的本质是数据存储，是个NoSql数据库；hbase部署于hdfs之上，并且克服了hdfs在随机读写方面的缺点。  
  > 
  > > 联系：
  > > Hbase和Hive在大数据架构中处在不同位置，Hbase主要解决实时数据查询问题，Hive主要解决数据处理和计算问题，一般是配合使用。
  
  在大数据架构中，Hive和HBase是协作关系，数据流一般如下图：
  1. 通过ETL工具将数据源抽取到HDFS存储；
  2. 通过Hive清洗、处理和计算原始数据；
  3. HIve清洗处理后的结果，如果是面向海量数据随机查询场景的可存入Hbase
  4. 数据应用从HBase查询数据；
![数据流](/images/Hive和HBase一般数据流.png "Hive和HBase一般数据流")   

## 安装

### 环境描述

___

## 配置

### Hadoop/HDFS

#### 简介

#### 伪分布式安装

+ 1.修改hadoop-env.sh，配置JAVA变量  
  `export JAVA_HOME=/usr/local/java/jdk1.8.0_311`

+ 2.修改core-site.xml，配置文件系统和访问位置  
  
  ```xml
  <configuration>
        <property>
              <name>fs.defaultFS</name>
              <value>hdfs://127.0.0.1:9000</value>
        </property>
        <property>
              <name>hadoop.tmp.dir</name>
              <value>file:/usr/local/hadoop/hadoop-2.10.1/tmp</value>
        </property>
  </configuration>
  ```

+ 3.修改hdfs-site.xml，设置文件存储的位置(datanode和namenode不在同一个文件夹,所以可以共存在一个机器,通过端口访问)  
  
  ```xml
  <configuration>
        <property>
              <name>dfs.replication</name>
              <value>1</value>
        </property>
        <property>
              <name>dfs.namenode.name.dir</name>
              <value>file:/usr/local/hadoop/hadoop-2.10.1/tmp/dfs/name</value>
        </property>
        <property>
              <name>dfs.datanode.data.dir</name>
              <value>file:/usr/local/hadoop/hadoop-2.10.1/tmp/dfs/data</value>
        </property>
  </configuration>
  ```

+ 4.配置环境变量`vim ~/.bashrc`
  
  ```shell
  export JAVA_HOME=/usr/local/java/jdk1.8.0_311
  export FLUME_HOME=/usr/local/apache-flume-1.8.0
  export HADOOP_HOME=/usr/local/hadoop/hadoop-2.10.1
  export HADOOP_INSTALL=$HADOOP_HOME
  export HADOOP_MAPRED_HOME=$HADOOP_HOME
  export HADOOP_COMMON_HOME=$HADOOP_HOME
  export HADOOP_HDFS_HOME=$HADOOP_HOME
  export YARN_HOME=$HADOOP_HOME
  export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
  export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native
  export SQOOP_HOME=/usr/local/hadoop/sqoop-1.4.7
  export HIVE_HOME=/usr/local/hadoop/apache-hive-3.1.2
  export HIVE_CONF_DIR=$HIVE_HOME/conf
  export PATH=$PATH:$FLUME_HOME/bin:$JAVA_HOME/bin:$SQOOP_HOME/bin:$HIVE_HOME/bin:$HIVE_CONF_DIR:$HAOOP_HOME/bin:$HADOOP_HOME/sbin
  ```
  
  立即生效`source ~/.bashrc`

+ 5.初始化hdfs `$HADOOP_HOME/bin/hdfs namenode -format`  

+ 6.启动进程`$HADOOP_HOME/sbin/start-dfs.sh`  
  hadoop启动后通过命令`jps`查看，有三个进程NameNode、DataNode、SecondaryNode；访问Web界面<http://localhost:50070>  

#### HDFS命令

##### 查看

查看指定目录下的文件和文件夹  
`$HADOOP_HOME/bin/hadoop fs -ls /user/root/tb_dept`  
查看文件内容  
`$HADOOP_HOME/bin/hadoop fs -cat /user/root/tb_dept/part-m-00000`  

##### 删除

删除文件  
`$HADOOP_HOME/bin/hadoop fs -rm /user/root/tb_dept/*`  
删除文件夹  
`$HADOOP_HOME/bin/hadoop fs -rm -r /user/root/tb_dept`

### Hive数据仓库工具

#### 配置

1.将/conf/hive-default.xml.template复制重命名为hive-site.xml  
2.根据hive-site.xml中属性<hive.metastore.warehouse.dir>的值</user/hive/warehouse>，让Hadoop新建hdfs目录  
`$HADOOP_HOME/bin/hadoop fs -mkdir -p /user/hive/warehouse`  
3.赋予读写权限  
`$HADOOP_HOME/bin/hadoop fs -chmod 777 /user/hive/warehouse`  
4.新建`/tmp/hive`hdfs目录并赋予权限    
`$HADOOP_HOME/bin/hadoop fs -mkdir -p /tmp/hive`  
`$HADOOP_HOME/bin/hadoop fs -chmod 777 /tmp/hive`  
5.新建本地目录可自定义 `/opt/hive/tmp`并赋予权限
修改hive-site.xml中的临时目录，搜索目录将`${system:java.io.tmpdir}`替换为本地临时目录；将`${system:user.name}`替换为root  
6.修改hive-site.xml中数据库相关配置

```xml
<!-- 修改数据库连接 -->
<name>javax.jdo.option.ConnectionURL</name>
<value>jdbc:mysql://10.211.55.5:3306/hive_test?createDatabaseIfNotExist=true</value>

<!-- 修改数据库驱动 -->
<name>javax.jdo.option.ConnectionDriverName</name>
<value>com.mysql.jdbc.Driver</value>

<!-- 修改数据库用户名 -->
<name>javax.jdo.option.ConnectionUserName</name>
<value>root</value>

<!-- 修改数据库密码 -->
<name>javax.jdo.option.ConnectionPassword</name>
<value>123456</value>

<!-- 修改验证为false -->
<name>hive.metastore.schema.verification</name>
<value>false</value>
```

6.将数据库连接驱动mysql-connector-java-5.1.49.jar放入/usr/local/hadoop/apache-hive-3.1.2/lib/下  
7.进入/conf目录下，复制`hive-env.sh.template`重命名为`hive-env.sh`  
8.打开`hive-env.sh`添加变量

```
export HADOOP_HOME=/usr/local/hadoop/hadoop-2.10.1
export HIVE_CONF_DIR=/usr/local/hadoop/apache-hive-3.1.2/conf
export HIVE_AUX_JARS_PATH=/usr/local/hadoop/apache-hive-3.1.2/lib
```

##### 启动

初始化数据库  
`$HIVE_HOME/bin/schematool -initSchema -dbType mysql`  
启动  
`$HIVE_HOME/bin/hive`

##### 测试

从文本中导入

##### 常见问题

+ 错误信息：`Class path contains multiple SLF4J bindings`
  ![errorlog1](/images/hive_run_errorlog_1.png "错误日志1")
  原因及处理：hadoop和hive的log4j包版本不一致；删除hive中log4j-slf4j-impl-2.10.0.jar包  
+ 错误信息：`com.ctc.wstx.exc.WstxParsingException: Illegal character entity: expansion character`
  ![errorlog2](/images/hive_run_errorlog_2.png "错误日志2")
  原因及处理：找到报错文件所在行，注释特殊符号

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

#### 配置

1.将/conf/sqoop-env.sh.template复制重命名为sqoop-env.sh  
2.打开`sqoop-env.sh`添加变量  

```
export HADOOP_COMMON_HOME=/usr/local/hadoop/hadoop-2.10.1
export HADOOP_MAPRED_HOME=/usr/local/hadoop/hadoop-2.10.1/share/hadoop/mapreduce
export HIVE_HOME=/usr/local/hadoop/apache-hive-3.1.2
```

3.若要连接mysql数据库，将数据库连接驱动mysql-connector-java包导入$SQOOP_HOME/lib目录下

#### 测试

##### 列出RDB中数据库

> 语法范式解析：
> 
> > sqoop list-databases: SQOOP 命令  
> > --username: RDB用户名  
> > --password: RDB明文密码  
> > -P: 需要用户输入密码  
> > --password-file: 用户密码文件(400权限)  
> > --connect: 数据库JDBC连接串  

```shell
sqoop list-databases \
--connect jdbc:mysql://localhost:3306 \
--username root \
--password 123456
```

##### 列出RDB中某数据库中的表

> 语法范式解析：
> 
> > sqoop list-databases: SQOOP 命令  
> > --username: RDB用户名  
> > --password: RDB明文密码  
> > -P: 需要用户输入密码  
> > --password-file: 用户密码文件(400权限)  
> > --connect: 数据库JDBC连接串

```shell
sqoop list-tables \
--connect jdbc:mysql://localhost:3306/db_test \
--username root \
--password 123456
```

#### 数据抽取

##### 全量数据抽取至HDFS

```shell
sqoop import \
--connect jdbc:mysql://localhost:3306/db_test \
--username root \
--password 123456 \
--table tb_dept \
-m 1
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

> 例一：
> 
> > oracle中的表 tableA，需要一次性的全量抽取全部数据，之后的业务需要对该表做精确查询。这个时候选择Transporter直接将数据录入orc表中，之后根据业务的需求，将数据录入hyperbase中。

> 例二：
> 
> > oracle中的表tableA，该表每日有数据录入，需要对该表的数据做统计分析。这个时候可以选择sqoop对该表进行增量数据的抽取，放在HDFS上，之后通过inceptor将数据存入Argodb中，方便之后的统计分析。

> 例三：
> 
> > 每日的系统日志文件，需要根据日志进行ERROR的分析。选用flume对日志的目录进行数据采集，之后通过将数据存入Search中对日志信息进行分词，方便针对报错信息的排查。

> 例四：
> 
> > 超大量的离线历史数据文件，需要放入大数据平台存储。选用Superput将数据上传进hdfs进行存储。

### 准实时数据流转

![准实时数据流转](/images/tdh_dataflow_semirealtime.png "准实时")

> 例一：
> 
> > Oracle中的数据需要准实时的将数据同步写入大数据平台中。搭建一套ogg+transporter的方式，可以将数据准实时的录入到orc事务表中。

> 例二：
> 
> > Mysql中的数据需要准实时的将数据同步写入到大数据平台中。这边有很多的工具都可以直接将mysql数据同步到hdfs中，这边推荐使用CDC或者MySQL Applier for Hadoop。

### 实时流处理

![实时流处理](/images/tdh_dataflow_realtime_streaming.png "实时流")
比较多的情况下实时数据通过kakfa接入，数据来源可以是kafka producer/flume等，经过slipstream的处理，之后根据实际业务需求，将数据入库到TDH中

> 例一：
> 
> > 实时数据通过kafka进来，需要在经过处理后把结果实时显示在大屏上。这个时候的kafka担任着数据总线的功能，数据从kafka接入，通过slipstream处理后再输出给kafka。下游大屏显示工具实时消费kafka数据。

> 例二：
> 
> > 实施数据通过kafka进来，对结果需要进行统计分析和批处理。可以通过Slipstream接入数据到hyperbase表，之后再通过inceptor或者Hyperbase API，将数据流转到Inceptor ORC表中，从而满足业务需求。

上面抽象的三种典型数据流转综合场景，其原理是非常一致的：数据通过工具接入，根据业务需求，选择写入合适的表。


---
title: "Hive"
date: 2022-02-23T15:24:22+08:00
image: "b2.webp"
---

[TOC]



## 1.Hive Introduction

- HIve是一个开源的 **数据仓库**软件，用于read，write，manage直接存储在HDFS中的大型数据集文件

- HQL用于数据查询和分析

- HIve是基于读取的因此不适用于高频率写入操作的事务处理

- HIve功能

  - 提取/转载/加载 （ETL）数据
  - 存储 查询和分析HDFS或Hbase中存储的大规模数据
  - **查询是通过MapReduce完成的，Notice🎉：并非所有查询都需要Mapreduce**
    - Extension：hive为了执行效率考虑，简单的查询如select，不带count，sum,group by😀这样的都不走mapreduce，直接读取hdfs文件进行filter过滤
    - 当查询语句中的过滤条件只是分区字段的情况下不会进行mapreduce
  - hive-site-xml 有个配置参数`hive.fetch.task.conversion = more`将该参数设置为more时，简单查询就不会走mapreduce了，色湖之为minimal，那么任何查询都会走mapreduce🚩

- Hive允许用户编写自己的函数,有如下三种类型：

  - 用户定义函数 UDF
  - 用户定义聚合函数 UDAF
  - 用户定义的表生成函数 UDTF

- Hive缺点：

  - 不支持处理session
  - 无法修改表数据（无法更新、删除、插入）只能通过文件追加和重新导入数据
  - 无法为列编制索引（支持索引，但无法提高查询速度）

- Hive数据存储

  - Hive可以解析数据，所以将数据导入Hive表只需将数据移动到表所在的目录（数据在HDFS）,如果数据在本地文件系统，则需要将数据复制到表所在的目录中。🀄

  - 从本地：Example：在本地文件系统船舰一个名为local.txt的文件（数据之间空格间隔），然后执行了`load data local inpath'绝对路径/local.txt into table local;就可以 使用`hadoop fs -ls

    路径/local 查看是否导入成功

  - 从HDFS:在HIve中创建data_hdfs表，表中假设有两个字段：name,age;

    将文件从本地上传到hdfs：`hadoop fs -put data_hdfs.txt /data`

    hive导入`load data inpath 'data/data_hdfs.txt' into table data_hdfs;

- HIve客户端

  - Thrift Server：为所有支持Thrift的编程语言的请i去提供服务（几乎所有主流语言）
  - Thrift实际上是实现了C/S模式，通过代码生成工具将thrift文生成服务器端和客户端代码（可以为不同语言），从而实现服务端和客户端跨语言的支持。用户在Thirft文件中声明自己的服务，这些服务经过编译后会生成相应语言的代码文件，然后客户端调用服务，服务器端提服务便可以了。
  - Thrift是Facebook于2007年开发的跨语言的rpc服框架，提供多语言的编译功能，并提供多种服务器工做模式；用户经过Thrift的IDL（接口定义语言）来描述接口函数及数据类型，而后经过Thrift的编译环境生成各类语言类型的接口文件，用户能够根据本身的须要采用不一样的语言开发客户端代码和服务器端代码。git

    例如，我想开发一个快速计算的RPC服务，它主要经过接口函数getInt对外提供服务，这个RPC服务的getInt函数使用用户传入的参数，通过复杂的计算，计算出一个整形值返回给用户；服务器端使用java语言开发，而调用客户端能够是java、c、python等语言开发的程序，在这种应用场景下，咱们只须要使用Thrift的IDL描述一下getInt函数（以.thrift为后缀的文件），而后使用Thrift的多语言编译功能，将这个IDL文件编译成C、java、python几种语言对应的“特定语言接口文件”（每种语言只须要一条简单的命令便可编译完成），这样拿到对应语言的“特定语言接口文件”以后，就能够开发客户端和服务器端的代码了，开发过程当中只要接口不变，客户端和服务器端的开发能够独立的进行。apa

  - JDBC Driver：用于hive和java之间建立连接
  - **ODBC Driver**：允许自持ODBC协议的应用程序连接到hive 🏷

- HIve Service

  - HIve CLI ：是一个shell，用于**执行Hive查询和命令**
  - HIve Web User Interface:hive cli的替代，提供了相应功能的基于web的GUI
  - HIve Metastore：一个 **中央存储库**，存储仓库中的**各种表和分区的所有结构信息**。还包括列的元数据及其类型信息；Used for 读取和写入数据以及存储数据的相应HDFS文件的序列化程序和反序列化数据。📝
  - HIve Server: 接受来自不同客户端的请求，并将其提供给Hive驱动程序
  - HIve Driver: 接收来自不同来源的查询，它 **将查询传输到编译器**
  - HIve Compiler：目的是 **解析查询🔍并对不同的查询快和表达式执行语义分析**，-->将HQL转化为MapReduce Job

## 2.HIve数据模型

- ### Tables

  - **托管表或内部表**：HIve在创建表时的默认表。	
    - 特点：在内部表被删除后，表的元素据和表数据都从HDFS中完全删除
    - 适用场景：👤ETL数据清理死用内部表做中间表，清理时HDFS上的文件同步清除🆑在误删的情况下，易于回复的数据，用内部表⏲在统计分析时，不涉及数据共享的情况
  - 外部表：表中的数据删除后任然在HDFS中，也就是说只会删除相关的元数据，而表的内容不会删除
    - 声明外部表的语法`create external table if not exists `

- Partiotion

  -  **通过基于任何列或分区键将相同类型的数据分组在一起，HIve表被组织在分区中，每个表都有一个分区键用于表示**，分区可以加快查询和切片过程。
  -  `create table........... Patitioned BY(partition1 string,partition2 string)`
  -  分区通过减少延迟来提高查询速度，因为它只扫描相关数据，而不扫描完整数据

- 分桶Buckets

  - 表或者分区可以再次细分为Bucket，因此必须使用hash函数：
  - `create table.....patitioned B....Clustered BY(clolumn 1 ,column 2) Sorted BY(column_name Asc:Desc,_) Into num_bucket Buckets;
  - 分桶只是表目录中的文件，可以是分区也可不分区，甚至可以选择n个bucket对数据进行分区。、

## 3.Hive数据类型

- HIve是用java开发的，HIve的基本数据类型对应于java的基本数据类型：TINYINT-1,SMALLINT-2,INT-4,BIGINT-8;

- 复杂数据类型` create table tt(c1 ARRAY<INT>,c2 MAP<STRING,INT>,c3 STRUCT<a:STRING,b:INT,c:DOUBLE>);`

## 4.HIveServer2

- HiveServer2是一种使客户端能够对HIve执行查询的服务（hs1称作节约服务器，基于Thrift协议构建，用于处理HIve的跨平台通讯，它允许不同的客户端向hive提交请求并检索最后结果）。**但是它不处理来自多个客户端的并发请求，因此被Hs2替换**🏷🏷🏷

### 4.1 HS2体系结构

- **MetaStore👋**：原存储可以配置为 **嵌入式**或 **远程服务器**。HS2与metastore通信，以获取查询编译所需要的元数据
  - 嵌入式：hive服务和metastore服务运行在同一个进程中，derby服务也运行在该进程中。该模式无需特殊配置。
  - 本地模式：hive服务和metastore服务运行在同一个进程中，mysql是单独的进程，可以在同一台机器上，也可以在远程机器上。该模式只需将hive-site.xml中的ConnectionURL指向mysql，并配置好驱动名、数据库连接账号即可
  - 远程模式：hive服务和metastore在不同的进程内，可能是不同的机器。  该模式需要将hive.metastore.local设置为false，并将hive.metastore.uris设置为metastore服务器URI，如有多个metastore服务器，URI之间用逗号分隔。metastore服务器URI的格式为thrift://host:port。
- **JDBC Client🥐**：JDBC客户端使用JDBC驱动与HS2交互
  - jdbc启动传输连接，通过 **OpenSession API**调用获取会话句柄创建 **HiveConnection**， **绘画使从服务器端创建**
  - 执行HiveStatement，并从Thrift客户机发出ExecuteStatement API调用。在API调用中，SessionHandle信息与查询信息一起传递给服务器
  - HS2请求驱动程序（CommandProcessor）进行查询解析和编译。驱动程序启动一个后台job，该job与MApreduce对话，然后向客户端返回响应（包含从服务器端创建的OperationHandle）
  - 客户端使用oh与hs2对话，以查询执行状态

## 5.Hive管理

- Hive中的数据库描述了为了相似目的或属于相同组的表的集合。 **每当创建数据库时，HIve都会在/user/Hive/warehouse**处为每个数据库创建一个目录，该目录在HIve.metastore.warehouse.dir中定义。

- ```sq
  #描述数据库
  describe database ....
  #创建带有注释的数据库
  create database if not exists t1 comment "Its Niit Project"
  #使用dbproperties创建数据库
  create database if not exists db2 with dbproperties('creator'='Peter','date'='2021-08-02');
  #用dbproperties描述数据库
  describe database extended t1;
  #删除数据库及其所有表
  deop database if exists 数据库名 CASCADE;
  #使用通配符
  show databases like 'my.*';
  ```

- ```sql
  Create external table ......
  当用户创建内部表时，它会将数据移动到仓库指向的路径。如果创建了外部表的，它只会记录数据所在的路径，而不更改数据的位置。
  Create table wdnmd() row format delimited fields terminated by '分隔符' lines terminated by '\n' stored as 文件类型（默认是时TEXTFILE）
  #创建分区表
  create table if not exists part_table() partiotioned by(ss string) row format delimited fields terminated by ',' lines terminated by '\n';
  #创建分桶表
  create table if not exists test_bucket(id int) partiotioned by(ss string) clustered by (id) into 3 buckets row format delimited fields terminated by ',' lines terminated by '\n';
  #复制表结构
  create table test_liek_table like test_bucket;
  ```

- ```sql
  表重命名：alter table table_name rename to new_name
  修改表的列类型：alter table table_name  old_column_name new_column_name column_type;
  添加列：alter table table_nameadd columns (d int);
  更改列名： alter table table_name change column_name new_name string;
  删除列并创建新列名：alter table table_name replace columns(id int,name string);
  ```

### 5.1托管表和内部表

- 内部表的特点

  - 内部表由hive控制
  - **访问内部表的数据唯一的方法就是使用hive**。无论数据的位置是HDFS还是hive自己的仓库目录，**HIve不允许其他的任何应用程序访问该数据**®®®®

  - 如果必须访问内部表的数据，只能对该表使用HIve查询

- 外部表的特点：
  - 只有存储在metastore的元数据由hive控制

### 5.2将数据加载到Hive

- 加载的过程中，LOCAL关键词指定文件在主机中的位置。 **如果未指定LOCAL关键字，将从inpath之后指定的URI或fs.default.name Hive属性中的值加载文件，而Overrite决定是追加还是替换现有数据**
- 本地：`load data local inpath '/////' into table table_name`
- HDFS:`load data inpath '/user/.../... into table table_name'`

### 🍥🍥Hive分区🍥🍥

- 分区的重要性
  - 分区是一种根据特定列的值将表划分为相关部分的方法⏲先提一下HIve在查询中的作用：hive将SQL查询转换为Mapreduce作业，然后将其提交到Hadoop集群。提交SQL查询时，HIve读取整个数据集。
  
  - ### 静态分区 SP（static partition）
  
      1、静态分区是在编译期间指定的指定分区名🈯
          2、支持load和insert两种插入方式
            2.1load方式`load data local inpath'' into table partition..`
              1）会将分区字段的值全部修改为指定的内容
              2）一般是确定该分区内容是一致的时候才会使用2w3qaw
            2.2insert方式
              1）必须先将数据放在一个没有设置分区的普通表中
              2）该方式可以在一个分区内存储一个范围的内容
              3）从普通表中选出的字段不能包含分区字段
          3、适用于分区数少，分区名可以明确的数据
  
  - ### 动态分区 DP（dynamic partition）
  
    1、根据分区字段的实际值，动态进行分区💽
     2、是在sql执行的时候进行分区
     3、需要先将动态分区设置打开（set hive.exec.dynamic.partition.mode=nonstrict ）
     4、只能用insert方式`insert into table partition(分区名) select () from table ..`
     5、通过普通表选出的字段包含分区字段，分区字段放置在最后，多个分区字段按照分区顺序放置
  
  ​		6、增加分区`alter ... add partition() localtion....`
  
  ​		7、删除分区后，将同时删除元数据和数据

### 5.4Hive分桶

- Hive中的分桶是把数据分解为多个范围的概念，用于为数据提供额外的结构，以便用于更高效的查询。bucket的范围由一个或多个列的哈希值决定，这些列被称为“bucketing”列或者“clustering by”列。
- 通常的我们会在 **😀分区数量有限或者分区的大小相对相等的情况下选择分区**，但是对于一些数据分布并不均等的情况，这种分区时不不理想的，因此引入了bucket的概念。
- 如果要使用分桶需要设置以下属性📜
  - `set hive.enforce.bucketing = true;`
  - `set hive.exec.dynamic.partition.mode=nonstrict;`

- 创建分桶`create table t_name() partitioned by() clustered by() into 几个 buckets.....`
- 加载数据`insert into table t_name partition() select ...from table_...`

### 5.5Hive模式设计

#### 5.5.1日期表

- 按照日期分区来创建表：

 	  `create table data_table(id int,part string ) partitioned by(int date);`

- 一次加载多个进程

```sql
Hive可以在加载数据源后执行多个操作，而无需每次加载数据  like selcet * from table 第二次查询就可以不屑 这是不是就是哪个qt1 hive三大引擎。
```

- Hive中不同类型的文件
  - 文本文件：默认文件，但是又很大的磁盘开销和数据解析开销
  - 序列文件：hadoop api提供的二进制文件，易于使用，可拆分，可压缩。以二进制格式存储
  - 自定义格式：用户可以通过实现iputformat和outputformat自定义输入和输出格式
  - AVRO文件：它是一种文件格式加上一个序列化和反序列化框架
  - 列文件格式：不仅存储彼此相连的数据行，还存储彼此相邻的列值。因此数据集在水平和垂直方向都分了区。有三种重要的柱状文件格式 -RC文件 ORC文件 Parquet文件
    - RC文件：二进制键值对组成的平面文件，与序列化文件相似
    - ORC文件：rc的更好版本：有更快的读取速度，但写入性能较差，提供显著的块压缩，可以用高压缩比进行压缩。
    - Parquet：是一种Hadoop最著名的文件格式（柱状文件格式），以平面柱状格式存储嵌套数据结构，读取速度提升，但写入性能较差

### 5.6导出Hive数据

```shell
mkdir /jiaoben/ouput   

set hive.cli.print.header = true;
insert overwrite local directory '/jiaoben/ouput' row format delimited fields terminated by ' ' lines terminated by '\n' select * from wd;
```

## 6.HQL语法


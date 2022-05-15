---
title: "Hive"
date: 2022-02-23T15:24:22+08:00
image: "b2.webp"
draft: false
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
  - JDBC Driver：用于hive和java之间建立连接
  - **ODBC Driver**：允许自持ODBC协议的应用程序连接到hive 🏷

- HIve Service

  - HIve CLI ：是一个shell，用于**执行Hive查询和命令**
  - HIve Web User Interface:hive cli的替代，提供了相应功能的基于web的GUI
  - HIve Metastore：一个 **中央存储库**，存储仓库中的**各种表和分区的所有结构信息**。还包括列的元数据及其类型信息；Used for 读取和写入数据以及存储数据的相应HDFS文件的序列化程序和反序列化数据。📝
  - HIve Server: 接受来自不同客户端的请求，并将其提供给Hive驱动程序
  - HIve Driver: 接收来自不同来源的查询，它 **将查询传输到编译器**
  - HIve Compiler：目的是 **解析查询🔍并对不同的查询快和表达式执行语义分析**，-->将HQL转化为MapReduce Job

## 2. HIve数据模型

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

### 3.Hive数据类型


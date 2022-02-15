---
title: "Hbase"
date: 2022-02-16T00:53:15+08:00
draft: false
image: b3.jpg
---

```html
<a href=https://www.jianshu.com/p/bcc54f63abe4></a>
```

[toc]



# Hbase

## 1.Overview Hbase

- 是一种分布式 可拓展 支持海量数据存储的NoSQL数据库
- hdfs不支持随机写，基于HDFS，hbase可以实现随时写
- hadoop和habase都是存储大量数据的 但不同的是hadoop的分布式文件系统中，数据分布在网络的不同节点中 而hbase是个数据库 列行的形式存储

### 1.1数据模型

- NameSpace：类似于关系数据库中的database概念，每个ns下有多个表。Hbase自带两个ns：hbase和default.hbase。 hbase存放的是HBase内置的表，default表是用户默认使用的命名空间。

- Region：表的切片 。刚开始创建就一个region
- Row：Hbase表中的每行数据都由一个Rowkey和多个列组成，数据是按照Rowkey的字典序进行存储的，**并且查询时只能根据rowkey进行查询**
- Column：hbase每个列都由Column Family和Column Qualifier（列限定符）进行限定
- TimeStamp：用于标识数据的不同版本，数据仔写入时，不指定的话，系统会自动为其加上该字段，值为写入hbase的时间
- Cell：由{Rowkey，clolumn Family，clolumn Qualifier，timeStamp}唯一确定的单元，**cell中的数据是没有类型的，全部是字节码的形式存在。**

###  1.2 Hbase基本架构

- Store-->Region-->Region Server(分布式服务)
  - RegionServer：DML 对表中数据进行操作
    - Data：get，put（提交新数据覆盖元数据，而不是修改元数据），delete
    - Region：splitRegion，compactRegion     切分和合并
- Master挂掉的话，数据的增删改查仍可以，但是做表结构的不行  **master管理ddl**
  - Master：DDL 对表进行操作
    - Table：create,delete,alter  **关于表进行操作**
    - RegionServer：分配Regions到每个Regionserver，监控每个RegionServer的status
  - Zookeeper：**HBase通过Zookeeper来做master的高可用**、RegionServer的监控，元数据的入口以及集群配置的维护等工作

### 1.3 Hbase的安装和配置





### 1.4 HBase Shell操作

```bash
#general
status：
#ddl
alter：
1）add 列族到表中
alter '表名'，NAME=>'列族名'，VERSIONS=>5
alter 't1', 'f1', {NAME => 'f2', IN_MEMORY => true}, {NAME => 'f3', VERSIONS => 5}

2) delte 列族
alter "ns:表名",NAME=>'列族名'，METHOD=>'delete'
alter 'ns:表名','delete'=>'f1'

3)change table-scope的属性 like below
alter 'table 
name',MAX_FILESIZE=>'134217728'

4)设置coprocessor   不知道是否仔niit范围内，可以课下学学

5) 通过hbase命令行设置habse的配置信息

6) set Regin_Replication
alter 'table_name',{REGION_REPLICATION=>2}

7)disable/enable table split or merge
alter 'tn',{SPLIT_ENABLED=>false}
alter 'tn',{MERGE_ENABLED=>false}

alter_status:#Get the status of the alter command. Indicates the number of regions of thetable that have received the updated schema ,Passed by table name.

create:
create 'ns:tn',{NAME=>'',VERSIONS=>},{},{}
desc 'ns;tn'

disable:
disable 'ns:tn'
disable_all

drop: should be executed after "disable"
drop 'ns:tn'
drop_all

enable:
enable 'ns:tn'
enable_all

exits:判断表是否存在
exists 'ns;tb'

get_table: #Get the given table name and return it as an actual object to be manipulated by the user


#namespacelis

create_namespace 'ns1'
describe_namespace 'ns1' desc不行
drop_namespace ''
list_namespace

#DML
append 't1','r1','c1','value'相当于String的append value加在原有value的后

count: 计数一个表中的row数量
count 'tn'
或者建立一个t->'tn'的reference
t.count也可以

#####Get &&&& Scan

get的最大范围是指定到rowkey，甚至还可以指定到时间戳
#利用scan查看同一个cell之前已经put的数据（scan时可以设置是否开启RAW模式，开启RAW模式会返回已添加删除标记但是未实际进行删除的数据）
#get获取某个cell保留的（未添加删除标记）的所有version数据（在describe 表名，查看列族VERSIONS是多少，get就会多少数据(cell的数据大于等于VERSIONS的数量)）


```

### 1.5Hbase的读写流程

**对于hbase来说  读比写要慢**

- 写流程
  - like put的流程：首先client会----请求meta表所在的RegionServer---> ZooKeeper----meta:hadoop02----->client   **meta表存的就是元数据**    然后client---请求meta---->hadoop02------返回meta 获取RS----->client-----元数据存入缓存----->meta cache(下次查找会先查询缓存，不存在则请求ZK)  找到后client---发送Put请求---->RegionServer--->wal---->memstore----->client
  - 注意windows时间与linux时间未统一的话，不要传时间戳参数



### 1.6Hbase特性

- 容量大
- 面向列
- 稀疏性：null的空列不占用存储空间
- 拓展性：Hbase可以动态增加regionserver
- 高性能：LSM数据结构



### 1.7应用场景

- 搜索引擎
- 增量数据存储
- OPENTSDB
- 捕捉用户交互数据
- 广告效果和点击流
- 内容服务
- 信息交换

### 1.8Hbase与关系型数据库的区别

- HBase 里面有以下 2 个主要概念：
  -  Rowkey: HBase 中的记录是按照 rowkey 来排序的； 
  -  Column family：(列族)是在系统启动之前预先定义好的；
- HBase 优缺点：
  - 不支持条件查询以及 orderby 等查询；
  - 列可以动态增加，列为空则不存储数据，节省存储空间； 
  - 会自动切分数据；
  - 可以提供高并发读写操作的支持；
- 注意事项
  - Row key 行键 (Row key)可以是任意字符串(最大长度是 64KB，实际应用中长度一般为 10- 100bytes)，在 hbase 内部，row key 保存为字节数组。
  - 列族是表的 schema 的一部分(而列不是)，必须在使用表 之前定义。
  - HBase 中通过 row 和 columns 确定的为一个存贮单元称为 cell。每个 cell 都保存着同一份数据的 多个版本
  - cell 中的数据是没有 类型的，全部是字节码形式存贮。
  - **HBASE 强依赖 hadoop 的 hdfs 系统，hbase 的版本需要和 hadoop 的版本匹配，否则会出现一些意 外情况**

### 1.9hbase的启动命令

- 启动单机模式：**bin/start-hbase.sh**
- 查看进程：**jps**
- 启动集群：start-hbase。sh



## 2.Hbase基础

### 2.1 Hbase表的物理属性

- Table 在行的方向上分割为多个 Region；
- Region 按大小分割的，每个表开始只有一个 region，随着数据增多，region 不断增 大，当增大到一个阀值的时候，region 就会等分会两个新的 region，之后会有越来 越多的 region； 
- Region 是 Hbase 中分布式存储和负载均衡的**最小单元**，**不同 Region 分布到不同 RegionServer 上。**

### 2.2Region

- Region组成

  - Region 由一个或者多个 Store 组成，每个 store 保存一个 columns family；
  - 每个 Strore 又由一 个 memStore 和 0 至多个 StoreFile 组成，StoreFile 包含 HFile；memStore 存储在内存中， StoreFile 存储在 HDFS 上
  - Region 虽然是分布式存储的最小单元，但并不是存储的最小单元，Region 默认大小是 256M，当一个 Region 大小超过设置的值，Hmaster 会将 Region 拆分为 2 个子 Region，同时父 Region 下线。

- RegionServer(DML)

  - Region server 维护 Master 分配给它的 region，处理对这些 region 的 IO 请求 
  - Region server 负责切分在运行过程中变得过大的 regio

- Hmaster

  - 管理用户对 Table 的增、删、改、查操作 
  - 管理 RegionServer 的负载均衡、调整 Region 的分布  
  - 在 Region Split 后，将新 Region 分布到不同的RegionServer。  
  - 在 RegionServer 宕机后，那该 RegionServer 上所管理的 Region 由 HMaster 进行重新分 配。

- 管理类

  - HbaseAdmin：提供接口关系 HBase 数据库中的表信息

  - ```java
    HBaseAdmin admin = new HBaseAdmin(config);
    
    ```

  - HTableDescriptor:维护表的信息

  - ```java
    setMaxFileSize，指定最大的 region size
    setMemStoreFlushSize 指定 memstore flush 到 HDFS 上的文件大小
    通过 addFamily 方法增加 famil
    ```

  - HColumnDescriptor: 代表的是 column 的 schema，

  - ```java
    setTimeToLive:指定最大的 TTL,单位是 ms,过期数据会被自动删除。
     setInMemory:指定是否放在内存中，对小表有用，可用于提高效率。默认关闭
     setBloomFilter:指定是否使用 BloomFilter,可提高随机查询效率。默认关闭
     setCompressionType:设定数据压缩类型。默认无压缩。
     setMaxVersions:指定数据最大保存的版本个数。默认为 3。
        
    HTableDescriptor htd =newHTableDescriptor(tablename);
    htd.addFamily(new HColumnDescriptor(“myFamily”));
    
    
    ```





## 3.

### 1.HBase的存储逻辑

- HBase 的表中的数据分隔是使用列族而不是列。每个列族对应一个 HFile 文件，在 HDFS 上以一个 目录的形式存放。
- Key-Value存储模型：
  - key是RowKey，Value是列族的集合。--->也就是说我们可以通过rowkey检索到value
  - 行键的设计：、
    - 表扫描是对行键的操作，所以，**行键的设计控制着你能够通过 HBase 执行的实时/直接获取量。**
    - 当在生产环境中运行 HBase 时，它在 HDFS 上部运行，数据基于行键通过 HDFS，如果你所有的 行键都是以 user-开头，那么很有可能你大部分数据都被分配一个节点上（违背了分布式数据 的初衷），因此，你的行键应该是有足够的差异性以便分布式地通过整个部署。
- 在Hbase中表可以设计为高表(tall-narrow table)和宽表(flat-wide table)的形式  高表指的是列少行多  宽表反之








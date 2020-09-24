[toc]

### Ⅰ.Hadoop 是什么

1. Hadoop 是一个由Apache基金会所开发的<font color='red'>分布式系统基础架构</font>.
2. 主要解决, 海量数据的存储和分析计算问题. (这其实是大数据技术所要解决的问题.)
3. 广义上来说, Hadoop通常是指一个更广泛的概念 --- Hadoop生态.

### Ⅱ.Hadoop 发展历史

1. Lucene框架是<font color='red'>Doug Cutting</font>开创的开源软件, 用Java书写代码, 实现与Google类似的全文搜索功能, 它提供了全文检索引擎的架构, 包括完整的查询和索引引擎.
2. 2001年底Lucene成为Apache基金会的一个子项目.
3. 对于海量数据的场景, Lucene面对与Google同样的问题, 存储数据困难, 检索速度慢.
4. 学习和模仿Google解决这些问题的办法, 微型版Nutch.
5. 可以说谷歌是Hadoop的思想之源(Google在大数据方面的三篇论文)
   - GFS --> HDFS
   - Map-reduce --> MapReduce
   - BigTable --> HBase
6. 2003 - 2004年, Google公开了部分GFS和MapReduce思想的细节, 以此为基础Doug Cutting等人用两年业余时间实现了DFS和MapReduce的机制, 使Nutch性能飙升.
7. 2005年Hadoop作为Lucene的子项目Nutch的一部分正式引入Apache基金会.
8. 2006年3月, Map-Reduce和Nutch Distributed File System 分别纳入到Hadoop项目中, Hadoop就此正式诞生, 标志着大数据时代来临.
9. (名字,Logo,来源于Doug Cutting儿子的玩具大象).

### Ⅲ.Hadoop三大发行版本

Hadoop三大发型版本, Apache, Cloudera, Hortonworks.

Apache 版本最原始的版本, 对于入门学习最好

Cloudera内部继承了很多大数据框架. 对应产品CDH.

Hortonworks文档较好, 对应产品HDP.

### Hadoop的优势(4高)

1.   高可靠性：Hadoop底层维护多个数据副本，所以即使Hadoop某个计算元素或存储出现故障，也不会导致数据的丢失。  
2. 高扩展性：在集群间分配任务数据，可方便的扩展数以千计的节点  
3. 高效性：在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。  
4.  高容错性：能够自动将失败的任务重新分配。  

### Ⅳ.Hadoop的组成

#### 1.x时代

1. MapReduce (计算 + 资源调度)
2. HDFS (分布式存储系统)
3. Common (辅助工具)

#### 2.x 3.x 时代

1. MapReduce (计算)
2. Yarn (资源调度)
3. HDFS (分布式存储系统)
4. Common (辅助工具)

#### Hadoop1.x 和Hadoop2.x,3.x 版本的区别

在Hadoop1.x时代, Hadoop中的MapReduce同时处理业务逻辑运算和资源的调度, 耦合性较大

在Hadoop2.x时代, 增加了yarn, 它只负责资源调度, MapReduce只负责运算.

#### HDFS 架构概述

三个组成部分

1. NameNode (nn)

   存储文件的元数据, 如文件名, 文件目录结构, 文件属性(比如生成时间,副本数,文件权限),以及每个文件的块列表和块所在的DataNode等. 

2. DataNode (dn)

   在本地文件系统存储的文件块数据, 以及块数据的校验和

3. SecondaryNameNode (2nn)

   每隔一段时间对NN元数据备份 (并非热备份, 而是记录操作过程)

#### Yarn

1. ResourceMananger (RM)
2. NodeMananger
3. ApplicationMaster
4. Container

#### MapReduce

主要分为两个阶段 Map 和Reduce

1. Map阶段并行处理输入数据
2. Reduce阶段对Map 结果进行汇总

### Ⅴ.大数据生态体系
[![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_532aaac9bb87c44a7fe1fc124e684dc7.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_532aaac9bb87c44a7fe1fc124e684dc7.jpg)
图中涉及的技术名词解释如下：
1）Sqoop：Sqoop是一款开源的工具，主要用于在Hadoop、Hive与传统的数据库（MySql）间进行数据的传递，可以将一个关系型数据库（例如 ：MySQL，Oracle 等）中的数据导进到Hadoop的HDFS中，也可以将HDFS的数据导进到关系型数据库中。
2）Flume：Flume是一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据； 
3）Kafka：Kafka是一种高吞吐量的分布式发布订阅消息系统； 
4）Storm：Storm用于“连续计算”，对数据流做连续查询，在计算时就将结果以流的形式输出给用户。
5）Spark：Spark是当前最流行的开源大数据内存计算框架。可以基于Hadoop上存储的大数据进行计算。
6）Flink：Flink是当前最流行的开源大数据内存计算框架。用于实时计算的场景较多。
7）Oozie：Oozie是一个管理Hdoop作业（job）的工作流程调度管理系统。
8）Hbase：HBase是一个分布式的、面向列的开源数据库。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。
9）Hive：Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供简单的SQL查询功能，可以将SQL语句转换为MapReduce任务进行运行。 其优点是学习成本低，可以通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用，十分适合数据仓库的统计分析。
10）ZooKeeper：它是一个针对大型分布式系统的可靠协调系统，提供的功能包括：配置维护、名字服务、分布式同步、组服务等。
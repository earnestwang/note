>NoSQL, 它是Not only SQL 的简称.它意味着这种类型的数据库不是一个支持SQL主要查询语言的关系型数据库, 除了今天学习的HBase外还有很多这种数据库.

[toc]

### I. Hbase组成

#### Master

实现类 HMaster

主要功能

1. 把Region分配到某个RegionServer
2. 如果有RegionServer宕机了, HMaster可以把这台机器上的Region迁移到状态为active的RegionServer上.
3. 监控RegionServer, 并对其进行负载均衡,故障转移
4. 对表进行操作: create, delete, alter这些操作可能要跨越多个RegionServer, 因此需要Master进行协调

#### RegionServer

实现类 HRegionServer 

主要功能: 

1. 它是一个服务, 负责多个Region的管理.
2. 对于数据的操作: get, put, delete;
3. 对于Region的操作 : splitRegion, compactRegion
4. 客户端从ZK获取RegionServer的地址, 从而调用相应的服务, 获取数据

#### Zookeeper

​	RegionServer 非常依赖ZK, ZK管理的Hbase所有RegionServer的元数据信息. 客户端连接Hbase时, 第一步就是与ZK通信, 查出元数据信息, 得到需要连接的RegionServer的信息.(会将查出来的元数据信息缓存到本地方便下次查询). 然后再连接RegionServer.这个元数据表是 hbase: meata. 因此zk服务若没有启动, 客户端是无法进行操作的.

​	此外 Hbase 通过zk 来做Master的高可用, RegionServer的监控, 元数据的入口以及集群配置的维护工作.

#### HDFS

​	HDFS为Hbase提供最终的底层数据存储服务, 同时为Hbase提供高可用的支持.

### II. 核心概念

#### Table

​	Hbase是用表来存储数据的. 不过和RDBMS不同的是, Hbase定义的时候只需要声明列族即可, 数据属性, 比如超时时间(TTL), 压缩算法(Compression) 等, 都在列族的定义中定义.不需要声明具体的列. 这就意味着, 往Hbase写入数据的时候, 字段可以动态,按需指定. 因此, 和RDBMS相比, Hbase能够更轻松应对字段变更的场景.

#### NameSpace

​	命名空间,类似于关系型数据库的Database的概念.指对一组表的逻辑分组.方便对表在业务上进行划分.Hbase系统默认定义了两个缺省的namespace.

1. hbase :

   系统内建表,包含namespace和meta表

2. default

   用户创建表时未指定namespace的表都创建在这里.

#### Row Key

​	行键,由用户指定的一串不重复的字符串定义, 是 一行的唯一表示. 数据是根据RowKey的字典顺序进行存储的, 并且查询数据时只能根据Rowkey进行检索, 所以RowKey的设计十分重要.

如果使用了之前已经定义的RowKey, 那么会更新掉之前的数据.

#### Column Family

​	列族是多个列的集合. 一个列族可以动态地灵活定义多个列. 表的相关属性大部分都定义在列族上, 同一个表里的不同列族可以有完全不同的配置属性, 但是一个列族内的所有列都会有相同的属性.

​	列族存在的意义是HBase会把相同列族的列尽量放在同一机器上, 所以说, 如果想让某几个列被放在一起, 你就给他们定义相同的列族.

​	官方建议的是一张表的列族定义越少越好, 因为列族太多会极大影响数据库的性能, 且目前版本的HBase架构, 容易出现Bug.

#### Column Qualifier

​	HBase 中的列是可以随意定义的, 一个行中的列不限名字, 不限数量, 只限定列族.因此此列必须依赖于列族存在. 列的名称前必须带着其所属的列族. 中间用列限定符  ":" 分隔开.

​	就是列族下的每个子列名称，或者称为相关列，或者称为限定符，只是翻译不同。通过`columnFamily:column`来定位某个子列。 

#### TimeStamp

​	用于标识数据的不同版本(version), 时间戳默认由系统指定, 也可以根据用户显示指定. 在读取单元格的数据时，版本号可以省略，如果不指定，Hbase默认会获取最后一个版本的数据返回！

#### Cell

​	我们从外观看每个单元格其实都对应着多个存储单元, 默认情况下一个单元格对应着一个存储单元, 一个存储单元可以存储一份数据, 如果一个单元格有多个存储单元就表示一个单元格可以存储多个值. 可以通过version来设置存储单元的个数. 可以通过 rowkey + columnFamily + column + timestamp(version) 来唯一确定一个存储单元, Cell 中的数据是没有类型的, 全部都是字节码形式存储. Hbase 按照时间戳降序排列各个版本, 其他映射按照升序排列.

### III. HBase Shell

输入以下命令进入shell

```
[wmj@hadoop102 hbase]$ bin/hbase shell
```

如果配置了环境变量

```
[wmj@hadoop102 hbase]$ hbase shell
```

#### 命令们

| 命名          | 描述                                                         | 语法                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| help ‘命令名’ | 查看命令的使用描述                                           | help ‘命令名’  help 'hbase'  可查看全部命令                  |
| whoami        | 我是谁                                                       | whoami                                                       |
| version       | 返回 hbase 版本信息                                          | version                                                      |
| status        | 返回 hbase 集群的状态信息                                    | status                                                       |
| table_help    | 查看如何操作表                                               | table_help                                                   |
| create        | 创建表                                                       | create ‘表名’, ‘列族名 1’, ‘列族名 2’, ‘列族名 N’            |
| alter         | 修改列族                                                     | 添加一个列族：alter ‘表名’, ‘列族名’ 删除列族：alter ‘表名’, {NAME=> ‘列族名’, METHOD=> ‘delete’} |
| describe      | 显示表相关的详细信息                                         | describe ‘表名’                                              |
| list          | 列出 hbase 中存在的所有表                                    | list                                                         |
| exists        | 测试表是否存在                                               | exists ‘表名’                                                |
| put           | 添加或修改的表的值                                           | put ‘表名’, ‘行键’, ‘列族名’, ‘列值’ put ‘表名’, ‘行键’, ‘列族名: 列名’, ‘列值’ |
| scan          | 通过对表的扫描来获取对用的值                                 | scan ‘表名’ 扫描某个列族： scan ‘表名’, {COLUMN=>‘列族名’} 扫描某个列族的某个列： scan ‘表名’, {COLUMN=>‘列族名: 列名’} 查询同一个列族的多个列： scan ‘表名’, {COLUMNS => [ ‘列族名 1: 列名 1’, ‘列族名 1: 列名 2’, …]} |
| get           | 获取行或单元（cell）的值                                     | get ‘表名’, ‘行键’ get ‘表名’, ‘行键’, ‘列族名’              |
| count         | 统计表中行的数量                                             | count ‘表名’                                                 |
| incr          | 增加指定表行或列的值                                         | incr ‘表名’, ‘行键’, ‘列族: 列名’, 步长值                    |
| get_counter   | 获取计数器                                                   | get_counter ‘表名’, ‘行键’, ‘列族: 列名’                     |
| delete        | 删除指定对象的值（可以为表，行，列对应的值，另外也可以指定时间戳的值） | 删除列族的某个列： delete ‘表名’, ‘行键’, ‘列族名: 列名’     |
| deleteall     | 删除指定行的所有元素值                                       | deleteall ‘表名’, ‘行键’                                     |
| truncate      | 重新创建指定表                                               | truncate ‘表名’                                              |
| enable        | 使表有效                                                     | enable ‘表名’                                                |
| is_enabled    | 是否启用                                                     | is_enabled ‘表名’                                            |
| disable       | 使表无效                                                     | disable ‘表名’   用于维护表                                  |
| is_disabled   | 是否无效                                                     | is_disabled ‘表名’                                           |
| drop          | 删除表                                                       | drop 的表必须是 disable 的 disable ‘表名’ drop ‘表名’        |
| shutdown      | 关闭 hbase 集群（与 exit 不同）                              |                                                              |
| tools         | 列出 hbase 所支持的工具                                      |                                                              |
| exit          | 退出 hbase shell                                             |                                                              |

### IV. DDL

#### create

```
#语法
create 'table_name', {NAME => 'columnFamily_name1'}, {NAME => 'columnFamily_name2'}
#简写方式
create 'table_name', 'columnFamily_name1','columnFamily_name2'

create 'table_name', {NAME => 'columnFamily_name1', VERSIONS => version, TTL => ttl, BLOCKCACHE => true}

#e.g.
create 'tbl_user','info','detail'
create 't1', {NAME => 'f1', VERSIONS => 1, TTL => 2592000, BLOCKCACHE => true}
```

#### alter

1. 添加列族

   ```
   #语法
   alter 'table_name','columnFamily_name'
   #e.g.
   alter 'tbl_user','address'
   ```

2. 删除列族

   ```
   #语法
   alter ()
   ```

   
> 官方文档地址: 
>
>  https://hadoop.apache.org/docs/r2.7.6/ 

[toc]

### Ⅰ. Hadoop目录结构

```
[G20-WMJ@westgis168 hadoop-2.7.6]$ ll
total 112
drwxr-xr-x. 2 G20-WMJ G20-WMJ   194 Apr 18  2018 bin
drwxr-xr-x. 3 G20-WMJ G20-WMJ    20 Apr 18  2018 etc
drwxr-xr-x. 2 G20-WMJ G20-WMJ   106 Apr 18  2018 include
drwxr-xr-x. 3 G20-WMJ G20-WMJ    20 Apr 18  2018 lib
drwxr-xr-x. 2 G20-WMJ G20-WMJ   239 Apr 18  2018 libexec
-rw-r--r--. 1 G20-WMJ G20-WMJ 86424 Apr 18  2018 LICENSE.txt
-rw-r--r--. 1 G20-WMJ G20-WMJ 14978 Apr 18  2018 NOTICE.txt
-rw-r--r--. 1 G20-WMJ G20-WMJ  1366 Apr 18  2018 README.txt
drwxr-xr-x. 2 G20-WMJ G20-WMJ  4096 Apr 18  2018 sbin
drwxr-xr-x. 4 G20-WMJ G20-WMJ    31 Apr 18  2018 share
```

1. bin目录： 存放对Hadoop相关服务（HDFS,YARN）进行操作的脚本
2. etc目录： Hadoop的配置文件目录，存放Hadoop的配置文件
3. lib 目录： 存放Hadoop的本地库（对数据进行压缩解压缩功能）
4. sbin目录：存放启动或停止Hadoop相关服务的脚本
5. share目录：存放Hadoop的依赖jar包、文档、和官方案例

### Ⅱ. Hadoop运行模式

#### 本地运行模式(不想玩直接跳过,看完全分布式模式)

- Hadoop默认配置就是本地模式，因此不需要进行任何设置即可运行本地模式.

- 官方案例: 

  1. 准备数据(偷个懒, 不自己造数据了, 直接把配置文件们拿来用)

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ mkdir input
     [G20-WMJ@westgis168 hadoop-2.7.6]$ cp etc/hadoop/*.xml input
     ```

  2. 执行MapReduce程序(grep)

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar grep input output 'dfs[a-z.]+'
     ```

     注意, 输出路径output不能存在, 如果存在,会抛出FileAlreadyExistsException.

  3. 查看输出结果

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ cd output/
     [G20-WMJ@westgis168 output]$ ll
     total 4
     -rw-r--r--. 1 G20-WMJ G20-WMJ 11 Sep 24 19:43 part-r-00000
     -rw-r--r--. 1 G20-WMJ G20-WMJ  0 Sep 24 19:43 _SUCCESS
     ```

     有两个文件, 一个是标识文件, 用来告诉你执行是否成功,里面没有任何内容. 另一个是结果文件.

- Wordcount案例

  1. 准备数据

     就用上面案例的input

  2. 执行命令

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar wordcount input output2
     ```

  3. 查看结果

#### 伪分布模式

用不到吧

#### 完全分布式模式

- 集群规划

|      | westgis168  | westgis169                   | westgis170  | westgis171  | westgis172  | westgis173                  |
| ---- | ----------- | ---------------------------- | ----------- | ----------- | ----------- | --------------------------- |
| HDFS | NameNode    | DateNode                     | DateNode    | DateNode    | DateNode    | SecondaryNameNode//DateNode |
| YARN | NodeManager | ResourceManager//NodeManager | NodeManager | NodeManager | NodeManager | NodeManager                 |

- 集群配置

  1. 配置 hadoop-env.sh(需要配置JAVA_HOME)

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ cd etc/hadoop/
     [G20-WMJ@westgis168 hadoop]$ vim hadoop-env.sh
     ```

     [![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_04808b2e79d82fb0e8d204dacd3e9e65.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_04808b2e79d82fb0e8d204dacd3e9e65.jpg)

  2. 核心配置文件

     core-site.xml

     ```
     [G20-WMJ@westgis168 hadoop]$ vim core-site.xml
     ```

     在configuration标签中添加如下内容( 官方文档都有的 )

      ```
     <!-- 指定NameNode的地址 -->
     <property>
     	<name>fs.defaultFS</name>
     	<value>hdfs://westgis168:8020</value>
     </property>
      ```

     hdfs-site.xml

     ```
     <property>
     	<name>dfs.replication</name>
     	<value>3</value> 
     </property>
     <property>
     	<name>dfs.namenode.name.dir</name>
     	<value>/home/G20-WMJ/bigdata/hadoop-2.7.6/hdfs/name</value> 
     </property>
     <property>
     	<name>dfs.datanode.data.dir</name>
     	<value>/home/G20-WMJ/bigdata/hadoop-2.7.6/hdfs/data</value> 
     </property>
     <!--
     此处dfs.replation为数据的备份个数，一般情况为3
     此处/home/G20-WMJ/bigdata/hadoop-2.7.6/hdfs/name为元数据在NameNode本地存储路径，手动创建
     此处/home/G20-WMJ/bigdata/hadoop-2.7.6/hdfs/data为实际数据在DataNode本地存储路径，手动创建
     -->
     注意!!! 后两个配置项的目录要提前建好!!!
     ```

     slaves (workers)

     将规划的DataNode **主机名都写进去**

     ```
     westgis169
     westgis170
     westgis171
     westgis172
     westgis173
     ```

     至此, 配置完成

     (其实配置项在哪个配置文件里都无所谓,最后读取的时候会加载到一起, 分开只是为了方便程序员配置.)

  接下来需要将JDK, Hadoop分发到集群的各个节点. 为了不频繁的输入密码,我们先配置一个免密登录👇

  [免密登录](https://itaylor.top/2020/09/24/436/)

- 然后进行分发, 可以一个节点一个节点地scp.

  此处我写了一个分发脚本:(我把当前这个目录[/home/G20-WMJ/bin]也加到环境变量里了)

  ```
  [G20-WMJ@westgis168 bin]$ pwd
  /home/G20-WMJ/bin
  [G20-WMJ@westgis168 bin]$vim xsync
  ```

  ```
  #!/bin/bash
  #1. 判断参数个数
  if [ $# -lt 1 ]
  then
    echo Not Enough Arguement!
    exit;
  fi
  #2. 遍历集群所有机器
  for host in westgis168 westgis169 westgis170 westgis171 westgis172 westgis173
  do
    echo ====================  $host  ====================
    #3. 遍历所有目录，挨个发送
    for file in $@
    do
      #4 判断文件是否存在
      if [ -e $file ]
      then
        #5. 获取父目录
        pdir=$(cd -P $(dirname $file); pwd)
        #6. 获取当前文件的名称
        fname=$(basename $file)
        ssh $host "mkdir -p $pdir"
        rsync -av $pdir/$fname $host:$pdir
      else
        echo $file does not exists!
      fi
    done
  done
  ```

  保存退出后,给这个脚本执行的权限

  ```
  [G20-WMJ@westgis168 bin]$ chomod 777 xsync
  ```

  然后到家目录, 分发bigdata目录和, .bashrc 文件

  ```
  [G20-WMJ@westgis168 ~]$ xsync bigdata/
  [G20-WMJ@westgis168 ~]$ xsync .bashrc
  ```

- 准备启动.

  1. 第一次启动,. 格式化namenode

     ```
     hdfs namenode -format
     ```

  2. 启动

     ```
     [G20-WMJ@westgis168 hadoop-2.7.6]$ pwd
     /home/G20-WMJ/bigdata/hadoop-2.7.6
     [G20-WMJ@westgis168 hadoop-2.7.6]$ sbin/start-dfs.sh
     ```

  3. 然后可以用jps命令查看进程是否启动,(看168上的namenode,其他节点的datanode,173上的SecondaryNamenode) 如果没启动,出问题了, 可以去看日志文件进行错误排查.

  4. 一切顺利, 去web界面看看 在浏览器输入 http://westgis168:50070/ 

     看看DataNode够不够

     






























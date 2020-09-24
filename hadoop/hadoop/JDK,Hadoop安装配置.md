 > 集群单节点配置: CPU 4核, 内存8GB, 硬盘容量1TB.
 >
 > 节点数: 6
 >
 > 用到的工具:  XShell 5 , Xftp 5
 >
 > 用到的安装包: jdk-8u212-linux-x64.tar.gz, hadoop-2.7.6.tar.gz

[toc]



 ### Ⅰ. 连接服务器



 #### Windows系统配置hosts文件

> 为了简便操作和方便记忆, 可以对集群里的所有节点的主机名和ip地址做一个映射.打开C:\Windows\System32\drivers\etc下的hosts文件, (注意以管理员方式打开),添加如下内容



 ```
 10.103.104.168 westgis168
 10.103.104.169 westgis169
 10.103.104.170 westgis170
 10.103.104.171 westgis171
 10.103.104.172 westgis172
 10.103.104.173 westgis173
 ```



 #### XShell 连接到节点



> 以自己的用户登录到服务器, 用户名格式是这样的, G20-姓名全拼大写, 例如我的G20-WMJ. 建议把其中某一台服务器当做主操作节点,其他节点通过同步来cp文件.(这里以westgis168为例)
>
> 具体操作步骤如下

 文件-> 打开 -> 新建 此处输入主机名
 [![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_d82f081e4a6fd6656a0906061547a553.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_d82f081e4a6fd6656a0906061547a553.jpg)

输入用户名密码: 密码统一设置的123456

[![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_5753ffe12c8ca584840224ddb136e6af.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_5753ffe12c8ca584840224ddb136e6af.jpg)

然后会弹出一个框, 点接受并保存即可.	出现以下内容即为登陆成功

[![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_7a9fdaebca8c43507fd990a233c95970.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_7a9fdaebca8c43507fd990a233c95970.jpg)

### Ⅱ. JDK安装配置

#### JDK下载安装

cd到自己的家目录下,新建一个文件夹放自己的文件

```
[G20-WMJ@westgis168 ~]$ cd
[G20-WMJ@westgis168 ~]$ pwd
/home/G20-WMJ
[G20-WMJ@westgis168 ~]$ mkdir bigdata
[G20-WMJ@westgis168 ~]$ cd bigdata/
```

省去下载的过程(自行百度)

将下载的jdk的tar.gz文件传输到bigdata目录下.

[![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_05aa176e76ae823ee7f840f4bddb79c2.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_05aa176e76ae823ee7f840f4bddb79c2.jpg)

👆此步骤确保已经安装XFtp 5 (如果没有装好像会自动装)

打开后从左边本地文件系统找到jdk, 即jdk-8u212-linux-x64.tar.gz这个文件.传输到你刚刚建的bigdata目录下. 

此刻/home/G20-WMJ/bigdata目录下已经有了jdk的安装包,解压即可

```
[G20-WMJ@westgis168 bigdata]$ tar -zxvf jdk-8u212-linux-x64.tar.gz -C .
```

#### JDK配置

接下里需要配置jdk的环境变量, 看这篇文章👇 (单个用户变量配置)

[ https://itaylor.top/2020/09/16/388 ]( https://itaylor.top/2020/09/16/388 )

#### 验证

输入java -version 命令, 如果出现👇



即为成功.



### Hadoop安装配置

#### 下载安装

下载省略, 安装同jdk的, 直接将文件放到你的bigdata目录下, 解压即可

#### 配置

配置环境变量, 与jdk一样~

配置HADOOP_HOME 修改PATH

#### 测试是否成功
[![](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_868e98f495b45433c9938d6dfeecd76b.jpg)](https://itaylor.top/wp-content/uploads/2020/09/wp_editor_md_868e98f495b45433c9938d6dfeecd76b.jpg)


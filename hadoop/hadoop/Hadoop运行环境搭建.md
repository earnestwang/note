### Ⅰ.Hadoop 运行环境搭建

> 默认已经有了服务器.

 #### 安装JDK

1. 将JDK安装包上传到Linux /opt/software目录下

2. 解压JDK到/opt/module目录下

   tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/

3. [环境变量的配置](https://itaylor.top/2020/09/16/388/)

4. 测试jdk是否安装成功

   ```
   java -version
   
   如果能看到以下结果、则Java正常安装
   
   java version "1.8.0_212"
   
   如果确定操作没错误依然不显示以上内容可以尝试重启服务器.
   sudo reboot
   ```

   
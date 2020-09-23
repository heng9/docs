# HBase1.2.0 windows单机版安装配置

### 准备工作

* jdk-1.8    
* hbase-1.2.0    
* [hadoop-common-2.2.0-bin-master](https://github.com/srccodes/hadoop-common-2.2.0-bin)

### 搭建

* 添加环境变量
	1. 添加 HADOOP_HOME (hadoop-common-2.2.0-bin-master目录)
	2. 在 path 变量后面加上 %HADOOP_HOME%\bin
	3. 如弹出cmd窗口中出现"Failed to locate the winutils binary in the hadoop binary path java.io.IOException: Could not locate executable null\bin\winutils.exe in the Hadoop binaries."报错时，说明机器环境中缺少winutils.exe文件

* 解压 hbase-1.2.0.tar.gz 压缩文件, 设置conf下hbase-env.cmd中的JAVA_HOME：
    set JAVA_HOME=C:\Program Files\Java\jdk1.7.0_79

* 设置conf下hbase-site.xml中的路径
```
<configuration>
    <property> 
        <name>hbase.master</name> 
        <value>localhost</value> 
    </property>
    <property>  
        <name>hbase.master.info.port</name>
        <value>60010</value>
    </property>
    <property>
        <name>hbase.rootdir</name>
        <value>file:///C:/tmp/hbase/root</value>
    </property>
    <property>
        <name>hbase.tmp.dir</name>  
        <value>C:/tmp/hbase/tmp</value>  
    </property>  
    <property>  
        <name>hbase.zookeeper.quorum</name>
        <value>localhost</value>
    </property>  
    <property>  
        <name>hbase.zookeeper.property.dataDir</name>
        <value>C:/tmp/hbase/zoo</value>
    </property>  
    <property>  
        <name>hbase.cluster.distributed</name>
        <value>false</value>  
    </property>  
</configuration>
```
**配置文件中的zookeeper.quorum 为 localhost , zookeeper.port 默认为2181**

* 启动hbase-1.2.0\bin\start-hbase.cmd

* 进入shell终端：在 hbase-1.2.0\bin 目录下运行 hbase shell

* hbase有自带的简单的web界面，默认是不运行的，在hbase-site.xml中加入以下内容即可
```
<property>  
    <name>hbase.master.info.port</name>
    <value>60010</value>
</property>
```
之后访问：http://localhost:60010
    
* hadoop namenode web界面
```
<property>  
    <name>dfs.namenode.http-address</name>
    <value>master:50070</value>
</property>
```
之后访问：http://localhost:50070


# Windows 安装 Spark

1. **准备工作**
    * jdk-1.8
    * [scala](https://www.scala-lang.org/download)
    * [hadoop](http://hadoop.apache.org/releases.html)
    * [spark](http://spark.apache.org/downloads.html)

2. **搭建步骤**
    * jdk
    
    * Scala
        1. 安装scala，配置 SCALA_HOME 和 Path
        2. 通过scala进入命令行
    
    * Hadoop
        1. 解压，配置 HADOOP_HOME 和 Path
        2. [winutils](https://github.com/steveloughran/winutils) 下载对应版本的bin目录，直接替换本地bin目录
      
    * Spark
        1. 解压，配置 SPARK_HOME 和 Path
        2. 通过spark-shell进入命令行
        3. 访问：127.0.0.1:4040
    
3. 退出 spark-shell

    :quit

# docker下的[spark](https://hub.docker.com/r/mesosphere/spark/tags)使用

* 拉取镜像
```
docker pull mesosphere/spark:2.4.0-2.2.1-3-hadoop-2.6
```

* 运行镜像
```
docker run  
--name spark 
--hostname spark 
-p 4040:4040 
-p 8080:8080 
-p 8081:8081 
-p 18080:18080 
-p 7077:7077 
-it  
mesosphere/spark 
bash
```

* spark 端口说明
```
4040：application 的 webUI的端口
8080：master 的 webUI，sparkwebUI 的端口
8081：worker 的 webUI 的端口
18080：historyServer 的 webUI的端口
7077：提交job的端口
```

* 配置环境变量
```
root@spark2:/opt/spark/dist# export PATH=$JAVA_HOME/bin:$PATH
root@spark2:/opt/spark/dist# export SPARK_HOME=/opt/spark/dist
root@spark2:/opt/spark/dist# export PATH=$SPARK_HOME/bin:$PATH
root@spark2:/opt/spark/dist# spark-shell
```

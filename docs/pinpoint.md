# 开源APM工具pinpoint安装与使用
> Pinpoint是用Java编写的大型分布式系统的APM（应用程序性能管理）工具

* Pinpoint-Collector: 收集各种性能数据 
* Pinpoint-Agent: 和自己运行的应用关联起来的探针 
* Pinpoint-Web: 将收集到的数据显示成WEB网页形式 
* HBase Storage: 收集到的数据存到HBase中

# 准备工作
* pinpoint-agent-1.7.1.tar.gz
* pinpoint-collector-1.7.1.war
* pinpoint-web-1.7.1.war
* hbase

地址 https://github.com/naver/pinpoint/tags


# pinpoint安装
###1. 安装hbase
* 下载初始化脚本(hbase-create.hbase)，地址 https://github.com/naver/pinpoint/tree/master/hbase/scripts
* 运行 start-hbase.cmd 启动hbase
* 运行脚本: hbase shell %初始化库体保存路径%\hbase-create.hbase
* 访问 http://localhost:60010/master-status，查看库体初始化是否成功
    
###2. pinpoint-collector部署
* 准备tomcat1
* 解压pinpoint-collector-1.7.1.war，并将解压后的文件放在 webapps\ROOT目录下
* 配置ROOT/WEB-INF/classes/hbase.properties， 启动tomcat

###3. pinpoint-web部署
* 准备tomcat2
* 解压pinpoint-web-1.7.1.war，并将解压后目录中的文件放在 webapps\ROOT目录下
* 配置ROOT/WEB-INF/classes/hbase.properties， 启动tomcat
* 访问tomcat地址，显示管理界面

附：可以看出 pinpoint-collector 与 pinpoint-web 通过 hbase 交互


# pinpoint使用
### 1. pinpoint-agent部署
* 将 pinpoint-agent-1.7.1.tar.gz 拷贝到 web服务器 并解压到任意目录
* 修改 pinpoint.config 文件中 Collector server 的配置参数，profiler.collector.ip=127.0.0.1
* 如果web服务和pinpoint系统部署在同一服务器上，profiler.collector.ip可设置为localhost，否则设置为 profiler.collector 所在服务器ip地址。

###2. web服务器监控配置
在被监测的Tomcat目录bin下找到catalina.bat文件，使用文本编译器在其中添加如下参数：
* -javaagent:D:\DeveloperKit\pinpoint-agent-1.7.1\pinpoint-bootstrap-1.7.1.jar
* -Dpinpoint.agentId=jforum32tomcat  #设置采集器agent的id名称，全局唯一
* -Dpinpoint.applicationName=jforum  #设置被监控程序名称，全局唯一
        
###3. 启动jar
```
java \
-javaagent:D:\DeveloperKit\pinpoint-agent-1.7.1\pinpoint-bootstrap-1.7.1.jar \
-Dpinpoint.agentId=jforum32tomcat \
-Dpinpoint.applicationName=jforum \
-jar spring-boot-app.jar 
```
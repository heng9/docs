# Windows 安装 ELK

> ELK 是 elastic 公司旗下三款产品 ElasticSearch 、Logstash 、Kibana 的首字母组合。
* ElasticSearch 是一个基于 Lucene 构建的开源，分布式，RESTful 搜索引擎。
* Logstash 传输和处理你的日志、事务或其他数据。
* Kibana 将 Elasticsearch 的数据分析并渲染为可视化的报表。

## logstash

1. **准备工作**
    * jdk-1.8
    * logstash-6.4.2
    
2. **搭建步骤**
    * 解压文件，进入bin目录，新建文件 logstash.conf，输入
``` 
input {
    stdin{
    }
} 
output {
    stdout{
    }
}
```  
    * 执行 logstash -f logstash.conf，访问：http://localhost:9600
    
3. **插件安装**
    * 执行 bin/logstash-plugin install logstash-codec-json_lines
    
    
## ElasticSearch

1. **准备工作**
    * jdk-1.8
    * elasticsearch-6.4.2
    
2. **搭建步骤**
    * 解压文件，运行bin/elasticsearch.bat，访问：http://localhost:9200
    
3. **插件安装**
    * 执行 bin/elasticsearch-plugin install elasticsearch-head，POST方式访问：http://localhost:9200/plugin/head
    
    
## Kibana

1. **准备工作**
    * kibana-6.4.2
    * elasticsearch-6.4.2
    * node
    * 注意：elasticsearch 的版本和 kibana 的版本必须一致，才可以正确运行。
 
2. **搭建步骤**
    * 解压文件，打开 config/kibana.yml，设置 elasticsearch.url 为启动的 elasticsearch（http://localhost:9200），按照默认可以不用修改配置文件
    * 运行bin/kibana.bat，访问：http://localhost:5601   


## Spring 集成 ELK
1. Spring项目加入依赖
```
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>4.11</version>
</dependency>
```

2. Spring项目中logback需配置appender
```
<appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
    <destination>localhost:4560</destination><!-- 4560为发送日志的端口 -->
    <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder" />
</appender>

<logger name="需要记录日志的包名" level="ERROR">
    <appender-ref ref="LOGSTASH" />
</logger>
```

3. logstash 配置如下
```
input {
    tcp {
        port => 4560            //接收日志的端口
        codec => json_lines     //需要安装logstash-codec-json_lines插件
    }
}
output{
    //输出到ElasticSearch
    elasticsearch { 
        hosts => ["localhost:9200"]
        index  => "applog"         //输出日志的index
    }  
    stdout { codec => rubydebug }  //在logstash控制台中输出
}
```

4. 之后在 Kibana 的 Management 面板中添加相应的 index
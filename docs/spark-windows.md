# windows环境下跑通spark streaming wordCount


使用netcat作为数据数据服务器
* 下载netcat
* 解压，将nc.exe拷贝到C:\Windows下

启动TCP套接字连接
```
nc -l -p 9999
``` 


# windows环境下 spark 常见错误
* 在window中运行Hadoop和Spark程序时会遇见如下异常错误  
java.lang.UnsatisfiedLinkError: org.apache.hadoop.util.NativeCrc32.nativeComputeChunkedSums(IILjava/nio/ByteBuffer;ILjava/nio/ByteBuffer;IILjava/lang/String;JZ)V  
解决办法:  
将hadoop.dll、winutil.exe 文件拷贝到 C:/Windows/System32


# spark on yarn 运行常见错误
* 执行 spark-shell --master yarn --deploy-mode client  
将spark 任务提交到yarn上运行时出现错误  
io.netty.buffer.PooledByteBufAllocator.defaultNumHeapArena()I  
解决办法:  
主要是 hadoop 与 spark netty 的jar包版本冲突
用spark netty-all.jar 替换 hadoop 的jar包


* 执行 spark-submit --name <Application-name> --master yarn --class <main-class> <jar>  
NoSuchMethodError: org.apache.spark.deploy.SparkHadoopUtil$.newConfiguration(Lorg/apache/spark/SparkConf;)Lorg/apache/hadoop/conf/Configuration;  
解决办法:  
主要是 spark应用 与 服务器上 spark的版本不一致   
使用相同的spark版本  
例如服务器使用的是: spark-2.4.0 (scala 和 spark的jar包版本可以从 spark的 jars目录查看)  
maven使用  
```xml
<dependencies>
    <dependency>
        <groupId>org.scala-lang</groupId>
        <artifactId>scala-library</artifactId>
        <version>2.11.12</version>
    </dependency>
    
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-sql_2.11</artifactId>
        <version>2.4.0</version>
    </dependency>
    
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-streaming_2.11</artifactId>
        <version>2.4.0</version>
    </dependency>
</dependencies>
```


* 报错 java.lang.NoClassDefFoundError: scala/runtime/java8/JFunction0$mcZ$sp  
主要是应用的scala与服务器上 scala的版本不一致   
使用相同的scala版本
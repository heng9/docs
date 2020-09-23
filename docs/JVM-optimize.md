# JVM自带性能调优工具
> jps, jstack, jmap, jhat, jstat, hprof

### jps(Java Virtual Machine Process Status Tool)
* 查看当前java进程
```
jps
```

* 查看输出传入JVM的参数
```
jps -v
```

### jstack
* 查看某个Java进程内的线程堆栈信息，[-l]会打印出额外的锁信息
```
jstack [-l] pid
jstack [-l] pid > [file_name.jstack]
```

### jmap(Memory Map)
* 查看整个JVM内存状态
```
jmap -heap pid
```
    
* 查看JVM堆中对象详细占用情况
```
jmap -histo[:live] pid
```

* 导出整个JVM中内存信息
```
jmap -dump:format=b,file=<dumpfile.hprof> pid
```

* 通过添加JVM参数，在memory溢出时候能自动生成heap dump文件
```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=<dumpfile.hprof>
```

### jhat(Java Heap Analysis Tool)
* 分析dump文件
```
 jhat <dumpfile.hprof>
 jhat -J-Xmx512m -port 8888 <dumpfile.hprof>
 之后访问对应端口的网址
```

### jstat(JVM统计监测工具)
* 对JVM堆信息采样。interval是采样时间间隔(ms)，count是采样数

```
jstat -gc pid [interval] [count]

S0C、S1C、S0U、S1U：Survivor 0/1区容量(Capacity)和使用量(Used)
EC、EU：Eden区容量和使用量
OC、OU：年老代容量和使用量
PC、PU：永久代容量和使用量
YGC、YGT：年轻代GC次数和GC耗时
FGC、FGCT：Full GC次数和Full GC耗时
GCT：GC总耗时
```


# JVM参数设置
* -Xms512m：设置JVM初始堆内存为512m。此值可以设置与-Xmx相同，避免垃圾回收完成后JVM重新分配内存。
* -Xmx512m：设置JVM最大堆内存为512m。

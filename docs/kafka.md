# Kafka
> Apache Kafka相对于ActiveMQ是一个非常轻量级的消息系统，除了性能非常好之外，还是一个工作良好的分布式系统。

![Kafka](images/Kafka.png)

* broker : Kafka 集群包含一个或多个服务器，服务器节点称为broker
* Topic : 每条发布到Kafka集群的消息都有一个类别(Topic)
* partition : topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列。 
* Producer  : 数据的发布者，将消息发布到Kafka的topic中
* Consumer  : 从broker中读取数据。消费者可以消费多个topic中的数据
* Consumer Group : 每个Consumer属于一个特定的Consumer Group(可为每个Consumer指定group name，若不指定group name则属于默认的group)
* Leader :  每个partition有多个副本，其中有且仅有一个作为Leader，Leader是当前负责数据的读写的partition
* Follower : partition 其余的副本为follower





### kafka log
kafka log的路径配置在conf/server.properties配置文件中: log.dirs = /tmp/kafka-logs  
假设创建一个 名为demo1 的 topic，且只有一个分区和副本  
log文件的命名那一长串0(比如/logs1/demo1-0/00000000000000000000.log)，是这个日志文件的offset位置  
当日志文件达到时间或者大小的上限时，就会生成下一个日志文件，命名的就是下一个offset位置了。  
另外在/logs1/demo1-0还有两个文件，index文件存放的是topic的offset，timeindex是存放的是时间戳   




### kafka offset
offset：指的是kafka的topic中的每个消费组消费的下标。
从kafka-0.9版本，默认将消费的 offset 迁入到了 Kafka 一个名为 __consumer_offsets 的 Topic

1. consumer有两种消息方式存放offset, 与执行kafka-console-consumer时的参数有关
* 存放在broker, 使用 –-bootstrap-server 参数
* 存放在zookeeper, 使用 –-zookeeper 参数 (新版本kafka中，--zookeeper这种启动方式已删除)


2. Kafka consumer auto.offset.reset 配置
* earliest  
当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费  
* latest  
当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据  
* none  
各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的offset，则抛出异常





#### kafka offset 提交的方式
1. 自动提交
Kafka中偏移量的自动提交是由参数 enable_auto_commit 和 auto_commit_interval_ms 控制的，  
当enable_auto_commit=True时，Kafka在消费的过程中会以频率为auto_commit_interval_ms向Kafka自带的topic(__consumer_offsets)进行偏移量提交，  
具体提交到哪个partition是以算法：partition=hash(group_id)%50来计算的。  

2. 手动提交
* spring for kafka的AckMode

|AckMode         |     含义     |
|----------------|-------------|
|RECORD          | 每处理一条commit一次 |
|BATCH(默认)     | 每次poll的时候批量提交一次，频率取决于每次poll的调用频率 |
|TIME            | 每次间隔ackTime的时间去commit |
|COUNT           | 累积达到ackCount次的ack去commit |
|COUNT_TIME      | ackTime或ackCount哪个条件先满足，就commit |
|MANUAL          | listener负责ack，但是背后也是批量上去 |
|MANUAL_IMMEDIATE| listener负责ack，每调用一次，就立即commit |

只有配置 AckMode 为 MANUAL / MANUAL_IMMEDIATE 时, 才能在方法中 使用 Acknowledgment 该参数，  
否则会报: the listener container must have a MANUAL Ackmode to populate the Acknowledgment

         
         


### kafka consumer acks

|     acks       |   含义      |
|----------------|-------------|
|   0            | 只要把消息发送出去， 不管数据是否在Partition Leader上落到磁盘，都认为发送成功 |
|   1(默认设置)   | Partition Leader接收到消息而且写入本地磁盘了，就认为成功了， |
|   all          | Partition Leader接收到消息之后，还必须要求ISR列表里跟Leader保持同步的那些Follower都要把消息同步过去，才能认为这条消息是写入成功了 |


---
title: Kafka 实现原理
date: 2018/12/29
tags: [MQ, 分布式]
---

### 1. 消息可靠性机制
#### 消息发送可靠性
消息发送到 broker 有三种确认方式 `request.required.acks` ：
- **acks=0**  producer 不会等待 broker 发送ack，既可能丢失也可能重发。
- **acks=1**  当 leader 接收到消息后发送ack，可能重发。
- **acks=-1** 当所有 follower 同步成功后发送ack，丢失消息可能性低。

#### 消息存储可靠性
每一条消息发送到broker中，会跟据partition规则存储到对应分区，如果规则设置合理可以实现消息均匀分布到不同分区，实现存储的水平扩展。  
高可靠性的保障来自另一个叫副本(replication)策略，通过设置(--replication-factor)参数设置。

### 2. 副本存储机制
Kafka 在创建 topic 支持设置对应的副本个数 `--replication-factor`，会生成对应的分区副本数交叉存储在每个节点上。

##### 副本存活条件
1. 副本所有节点必须和zookeeper保持连接状态
2. 副本的最后一条消息的offset和leader的最后一条消息的offset之间差值不能超过设定值`replica.lag.max.messages`

##### 如何同步消息
第一个启动的节点成功在zookeeper中注册信息成为leader对外提供服务，其它replica做为follower要定时同步数据。  
- **HW(high watermark)** ：表示所有follower已同步完成的offset位置  
- **LEO(Log End Offset)** ：表示leader节点当前消息的offset位置

consumer只能消费所有follower已同步完成的数据，即HW标注的位置。

##### 如何均匀分布
Kafka 为了更好的做到负载均衡，会尽量把所有的 partition 均匀分配到整个集群上，分配的算法：
1. 把所有 `broker(n)` 和 `partition(n)` 排序
2. 把第`i`个partition分配到 `(i%n)` 个broker上
3. 把第 `i` 个 partition 的第 j 个 replica 分配到 `((i+j) % n)` 个broker上

##### 如何处理所有副本不工作情况
在ISR中至少有一个follower工作时，Kafka可以确保消息不丢失，但如果某个分区所有备份都宕机了，采取以下措施：
1. 等待ISR中任意一个follower活过来，并且选择它为leader
2. 选择任意一个replica(不一定是ISR中的)活过来作为leader

这两种需要在可用性和一致性当中做一个选择。

### 3. Kafka 文件存储机制
#### partition
每个partition为一个目录，命名规则为 `<topic_name>-<partition_no>`，存储在 kafka_logs 目录下。

#### segment
Kafka 为防止分区文件过大，又将分区拆分为 segment 存储，一个segment文件`.index`和`.log`两部分组成，即索引文件和数据文件。  
segment 文件由64位long数值命名，文件名即记录的最大 offset 值，查找时先通过定位 offset 落的范围，再进入文件查找。

#### 日志保留
Kafka 中无论是否消费了消息(只是移动了 offset)，都会一直保留这些消息，为了避免磁盘爆满，使用相应的保留策略(retention policy)，以实现周期性的删除陈旧消息：
- 根据消息保留时间，超过指定时间则删除
- 根据 topic 大小，超过阈值开始删除最旧消息

#### 日志压缩
Kafka 会定期将相同 Key 的消息进行合并，只保留最新的 Value 值。

### 4. Kafka 消息的消费原理
旧版本的 Kafka 将 Consumer group 的消费进度记录在 zookeeper 中，导致对 zookeeper 频繁的写入而性能低下。  
新版本1.0+已修改为记录在对应 topic 目录下，默认创建了50个 `__consumer_offset_topic` 文件夹，将消费的进度 offset 保存在对应的文件中。
1. 计算 consumer group 的哈希值
```java
Math.abs("group1".hashCode() % 50)
```
2. 根据哈希值找到对应的__consumer文件，查看消费进度
```shell
sh kafka-simple-consumer-shell.sh --topic __consumer_offsets --partition 15 -broker-list 192.168.56.110:9092,192.168.56.120:9092,192.168.56.130:9092 --formatter kafka.coordinator.group.GroupMetadataManager\$OffsetsMessageFormatter
```

### 5. Kafka 的消费者分区分配策略
Kafka中存在 consumer group 的概念，也就是group.id一样的consumer属于一个consumer group，组内的所有消费者协调在一起来消费消费订阅主题的所有分区。当然每一个分区只能由同一个消费组内的consumer来消费，那么同一个consumer group里面的consumer是怎么去分配该消费哪个分区里的数据，这个就设计到了kafka内部分区分配策略（Partition Assignment Strategy）。
在 Kafka 内部存在两种默认的分区分配策略：Range（默认） 和 RoundRobin。通过：`partition.assignment.strategy`指定
#### 两种分区策略
##### Range （默认策略）
0 ，1 ，2 ，3 ，4，5，6，7，8，9   
c0 [0,3]   
c1 [4,6]  
c2 [7,9]  
10`(partition num)`/3`(consumer num)` =3  
##### RoundRobin （轮询）
0 ，1 ，2 ，3 ，4，5，6，7，8，9  
c0,c1,c2  
c0 [0,3,6,9]  
c1 [1,4,7]  
c2 [2,5,8]  
kafka 的key 为null， 是随机｛一个Metadata的同步周期内，默认是10分钟｝


### 6. 高吞吐量原因
- **消息顺序存储**，通过 offset 偏移量进行顺序读取，减少机械硬盘磁柱移动。
- **消息批量发送**，在异步模式中允许进行批量发送消息，先将消息缓存到内存，再一次请求中批量发送出去，减少磁盘读写和网络传输。
    - batch.size 每批量发送的数据大小
    - linger.ms  批量发送的间隔时间
- **消息的零拷贝**，使用 `FileChannel.transferTo` 直接将消息发送到 socket buffer 中，省略了将消息读取到内存的过程。

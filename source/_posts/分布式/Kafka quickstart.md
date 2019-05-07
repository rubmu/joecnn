---
title: Kafka quickstart
date: 2018/12/29
tags: [MQ, 分布式]
---

### 1. Kafka是什么?
> [Apache Kafka®](https://kafka.apache.org/documentation/#introduction) is a distributed streaming platform. 

Kafka是一个高性能、高吞吐量的分布式消息通信系统。常用于系统的日志收集分析、消息通信、用户行为分析、服务器指标监控、流式处理等。

### 2. Kafka集群安装
1. 下载kafka二进制安装包, 并解压到 /usr/local目录下。
2. 进入到config目录下修改server.properties配置文件：
    - broker.id 在集群中的唯一编号
    - listeners 本机ip
    - num.partitions 默认的topic分区数
    - zookeeper.connect 连接到zookeeper集群，可以设置根路径 `ip:port/kafka`
3. 进入到bin目录下，以守护进程方式启动。
```bash
$ sh kafka-server-start.sh -daemon ../config/server.properties
```

### 3. Kafka基本操作

1. 创建一个topic
```bash
$ sh kafka-topics.sh --create --zookeeper 192.168.56.110:2181/kafka --replication-factor 1 --partitions 2 --topic first-topic
```

2. 列出所有topic
```bash
$ sh kafka-topics.sh --list --zookeeper 192.168.56.110:2181/kafka
```

3. 生成者发送消息
```bash
$ sh kafka-console-producer.sh --broker-list 192.168.56.110:9092 --topic first-topic
```

4. 消费者接收消息
```bash
$ sh kafka-console-consumer.sh --bootstrap-server 192.168.56.110:9092 --topic first-topic --from-beginning
```

5. 查看消息日志
```bash
$ sh kafka-run-class.sh kafka.tools.DumpLogSegments --files /tmp/kafka-logs/first-topic-0/00000000000000000000.log --print-data-log
```

### 4. Kafka中的概念
####  Message
消息是 Kafka 中最基本的数据单元，由 Key/Value 组成(byte[])，根据Key的哈希值路由到不同区间进行存储。Kafka 会对消息进行压缩和批量发送。

#### Topic
topic 是用于存储消息的逻辑单元，可以看作一个消息集合，每个 topic 可以有多个生产者向其推送消息，也可以有任意多个消费者。

#### Partition
每个 topic 可以划分多个分区，即 Kafka 存储消息的物理单元。Partition 是以文件的形式存储在 kafka-logs 目录下，命名规则是 `<topic_name>-<partition_id>`。

#### Group
Kafka 中的逻辑分组，同一个组内的一条消息只被一个组员消费，并且共同维护 consumer offset。不同组内的消费者可以同时消费同一条消息，实现 pub/sub 模式。
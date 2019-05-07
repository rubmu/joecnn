---
title: Hadoop quickstart
date: 2016/12/16
tags: [hadoop, 分布式]
---

### 1. Hadoop 是什么？
Hadoop是一个分布式系统架构，由Apache基金会基于Java开发。用户可以在不了解分布式底层细节的情况下，开发分布式程序，充分利用集群的威力高速运算和存储。 

### 2. Hadoop 能做什么？
Hadoop 可以解决海量数据的存储、海量的数据分析。例如：Yahoo! 的垃圾邮件识别和过滤、用户特征建模；Amazon.com（亚马逊）的协同过滤推荐系统；Facebook的Web日志分析；Twitter、LinkedIn的人脉寻找系统；淘宝商品推荐系统、淘宝搜索中的自定义筛选功能......这些应用都使用到Hadoop及其相关技术。

### 3. Hadoop 的构成

#### 3.1 HDFS 分布式文件系统

HDFS有着高容错性的特点，并且设计用来部署在低廉的硬件上。而且它提供高传输率来访问应用程序的数据，适合那些有着超大数据集的应用程序。  
HDFS是一个 `master/slave` 的结构，在 `master` 上只运行一个 `NameNode`，而在每一个 `slave` 上运行一个 `DataNode`。 
##### NameNode负责: 
- 接受用户请求. 
- 维护文件系统目录结构. 
- 管理文件与Block间关系，Block与DataNode之间关系.(默认Block块大小为64M ) 

##### DataNode负责: 
- 存储文件. 
- 文件被分成Block存储在磁盘上. 
- 为保证数据安全，文件会有多个副本. 

#### 3.2 MapReduce 并行计算框架 
MapReduce 是 Google 的一项重要技术, 它是一个编程模型，用于进行大数据计算，通常采用的处理手法是并行计算。 
MapReduce 是一个 `master/slave` 的结构，在 `master`上只运行一个 `JobTracker` ，而在每一个`slave`上运行一个 `TaskTracker`。

##### JobTracker 负责: 
- 接收客户提交的计算. 
- 把计算分配给 TaskTracker 执行. 
- 监控 TaskTracker 的执行情况. 

##### TaskTracker 负责: 
- 执行 JobTracker 分配的任务. 

### 4. Hadoop 的特点
1. 扩容能力：通过增加节点，扩容方便可靠. 
2. 成本低：可以通过普通机器组成服务器集群来处理数据. 
3. 高效率：通过分发数据，可以在数据所在节点上并行处理数据. 
4. 可靠性：可以自动维护数据的多个副本，并在任务失败后自动重新部署计算任务 

### 5. 安装 Hadoop
#### 环境准备
- 安装 Jdk
- 下载 [Apache Hadoop](https://hadoop.apache.org/releases.html) 

#### 解压并设置环境变量
```bash
$ tar -zxvf hadoop-1.1.2.tar.gz
$ export HADOOP_HOME=/usr/local/hadoop/hadoop-1.1.2 
$ export PATH=.:$HADOOP_HOME/bin/:$JAVA_HOME/bin/:$PATH 
$ source /etc/profile
```

#### 配置
1. 修改 `$hadoop_home/conf/hadoop-env.sh` JAVA_HOME 配置 
2. 修改 `$hadoop_home/conf/core-size.xml` 

```xml
<configuration> 
    <property> 
            <name>fs.default.name</name> 
            <value>hdfs://joecnn:9000</value> 
            <description>your hostname</description> 
    </property> 
    <property> 
            <name>hadoop.tmp.dir</name> 
            <value>/usr/local/hadoop/hadoop-1.1.2/tmp</value> 
            <description>your hadoop home</description> 
    </property> 
</configuration> 
```

3. 修改 `$hadoop_home/conf/hdfs-site.xml`

```xml
<configuration> 
    <property> 
            <name>dfs.replication</name> 
            <value>1</value> 
    </property> 
    <property> 
            <name>dfs.permissions</name> 
            <value>false</value> 
    </property> 
</configuration> 
``` 

4. 修改 `修改 $hadoop_home/conf/mapred-site.xml`

```xml
<configuration> 
    <property> 
            <name>mapred.job.tracker</name> 
            <value>chenquan:9001</value> 
            <description>change your hostname</description> 
    </property> 
</configuration> 
```
#### 启动 Hadoop 

1. 格式化 HDFS 文件系统  `hadoop namenode -format` 
2. 启动 `start-all.sh`
    * **start-all.sh** 启动所有的Hadoop守护。包括namenode, datanode, jobtracker, tasktrack 
    * **stop-all.sh** 停止所有的Hadoop 
    * **start-mapred.sh** 启动Map/Reduce守护。包括Jobtracker和Tasktrack 
    * **stop-mapred.sh** 停止Map/Reduce守护 
    * **start-dfs.sh** 启动Hadoop DFS守护.Namenode和Datanode 
    * **stop-dfs.sh** 停止DFS守护 
3. 验证: jps 5个进程，namenode，datanode，jobtracker，tasktracker，secondarynamenode  
   **hadoop:50070/**  访问 hdfs webserver 页面   
   **hadoop:50030/**  访问 mapreduce webserver 页面  

#### 去除启动警告
1. 查看 start-all.sh -> hadoop-config.sh 
2. 修改 `/etc/profile` 增加 `export HADOOP_HOME_WARN_SUPPRESS=1` 
3. `source /etc/profile` 刷新环境变量 
4. 验证： start-all.sh  /  stop-all.sh 
---
title: Zookeeper quickstart
date: 2018/12/16
tags: [分布式]
---

### 1. zookeeper 是什么？
> ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services. 

zookeeper 是Apache基金会的一个项目，它为大型分布式计算提供开源的分布式配置服务、同步服务和命名注册，是分布式协调服务，目标是解决分布式数据的一致性问题。


### 2. zookeeper 能做什么？
数据的发布/订阅(配置中心 disconf)、 负载均衡(dubbo)、唯一ID生成器、统一命名服务、master选举(kafka, hadoop, hbase)、分布式队列、分布式锁……

### 3. zookeeper 的特性
> zookeeper 用来做的任务都是由他的特性决定的，理解了他的特性就可以根据实际的场景选择具体的方案。

- 顺序一致性：从客户端发起的事务请求，严格按照顺序被应用到zookeeper中
- 原子性：所有事务请求在集群上应用情况是一致的，要么都成功，要么都失败。
- 可靠性：一旦服务器应用了某个事务数据，那么这个数据一定是同步并且保留下来的。
- 实时性：一旦某个事务被成功应用，客户端能立即读取到最新数据状态(zookeeper 仅仅保证一定时间内的近实时性)。

### 4. 安装 zookeeper
#### 4.1 单机环境安装
1. 下载安装包  
    [zookeeper-3.4.12.tar.gz](http://apache.fayea.com/zookeeper/stable/)
2. 解压安装包  
`tar -zxvf zookeeper-3.4.12.tar.gz -C /usr/local/`
3. 创建配置文件  
`cd conf/ && cp zoo_sample.cfg zoo.cfg`
4. 启动 zookeeper  
`cd bin/ && sh zkServer.sh start`
5. 使用客户端连接到 zookeeper 进行操作  
`sh zkCli.sh -server ip:port`

单机环境下启动 zookeeper 状态为 standalone 仅用于测试或学习。

#### 4.2 集群环境安装
> 集群环境下至少需要2N+1台服务器进行安装

1. 修改配置文件，添加主机列表  

```bash
……
# ip:port:port 
# ip地址 : 用于节点通信的端口 : 用于选举的端口 [:observer]
server.1 = 192.168.56.110:2888:3888
server.2 = 192.168.56.120:2888:3888
server.3 = 192.168.56.130:2888:3888
```

2. 添加 myid 文件  
在配置文件的 `dataDir` 目录下创建 myid 文件，文件就一行数据内容是每台机器对应的server.ID的数字。

3. 启动 zookeeper

#### 4.3 zookeeper 节点类型
1. leader : 领导节点，主要接收、分发请求，发起事务处理投票，并给 follower 节点同步数据。
2. follower : 随从节点，处理读请求，转发事务请求，并保持和 leader 节点同步和事务的投票选举。
3. observer : 监控节点，处理读请求，保持和 leader 节点同步但不进行投票选举。

### 5. zookeeper 客户端命令使用

```bash
# 列出路径下的所有节点
ls / 

# 创建节点
# -e 临时节点  -s 有序节点  acl 权限
create [-e] [-s] path data acl

# 获取节点信息
get path

# 更新节点内容
# version 类似数据库乐观锁的实现
set path data [version]

# 删除节点
delete path [version]
```
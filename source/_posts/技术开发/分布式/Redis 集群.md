---
title: Redis 集群
date: 2018/12/31
tags: [redis, 分布式]
---

Redis 是一个开源的 key-value 存储系统，由于出众的性能，大部分互联网企业都用来做服务器端缓存。Redis 在3.0版本前只支持单实例模式，虽然支持主从模式、哨兵模式部署来解决单点故障，但是现在互联网企业动辄大几百G的数据，可完全是没法满足业务的需求，所以，Redis 在 3.0 版本以后就推出了集群模式。

### 一、Master-slave 模式
实现主从复制模式，只需要在`slave`服务器上修改配置文件：
```bash
## 配置master服务器
slaveof <masterip> <masterport>
```
`slave`服务器只接收读请求，可以用来做读写分离，通过`sync`命令向`master`同步数据。  

配置完成后启动，可以通过命令查看状态：
```bash
## 输出当前服务信息
info replication
```
#### 数据同步
从节点定时(`1S`)从主节点同步数据，通过发送`sync`命令, 通过命令可以监控同步信息：  

```bash
replconf listening –port 6379
```

可以使用命令手动同步数据：`sync`.  

#### 数据同步方式
1. 基于RDB文件的复制(第一次连接或重启的时候)
2. 无硬盘复制 `repl-diskless-sync yes`
3. 增量复制 PSYNC master run id. Offset


#### 实现原理
1. slave 第一次或者重连到 master 上以后，会向 master 发送一个`SYNC`的命令
2. master 收到`SYNC`的时候，会做两件事：
    1. 执行`bgsave`（rdb的快照文件）
    2. master 把新收到的修改命令存入到缓冲区, 通过`RESP`协议发送给 slave (aof方式)
3. slave 收到文件后会先清空数据，再从rdb快照文件恢复

缺点：主从复制的模式无法对 master 进行动态选举。

### 二、哨兵模式
Redis 提供了哨兵工具，监控 master 和 salve 是否正常运行,如果 master 出现故障，那么会把其中一台 salve 数据升级为 master。  

哨兵机也可做集群防止单点问题，启动哨兵的步骤：
1. 拷贝哨兵配置文件 `sentinel.conf`
2. 修改配置文件

```shell
## 配置主节点名称、ip、port、master需要的投票数
sentinel monitor <master-name> <ip> <redis-port> <quorum>
```

3. 启动哨兵

```bash
bin/redis-sentinel sentinel.conf
```

### 三、Redis 集群
Redis 集群采用了P2P的模式，完全去中心化。Redis 把所有的 Key 分成了 16384 个 slot，每个 Redis 实例负责其中一部分 slot 。集群中的所有信息（节点、端口、slot等），都通过节点之间定期的数据交换而更新。
Redis 客户端可以在任意一个 Redis 实例发出请求，如果所需数据不在该实例中，通过重定向命令引导客户端访问所需的实例。  

#### 修改配置文件，采用集群方式：

```bash
## 节点标识pid 6379和port要对应
pidfile /var/run/redis_6379.pid

## 启动集群模式
cluster-enabled yes

## 集群配置文件
cluster-config-file nodes-6379.conf

## 集群节点连接超时
cluster-node-timeout 15000
```

启动每个redis服务，尝试使用命令，发现集群还无法使用，提示信息：

```bash
(error) CLUSTERDOWN Hash slot not served
```

Redis 节点虽然启动了，但是它们之间还无法相互发现，也无法分配slot，需要一个中间人调度。

#### 安装集群所需软件
由于 Redis 集群需要使用 ruby 命令，所以我们需要安装 ruby 和相关接口。

```bash
yum install ruby
yum install rubygems
gem install redis
```

调用 ruby 命令创建集群：

```bash
bin/redis-trib.rb create --replicas 1 192.168.56.110:6379 192.168.56.110:6380 192.168.56.120:6379 192.168.56.120:6380 192.168.56.130:6379 192.168.56.130:6380
```

`--replicas` 1 表示主从复制比例为 1:1, 所以这里需要有6个 Redis 服务节点，组成3主3从的集群。

#### 验证一下
依然是通过客户端命令连接上，通过集群命令看一下状态和节点信息等。

```bash
bin/redis-cli -c -h 192.168.56.110 -p 6379
cluster info
cluster nodes
```

### 四、其它集群方案
#### 一致性哈希
通过程序对Key进行一致性哈希，再路由到不同的 Redis 服务节点上。

#### redis-shardding
Jedis封装的基于一致性哈希算法的解决方案，通过`ShareddingJedis`客户端连接到服务节点，也是在应用层面实现的。

#### codis
基于 redis-2.8 开发的 codis-server，支持了数据的分片存储，通过codis-proxy实现请求路由。

#### twemproxy
twitter 提供的解决方案，也是通过增加 proxy 代理层，做数据分片存储和请求路由。
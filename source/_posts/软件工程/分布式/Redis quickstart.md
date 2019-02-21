---
title: Redis quickstart
date: 2018/12/29
tags: [redis, 分布式]
---

### 1. Redis 是什么？
> Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker.  

Redis 是一个开源的分布式键值对(key/value)存储数据库，常用于做为数据缓存、单点登录、网站访问排名、秒杀抢购、应用模块开发等。

### 2. 安装 Redis
1. 下载 [redis-4.0.12.tar.gz](https://redis.io/download) 安装包
2. 解压并进行编译测试  
`make && make test`
3. 安装到指定目录  
`cd src && make install PREFIX=/usr/local/redis-4.0.12`

`make install`安装完成后会在指定目录下生成`bin/`文件夹，里面存放着使用 redis 的工具。

### 3. 启动和停止 Redis
1. 首先到源目录下拷贝配置文件到安装目录  
`cp ~/redis-4.0.12/redis.conf /usr/local/redis-4.0.12/`
2. 修改配置信息
    - **bind**： IP绑定修改为本机IP 
    - **daemonize**： 改为后台进程运行
3. 启动 redis 服务  
`./redis-server ../redis.conf`
4. 使用可以端连接到 redis  
`./redis-cli -h 192.168.56.110 -p 6379`
5. 停止 redis 服务  
`./redis-cli shutdown`

### 4. 常用命令
#### Key 相关
keys 检索满足条件的键值对，支持正则表达式的通配符匹配，但要注意大数据量下的检索影响 redis 服务性能。
```bash
## 获得一个符合匹配规则的键名列表，支持通配符
keys [? / * [] ]
## 判断key是否存在
exists key
## 获取key结构类型
type key
```

#### 字符类型
最基本的数据结构，可以存储任意的字符类型的数据，单条数据最大可支持512M。
```bash
## 设置key/value键值, 过期时间 EX=10s, PX=100ms
set key value EX 10 PX 100 
## 批量设置多个key/value
mset key value key1 value1
## 只在key不存在时进行设置
setnx key value
## 获取key值
get key
## 获取多个key的值
mget key key1
## 对数字类型原子递增 1
incr num
## 原子递减 1
decr num
## 原子递增 10
incrby num 10
## 原子递减 10
decrby num 10
## 向指定的key追加字符串
append key value
## 获取key对应的value的长度
strlen key 
```

#### 列表类型
list 可以存储一个有序的字符列表，内部是使用双向链表实现的，可以用来实现分布式消息队列。命令以 `l-`开头。
```bash
## 左右两端添加数据
(r|l)push key value value1 value2
## 左右两端弹出数据，输出弹出的value
(r|l)pop key
## 获取列表长度
llen key
## 获取列表元素，start 开始索引， stop 结束索引(-1表示最右边)
lrange key start stop
## 从列表中移除N个value值的元素
lrem key count value
## 更新idx位置处的元素为value
lset key idx value
```

#### 散列类型
散列类型是 HashMap 数据结构，存储多个键值对，适合存储多属性对象，但不支持数据类型的嵌套。命令以 `h-` 开头。
```bash
## 设置对象field属性值
hset key field value
hmset key field value field2 value2

## 获取field属性值
hget key field
hmget key field field2

## 获取所有field值
hgetall key
## 判断field属性是否存在
hexists key field
```

#### 集合类型
集合类型 set 与列表不同，不能存在重复的数据，而且是无序存储。命令以 `s-` 开头。
```bash
## 增加元素，如果value存在则忽略，返回成功加入集合的数量
sadd key value value2
## 删除元素
srem key value
## 获取所有元素
smembers key
## 获取集合长度
scard key
## 集合key1与key2的差集，列出key1中存在而在key2中不存在的元素
sdiff key1 key2
## 将差集存储在des中
sdiffstore des key key2
## 集合key1与key2的交集
sinter key1 key2
## 集合key1与key2的并集
sunion key1 key2
```

#### 有序集合类型
有序集合是在集合set的基础上，增加了排序功能，增加了`score`表示元素的优先级。命令以 `z-` 开头。
```bash
## 增加元素，优先级 score
zadd key score value
## 列出集合，并且输出优先级
zrange key start stop withscores
```

#### 事务处理
Redis 中支持事务，将多个命令加入到`QUEUED`中到最后一起提交或回滚，但有特例的情况无法回滚(命令运行时出错)。
```bash
## 开启事务
multi
...
## 提交事务
exec
```

#### 过期时间
Redis 中可以对每个键值设置对应的过期时间。
```bash
## 设置key过期时间为seconds秒
expire key seconds
## 获得key的过期时间,-1 未设置  -2已过期 
ttl key
```

#### 发布订阅(pub/sub)
Redis 中支持消息的发布订阅模式，类型消息中间件的功能，但性能不高一般不推荐使用。
```bash
## 发布消息到channel频道
publish channel msg
## 订阅channel频道的消息
subscribe channel
```
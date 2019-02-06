---
title: Redis 持久化机制
date: 2018/12/31
tags: [redis, 分布式]
---

Redis 是一款基于内存的键值对数据库，使用单线程IO多路复用技术达到高性能。但某些场景下对数据的持久性是有要求的，所以Rdis提供了两种持久化策略，防止宕机数据丢失。
### Redis 持久化机制
对于persistence持久化存储，Redis提供了两种方式：
- RDB(Redis database) 存储快照文件snapshot
- AOF(Append-only file) 存储redo文件

#### 1. RDB
RBD 是在规定时间点将内存数据通过快照方式写入临时文件，再替换上次的持久化文件，达到数据持久化的方式。
##### 优点：
- 使用fork出的子进程处理，不影响主进程
- 输出snapshot快速且体积小
##### 缺点：
- RDB间隔一段时间执行，如果在执行期间发生故障，将导致数据丢失
- 当内存数据较大时，fork操作将花费较长时间，导致Redis无法提供服务

##### RDB 会在指定的情况下触发快照
1. 配置的快照间隔时间
```shell
## RDB默认是开启的
## RDB文件名
dbfilename dump.db

## RDB文件存储目录
dir ./

## RDB快照触发时机, save <间隔时间> <操作数>
## 全部删除即关闭RDB
save 900 1

## RDB快照异常时, 是否阻塞客户端"变更操作"
stop-writes-on-bgsave-error yes

## RDB文件是否进行压缩
rdbcompression yes
```
2. 执行 `save` 或 `bgsave` 时
```shell
## 同步进行
./redis-cli -h ip -p port save

## 异步进行
./redis-cli -h ip -p port bgsave
```
3. 执行 `flushall` 时
4. 执行`master/slave`全量复制时

##### 快照的实现原理
1. Redis使用`fork`复制一份当前进程的副本(子进程)。
2. 父进程继续接收处理客户端请求，子进程开始将内存的数据写入到临时文件。
3. 子进程用临时文件替换原文件。
> 注意：redis在进行快照的过程中不会修改RDB文件，只有快照结束后才会将旧的文件替换成新的，也就是说任何时候RDB文件都是完整的。 这就使得我们可以通过定时备份RDB文件来实现redis数据库的备份， RDB文件是经过压缩的二进制文件，占用的空间会小于内存中的数据，更加利于传输。

#### 2. AOF
AOF 将"操作+数据"以格式化(RESP)的方式最佳到操作日志文件尾部，在`append`操作成功后才进行实际数据的变更。当Server需要恢复时，可以直接`replay`日志文件，即可还原所有操作过程。
##### 优点：
- 可以保持更高的数据完整性，如果设置间隔`1S`则最多丢失`1S`的数据。
- 可以手动删除其中某些命令，方便维护
##### 缺点：
- 额外的IO操作，略微影响Redis性能
- AOF文件比RDB文件大
- 恢复速度慢，要逐条重放命令

##### AOF 默认关闭，开启时需要修改配置文件；
```shell
## 开启aof
appendonly yes

## 指定aof文件名称
appendfilename appendonly.aof
```

##### AOF 文件重写
在开启AOF时，命令会一直append到aof文件中，使得aof文件体积越来越大，Redis支持对aof文件进行重写(`rewrite`)，合并相同Key的操作，保留最小命令集合。
```shell
##aof文件rewrite触发的最小文件尺寸(mb,gb),只有大于此aof文件大于此尺寸是才会触发rewrite，默认“64mb”，建议“512mb”  
auto-aof-rewrite-min-size 64mb  
  
##相对于“上一次”rewrite，本次rewrite触发时aof文件应该增长的百分比。  
##每一次rewrite之后，redis都会记录下此时“新aof”文件的大小(例如A)，那么当aof文件增长到A*(1 + p)之后  
##触发下一次rewrite，每一次aof记录的添加，都会检测当前aof文件的尺寸。  
auto-aof-rewrite-percentage 100
```
 整个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。AOF 文件有序地保存了对数据库执行的所有写入操作， 这些写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件进行分析（parse）也很轻松。
 
 ##### 同步磁盘数据
 Redis每次更改数据的时候都会将命令记录到aof文件，但是实际上由于`kernel`的缓存机制，数据并没有实时写入到硬盘，而是进入硬盘缓存,再通过硬盘缓存机制去刷新到保存到文件。
 ```shell
 ## 每次执行写入都会进行同步  ， 这个是最安全但是是效率比较低的方式
 # appendfsync always  
 
 ## 每一秒执行
appendfsync everysec 

## 不主动进行同步操作，由操作系统去执行，这个是最快但是最不安全的方式
# appendfsync no  
 ```
 
 ##### aof文件损坏后怎么恢复
 Redis 在执行命令中途宕机，导致命令只存储到aof一半，这个时候通过aof文件无法恢复，需要先对文件进行修复。  
 aof文件修复可以使用Redis提供的工具：
 ```shell
 ## bin/
 redis-check-aof --fix
 ```
 
 #### 3. RDB和AOF如何选择
 一般来说,如果对数据的安全性要求非常高的话，应该同时使用两种持久化功能。如果可以承受数分钟以内的数据丢失，那么可以只使用 RDB 持久化。有很多用户都只使用 AOF 持久化， 但并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快 。  
两种持久化策略可以同时使用，也可以使用其中一种。如果同时使用的话， 那么Redis重启时，会优先使用AOF文件来还原数据。

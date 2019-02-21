---
title: ActiveMQ configuration
date: 2018/12/27
tags: [MQ, 分布式]
---

ActiveMQ使用过程中涉及到的一些配置信息。
### 1. 传输协议配置
支持 TCP, UDP, NIO, SSL, HTTP(S), VM 等协议.
```xml
<transportConnectors>
    <!-- DOS protection, limit concurrent connections to 1000 and frame size to 100MB -->
    <transportConnector name="nio" uri="nio://0.0.0.0:61618?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600" />
</transportConnectors>
```

### 2. 持久化策略配置
支持的持久化方式有 kahaDB, AMQ, JDBC, Memory.
#### 使用 AMQ 文件存储
```xml
<persistenceAdapter>
    <amqPersistenceAdapter directory="${activemq.data}/amq" maxFileLength="32m"/> 
</persistenceAdapter>
```

#### 使用 JDBC 存储
使用jdbc数据库存储的方式，需要连接到对应的数据源，要在`../lib`下加入连接包。
```xml
<!-- jdbc存储策略 -->
<persistenceAdapter>
    <jdbcPersistenceAdapter dataSource="#mysqlDataSource" createTablesOnStartup="true" />
</persistenceAdapter>

<!-- 数据源配置 -->
<bean id="mysqlDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://192.168.56.104:3306/practice_dev" />
        <property name="username" value="root" />
        <property name="password" value="123456" />
    </bean>
```

### 3. 集群配置
配置多态activemq服务进行联通，提高消息服务的性能。
```xml
<!-- broker 添加此配置 -->
<networkConnectors>
    <networkConnector uri="static://(tcp://192.168.56.110:61616,tcp://192.168.56.120:61616)" />
</networkConnectors>
```
配置集群后，当使用某一个节点消费时，会将另外节点的数据转移到此节点上，导致在原节点无法再消费。此时需要配置==消息回流==
```xml
<!-- 消息回流支持，添加在 policyEntries下-->
<policyEntry queue=">" enableAudit="false">
    <networkBridgeFilterFactory>
        <conditionalNetworkBridgeFilterFactory replayWhenNoConsumers="true" />
    </networkBridgeFilterFactory>
</policyEntry>
```

### 4. zk+activemq高可用配置
使用ZK进行master/slaver选举管理，在ZK中维护了临时有序节点，最先启动的先获得master。
- directory： levelDB数据文件存储的位置
- replicas：计算公式（replicas/2）+1  ， 当replicas的值为2的时候， 最终的结果是2. 表示集群中至少2台是启动时才能提供服务
- bind:  用来负责slave和master的数据同步的端口和ip
- zkAddress： 表示zk的服务端地址
- hostname：本机ip
```xml
<persistenceAdapter>
    <replicatedLevelDB directory="${activemq.data}/levelDB" 
                replicas="2" bind="tcp://0.0.0.0:61615"
                zkAddress="192.168.56.110:2181"
                hostname="192.168.56.110"
                zkPath="/activemq/leveldb" />
</persistenceAdapter>
```

### 5. hawtio 监控服务
1. 拷贝hawtio.war包到webapps目录下.
2. 添加jetty容器映射`rewriteHandler`:
```xml
<bean class="org.eclipse.jetty.webapp.WebAppContext">        
        <property name="contextPath" value="/hawtio" />        
        <property name="war" value="${activemq.home}/webapps/hawtio.war" />        
        <property name="logUrlOnStart" value="true" />  
</bean>
```
3. 修改 bin/env 文件添加参数   
> 需要注意的是-Dhawtio的三个设定必须放在ACTIVEMQ_OPTS设置的最前面(在内存参数设置之后),否则会出现验证无法通过的错误(另外,ACTIVEMQ_OPTS的设置语句不要回车换行)

```bash
-Dhawtio.realm=activemq -Dhawtio.role=admins -Dhawtio.rolePrincipalClasses=org.apache.activemq.jaas.GroupPrincipal
```
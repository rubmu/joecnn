---
title: Jenkins 开机启动
date: 2018/10/18 20:30:00
tags: [jenkins]
---

使用 jenkins 最简单的方式即使用 war 包进行启动，war 包中带了 jetty 服务，可以直接 `java -jar jenkins.war` 启动使用。
但每次都使用命令相当繁琐，本编即介绍如何将此步骤设置于开机启动。

## 1. 环境准备

- Linux CentOs 7.3      [下载](https://www.centos.org/download/)
- Jre 1.8.0[下载](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)
- Jenkins 2.138.2[下载](https://jenkins.io/download/)

## 2. 编写 linux 开机自运行脚本 jenkins.sh

> 将该脚本加入chkconfig启动项目中，开机时运行。  
  JENKINS_ROOT: jenkins软件目录  
  JENKINS_HOME: jenkins主目录

```bash
#!/bin/sh
#chkconfig: 2345 80 90
#description:开机启动jenkins服务

JENKINS_ROOT=/usr/local/jenkins
JENKINSFILENAME=jenkins.war

#停止方法
stop(){
    echo "Stoping $JENKINSFILENAME "
        ps -ef|grep $JENKINSFILENAME |awk '{print $2}'|while read pid
        do
           kill -9 $pid
           echo " $pid kill"
        done
}

case "$1" in
start)
    echo "Starting $JENKINSFILENAME "
        nohup $JENKINS_ROOT/start_jenkins.sh >> $JENKINS_ROOT/jenkins.log 2>&1 &
  ;;
stop)
  stop
  ;;
restart)
  stop
  start
  ;;
status)
  ps -ef|grep $JENKINSFILENAME
  ;;
*)
  printf 'Usage: %s {start|stop|restart|status}\n' "$prog"
  exit 1
  ;;
esac

```

## 3. 编写启动 war 包命令 start_jenkins.sh

> 启动war包的命令，由于在启动时需要使用java命令，所以在脚本中加入了java的bin路径。

```bash
#!/bin/bash
JENKINS_ROOT=/usr/local/jenkins
export JENKINS_HOME=$JENKINS_ROOT/home
JAVA_HOME=/usr/local/java/jre1.8.0_151 PATH=$PATH:$JAVA_HOME/bin
java -jar $JENKINS_ROOT/jenkins.war --httpPort=8080
```

## 4. 加入 chkconifg 启动项目

```bash
# 赋予执行权限
chmod +x /usr/local/jenkins/jenkins.sh

# 创建软链接到 init.d 目录
ln -s /usr/local/jenkins/jenkins.sh /etc/rc.d/init.d/jenkins

# 添加到 chkconfig
chkconfig --add jenkins
chkconfig --level 345 jenkins on
```

## 5. 启动jenkins服务

```bash
/etc/rc.d/init.d/jenkins start
```

到此已经可以在启动服务器时自动运行jenkins了，端口占用8080.
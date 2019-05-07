---
title: MySQL quickstart
date: 2019/01/07 17:00:00
tags: [mysql]
---

### 一、安装 MySQL-5.7
1. 下载 MySQL-5.7 的 repo 源：  
  `wget http://repo.mysql.com/mysql57-community-release-el7.rpm`
2. 安装源：  
  `rpm -ivh mysql57-community-release-el7.rp`
3. 安装数据库：  
  `yum -install mysql-server`
4. 启动数据库：  
  `systemctl start mysqld`

#### 安装后文件对应的目录
- mysql 的数据和二进制文件：/var/lib/mysql
- mysql 的配置文件：/etc/my.cnf
- mysql 的日志文件：/var/log/mysql.log

### 二、登录到 MySQL
MySQL-5.7 版本对新安装的 root 账号有一个随机密码，可以通过 `grep "password" /var/log/mysqld.log` 获得，root@localhost 此处为随机密码
1. 运行 `mysql -uroot -p`
2. 输入初始的随机密码进入

#### 设置密码
1. 默认的随机密码无法对数据库进行操作，需要进行设置，而5.7版本用了`validate_password`密码加强插件，简单的密码无法通过验证
2. 执行以下两条命令，让密码可以随意设置
```
set global validate_password_length=1;
set global validate_password_policy=0;
```
3. 这样可以设置简单的密码，但是长度要求大于4位  
`set password=password("root")`

#### 连接授权
默认情况下其它服务器的客户端不能直接访问mysql服务器，需要对IP授权。  
`GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;`

#### 验证
查询所有的数据库 `show databases;`
---
title: Linux PostgreSQL 数据库安装
date: 2019/2/23 19:51
tags: [postgres]
---

**PostgreSQL** 是加州大学伯克利分校计算机系开发的 **对象-关系型数据库(ORDBMS)** 管理系统，在 BSD许可证 下开源发行。PostgreSQL 的稳定性极强，支持丰富的几何类型数据。它可以存储 array 和 json, 可以在 array 和 json 上建索引, 甚至还能用表达式索引。

PostgreSQL 提供了很多种安装方式，本编文章主要是在 Linux 环境下以源码的方式进行安装，这样虽然在安装上步骤多了许多，但让你可以控制安装细节。



## 下载 PostgreSQL

首先你需要到 [postgresql.org](https://www.postgresql.org/) 上下载你需要的版本源码，也可以通过以下命令直接下载。

`$ sudo wget https://ftp.postgresql.org/pub/source/v11.2/postgresql-11.2.tar.gz`

解压 `tar.gz` 文件到 `/usr/local/etc` 目录。



## 安装 PostgreSQL

接下来你需要从源码进行安装，这个过程分为三个步骤。首先你需要准备你的 Linux 编译环境，其次对源码进行编译生成安装文件，最后进行 PostgreSQL 安装。

#### 准备编译环境

进入已解压的 PostgreSQL 文件目录，执行 `$ ./configure` 命令。

#### 编译安装

执行命令 `$ make && sudo make install`

#### 安装完成

PostgreSQL 会安装在 `/usr/local/pgsql` 目录下，在 `bin` 目录下提供了一些 PostgreSQL 的命令行工具，提供给用户使用。



## 创建 Linux 用户

出于对数据库安全性和完整性的控制，为 PostgreSQL 应用创建一个用户进行管理。

```bash
$ groupadd postgres
$ useradd -g postgres postgres
$ passwd postgres
```



## 创建数据存储目录

在这一步骤，你将创建一个用于存储 PostgreSQL 数据的目录，并将该目录授权给上一步创建的`postgres` 用户。

进入 `/usr/local/pgsql` 目录，这个是安装 PostgreSQL 的目录，在此目录下创建 `data` 目录。

```bash
$ cd /usr/local/pgsql
$ sudo mkdir data
$ sudo chown postgres:postgres /usr/local/pgsql/data
```



## 设置环境变量

在这一步骤中，在 `/etc/profile` 下添加配置，方便用户在 `bash` 中直接使用 PostgreSQL 提供的工具，并且设置默认的数据文件存储位置 `PGDATA`。

```bash
PGDATA=/usr/local/pgsql/data
PATH=$PATH:/usr/local/pgsql/bin
export PGDATA PATH
```

最后执行 `source /etc/profile` 让环境配置生效。



## 初始化数据库

切换到 `postgres` 用户，对新安装的 PostgreSQL 进行初始化，并通过 `-D` 指定数据存储目录，如果不指定则默认使用环境变量中 `PGDATA` 指向的目录。

`$ pg_ctl initdb -D /usr/local/pgsql/data` 

接下来可以启动数据服务，可以指定日志输出文件。

`$ pg_ctl -l ~/logfile start`

到此可以看到 PostgreSQL 服务已经启动，可以通过 `psql` 命令连接到服务，先给默认的用户 `postgres` 设置登录密码。

`postgres=# ALTER USER postgres PASSWORD 'postgres';`



## 开启支持远程连接

默认安装的 PostgreSQL 只支持本地连接，但我们在使用时一般都是通过客户端进行远程连接，所以这一步骤主要是修改配置文件，让PostgreSQL 数据服务可以接受远程连接。

进入 `/usr/local/pgsql/data` 目录下，修改配置文件 **postgresql.conf** 。

```properties
# 开放监听的地址
listen_addresses = '*'
```

修改配置文件 **pg_hba.conf**  添加允许连接的 IP 段。

```properties
# IPv4 local connections:
host    all             all              0.0.0.0/0                       md5
# IPv6 local connections:
host    all             all              ::/0                            md5
```

修改完这两个配置文件后，重启 pgsql 服务即可支持远程连接。



## 设置开机启动

PostgreSQL 数据服务一般要设置到服务器开机自动启动，接下来的步骤就是通过开机运行脚本进行设置。

首先切换到 `root` 用户。

`$ su root`

接下来拷贝一份开机脚本到 `/etc/init.d` 目录。

`$ sudo cp /usr/local/etc/postgresql-11.2/contrib/start-scripts/linux /etc/init.d/postgresql `

给脚本添加执行权限。

`$ sudo chmod +x /etc/init.d/postgresql`

添加到开机启动运行服务列表中。

`$ sudo chkconfig --add postgresql`

你可以通过 `reboot` 重启 Linux 进行验证是否已设置成功。



## 总结

本编文章记录了 PostgreSQL 数据的源码编译安装方式，同样也可以通过 `yum` 或 `rpm` 等在线安装的方式，也可以直接下载已编译好的包，解压配置环境变量即可。如果进行手动安装的方式，记得要配置环境变量和数据存储目录，以便 PostgreSQL 的使用。
---
title: Docker Mysql
date: 2018/03/07 21:30:00
tags: [docker]
---

### docker mysql
> docker 下启动 mysql 实例步骤

#### 1. 查询镜像 images

```bash
$ docker images
$ docker search mysql
$ docker pull mysql
```

#### 2. 创建数据进行映射

```bash
$ mkdir -p /var/docker_data/mysql/data
$ chmod 775 -R /var/docker_data/mysql
```

#### 3. 运行容器

```bash
$ docker run -p 3306:3306 --name mysql-dev -v /var/docker_data/mysql/data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=asdf123456 -e MYSQL_USER=root -d mysql
```

#### 4. 验证是否已启动

```bash
$ docker ps
```


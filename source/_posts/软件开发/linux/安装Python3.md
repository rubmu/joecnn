---
title: 安装Python3
date: 2016/01/08
tags: [linux]
---

### 1. 安装 Python 依赖包
```bash
$ yum groupinstall "Development tools"
$ yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

### 2. 下载 Python3.5 源码包并编译
```bash
$ wget https://www.python.org/ftp/python/3.5.0/Python-3.5.0.tgz
$ tar -zxvf Python-3.5.0.taz
$ cd Python-3.5.0
```

### 3. 编译安装
```bash
$ ./configure --prefix=/usr/local --enable-shared
$ make 
$ make install
$ ln -s /usr/local/bin/python3 /usr/bin/python3
```

### 4. 在运行 Python 前配置需要库
```bash
$ echo /usr/local/lib >> /etc/ld.so.conf.d/local.conf
$ ldconfig
```

### 5. 检测安装
```bash
$ python3 --version
```

### 6. 移除编译 Python 安装的库
```bash
$ yum groupremove "Development tools"
$ yum remove zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

### 7. 设置别名
```bash
$ alias py=python3
```
---
title: 安装gcc
date: 2016/01/06
tags: [linux]
---

### 一、安装内核支持gcc
```bash
$ yum -y install gcc+ gcc-c++
```

### 二、手动升级gcc

#### 1. 获取安装包
```bash
$ wget http://ftp.gnu.org/gnu/gcc/gcc-4.8.2/gcc-4.8.2.tar.bz2
$ tar -jxvf gcc-4.8.2.tar.bz2
```

#### 2. 下载编译需要的依赖项
```bash
$ cd gcc-4.8.2
$ ./contrib/down_prerequiesites
```

#### 3. 建立一个目录供编译输出的文件存放
```bash
$ mkdir gcc-build-4.8.2
```

#### 4. 生成 Makefile 文件
```bash
$ cd gcc-build-4.8.2
$ ../configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
```

#### 5. 编译, -j4选项是make对多核处理器的优化
```bash 
$ make -j4
$ yum -y install glibc-devel.i686 glibc-devel
```

#### 6. 安装
```bash 
$ make install
```

#### 7. 检查gcc版本
```bash
$ gcc --version
```
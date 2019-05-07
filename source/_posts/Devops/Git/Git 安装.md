---
title: Git 安装
date: 2017/12/26
tags: [git]
---

### 1. 从源码安装 
Git的工作需要调用 curl, zlib, openssl, expat, libconv 等库，所以需要先安装这些依赖工具。 
```bash
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel 
```
 
##### 之后从Git官方站点下载源码： 
    http://git-scm.com/download 
 
##### 进行编译安装： 
```bash
$ tar -zxf git-1.7.2.2.tar.gz 
$ cd git-1.7.2.2 
$ make prefix=/usr/local all 
$ sudo make prefix=/usr/local install 
```
 
### 2. 在 Linux 上安装 
如果要在 Linux 上安装预编译好的 Git 安装包，使用系统提供的包管理工具即可： 
```bash
$ yum install git-core 
$ apt-get install git-core 
```
 
### 3. 在 Mac 上安装 
在 Mac 上安装有两种方式，一种是下载 Git 安装工具，下载地址： 
    http://git-scm.com/download/mac 
 
另一种是通过 MacPorts 安装，用下面的命令安装： 
```bash
$ sudo port install git-core +svn +doc +bash_completion +gitweb 
```
 
### 4. 在 Windows 上安装 
在 Windows 上安装非常简单，只要下载 Git 安装工具，下载地址： 
    http://git-scm.com/download/win 
 
### 5. 初始配置 
一般在新的系统上，首先要配置 Git 的工作环境，Git 提供了 git config 的工具，用于配置和读取工作环境变量，这些变量可以存放在以下三个不同地方： 
- /etc/gitconfig 文件：对系统中所有用户都适用，若使用 git config 时用 --system 选项，读写的就是这个文件。 
- ~/.gitconfig 文件：用户目录下仅用于用户，若使用 git config 时用 --global 选项，读写的就是这个文件。 
- 当前项目的 git 目录中的配置文件：这里的配置仅仅对当前项目有效。每一个级别的配置都会覆盖上层的相同配置。 
 
##### 第一个要配置的是账户名称和邮箱地址： 
```bash
$ git config --global user.name "joecnn" 
$ git config --global user.email "starworking@126.com "
```
 
##### 配置文本编辑器： 
```bash
$ git config --global core.editor vim 
```
 
##### 配置差异分析工具： 
```bash
$ git config --global merge.tool vimdiff 
```
 
### 6. 查看配置信息 
```bash
$ git config --list 
```
 
### 7. 获取帮助     
```bash
$ git help  
$ man git- 
```
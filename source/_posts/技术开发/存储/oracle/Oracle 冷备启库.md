---
title: Oracle 冷备启库
date: 2013/12/26
tags: [oracle]
---

### 1、准备工作 

1. 查看数据版本是否跟库文件相同
2. 查看数据文件是否都齐全
3. 查看 init.ora 是否在（参数文件，也可以自己配置）

### 2、创建文件夹 

1. 在 oracle/admin 目录下创建 实例名文件夹。如：phoenix
2. 在此文件夹中创建 adump、dpdump、pfile ,将 init.ora 放置在 pfile 文件夹中
3. 在 fast_recovery_area 文件夹中创建实例名文件夹4. 在 oradata 文件夹中创建实例名文件夹

### 3、创建空实例

```bash
$ oradim -new -sid phoenix -startmode auto -pfile init.ora 
```

### 4、创建密码文件

```bash
$ orapwd file=D:\oracle\product\11.2.0\dbhome_1\database\pwdphoenix.ora password=oracle; 
```

### 5、进入创建好的实例
```bash
$ sqlplus /nolog  
$ conn zysys/zy2009phoenix as sysdba 
```

### 6、生成spfile文件 
```bash
$ create spfile from pfile='D:\Oracle\admin\phoenix\pfile\init.ora'; 
```

### 7、重建控制文件
```bash 
$ CREATE CONTROLFILE REUSE SET DATABASE "phoenix" RESETLOGS  NOARCHIVELOG 
    MAXLOGFILES 16 
    MAXLOGMEMBERS 3 
    MAXDATAFILES 100 
    MAXINSTANCES 8 
    MAXLOGHISTORY 292 
LOGFILE 
  GROUP 1 'E:\Work\Data\Orcl\REDO01.LOG'  SIZE 50M BLOCKSIZE 512, 
  GROUP 2 'E:\Work\Data\Orcl\REDO02.LOG'  SIZE 50M BLOCKSIZE 512, 
  GROUP 3 'E:\Work\Data\Orcl\REDO03.LOG'  SIZE 50M BLOCKSIZE 512 
-- STANDBY LOGFILE 
DATAFILE 
  'E:\Work\Data\Orcl\SYSTEM01.DBF', 
  'E:\Work\Data\Orcl\SYSAUX01.DBF', 
  'E:\Work\Data\Orcl\UNDOTBS01.DBF', 
  'E:\Work\Data\Orcl\USERS01.DBF', 
      ………………数据文件位置……………… 
  CHARACTER SET ZHS16GBK; 
```

### 8、启动数据库 
1. `alter database open;`   // alter database open resetlogs upgrade  小版本不同需要升级  
2. 若是选用noresetlogs选项, 若无法打开数据库, 则尝试执行  
`recover database;` 
3. 若是在线热备数据库文件,则还要把热备时刻的归档日志拷过来,恢复时用下列语法  
`recover database using backup controlfile until cancel;`  
写入日志文件  d：/xxx.log 
4. 创建临时表空间  
`alter tablespace temp add tempfile 'C:\Oracle\database\phoenix\temp01.ora' size 50M reuse autoextend on next 50M maxsize 500M;` 
5. 小版本不同需要执行升级脚本 
`oracle_home/rdbms/admin/catupgrd.sql；`
9. 重启数据库 

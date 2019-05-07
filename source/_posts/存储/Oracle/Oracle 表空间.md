---
title: Oracle 表空间
date: 2013/12/26
tags: [oracle]
---

1. 查看数据文件位置 

```sql
SELECT TABLESPACE_NAME,FILE_ID,BYTES,FILE_NAME FROM DBA_DATA_FILES; 
```

2. 表空间状态 

```sql
SELECT TABLESPACE_NAME,STATUS FROM DBA_TABLESPACES; 
```

3. 创建表空间 

```bash
CREATE TABLESPACE "ZYMBS"  
NOLOGGING --不创建重做日志文件 
DATAFILE 'E:\WORK\DATA\PHOENIX\ZYMBS01.ORA' SIZE 5M  
AUTOEXTEND ON  --自动增长 
NEXT 50M MAXSIZE 2048M   --自动增长最大到2G 
EXTENT MANAGEMENT LOCAL;  
```

4. 创建用户 

```
CREATE USER zymbs IDENTIFIED BY zy2009phoenix 
DEFAULT TABLESPACE ZYMBS 
TEMPORARY TABLESPACE TEMP; 
```

5. 分发权限 
```
GRANT --系统权限 
　　CREATE SESSION, CREATE ANY TABLE, CREATE ANY VIEW ,CREATE ANY INDEX, CREATE ANY PROCEDURE, 
　　ALTER ANY TABLE, ALTER ANY PROCEDURE, 
　　DROP ANY TABLE, DROP ANY VIEW, DROP ANY INDEX, DROP ANY PROCEDURE, 
　　SELECT ANY TABLE, INSERT ANY TABLE, UPDATE ANY TABLE, DELETE ANY TABLE 
　　TO ZYMBS; 
GRANT RESOURCE, CONNECT TO ZYMBS;  --角色权限 
```

6. 删除表空间 
```
  DROP TABLESPACE ZYMBS INCLUDING CONTENTS AND DATAFILES;  
```

7. 修改状态(ONLINE,OFFLINE,READONLY,READWRITE) 
```
 ALTER TABLESPACE ZYMBS OFFLINE; 
```

8. 修改文件大小,添加文件 
```
ALTER TABLESPACE DATAFILE 'E:\ZYMBS01.ORA' RESIZE 10M; 
ALTER TABLESPACE ZYMBS ADD DATAFILE 'E:\ZYMBS02.ORA' SIZE 5M; 
```

9. 修改表空间数据文件的自动扩展性  
```
ALTER DATABASE DATAFIELE 'E:\ZYMBS01.ORA' AUTOEXTEND ON NEXT 5M MAXSIZE 50M;  
```

10. 重新指定表空间路径   
```
alter tablespace SIMPLE RENAME DATAFILE 'E:\table2.dbf' to 'D:\table.dbf';  
```

11. 设置默认表空间  

   1. 查询(当前用户)默认表空间 `select default_tablespace from user_users;`  

   2. 修改默认表空间 `alter database default tablespace SIMPLE;`  
  
> 如果是oracle 9i本语句不支持，但能让用户指向某表空间，具体做法如下：  
      例： `alter user scott default tablespace users;` (scott为用户名, users为表空间)  
  
      有时会出现表空间有存在的情况，这时一般都是以下几个原因造成的：   
     1、写错表空间名，我想的话这种机率较小。  
     2、回想一下，你在创建表空间时是否给表空间表加了双引号如：  
          `CREATE TABLESPACE "Sample"`  
          ………………  
          如果是这样的话，你在修改默认表空间，写表空间名字的时候就要区分大小了，这个是非常重要的，并且还要加上双引号，  
         如：`alter user scott default tablespace "Sample";`(scott为用户名)。  

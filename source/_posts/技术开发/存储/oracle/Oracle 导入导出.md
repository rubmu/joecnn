---
title: Oracle 导入导出
date: 2013/12/26
tags: [oracle]
---

可将整个数据库，用户，表导出备份，在一些包含大字段的表无法用sql插入时，很好用哦。 
 
1. 导出exp 
```bash
exp zysys/zy2009phoenix@phoenix_my file=e:\phoenix.dmp full=y  --完全导出 
exp zysys/zy2009phoenix@phoenix_my file=e:\zymbs.dmp owner=(zysys,zymbs)  --按用户导出 
exp zysys/zy2009phoenix@phoenix_my file=e:\table.dmp tables=(ZYMBS.RHRSP_KPI_DICT,table2)  --导出表 
```
 
2. 导入imp 
```bash
imp zysys/zy2009phoenix@phoenix file=c:\orabackup\hkbfull.dmp log=c:\orabackup\hkbimp.log full=y --完全导入 
imp zysys/zy2009phoenix@phoenix_my fromuser=zymbs touser=zymbs FILE=E:\zymbs.dmp ignore=y; --按用户 
imp zysys/zy2009phoenix@phoenix  file=e:\table.dmp  tables=(RHRSP_KPI_DICT)  --导入表（不需要表空间名） 
```

3. 11g R2 导出空表
oracle 11g r2 新增了一个参数：`deferred_segment_creation`, 如果这个参数设置为 true，你新建了一个表 T1，并且没有向其中插入数据，那么这个表不会立即分配 extent，也就是不占数据空间，只有当你 insert 数据后才分配空间。这样可以节省少量的空间。
所以在导出时，空的表将不导出需要单独处理
- 1) 设置 `deferred_segment_creation` 参数为FALSE
`SQL> alter system set deferred_segment_creation=false;`

- 2) 注意并且要重启数据库，让参数生效, 但是这样操作后只会生效于新表
- 3) 使用 allocate extent 可以为数据库对象分配Extent
`SQL> ALLOCATE EXTENT { SIZE integer [K | M] | DATAFILE 'filename' | INSTANCE integer `

> 对现有表的处理脚本 createsql.sql：
```sql
set heading off; 
set echo off; 
set feedback off; 
set termout on; 
spool C:\allocate.sql; 
select 'alter table '||table_name||' allocate extent;' from user_tables where num_rows=0; 
spool off;
```


---
title: MySQL 表结构查询
date: 2017/04/06
tags: [mysql]
---

#### 获取所有表  
```sql
SELECT TABLE_NAME
  FROM INFORMATION_SCHEMA.TABLES
 WHERE TABLE_SCHEMA='{0}'
   AND (TABLE_TYPE='BASE TABLE' OR TABLE_TYPE='VIEW')
```
#### 获取表字段名  
```sql
select * from table_name where 1<>1
```

#### 获取表主键列  
```sql
SELECT k.TABLE_NAME,
		k.COLUMN_NAME,
		k.ORDINAL_POSITION,
		c.DATA_TYPE,
		c.COLUMN_TYPE
  FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS t
  JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE k
USING (constraint_name,table_schema,table_name)
  JOIN INFORMATION_SCHEMA.COLUMNS c
USING (column_name,table_schema,table_name)
 WHERE t.constraint_type='PRIMARY KEY'
  AND t.table_schema='data_base'
  AND t.table_name='table_name'
```
 
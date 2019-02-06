---
title: Mssql 表结构查询
date: 2017/04/06 17:00:00
tags: [mssql]
---

#### 获取所有表  

```sql
SELECT TABLE_NAME 
  FROM INFORMATION_SCHEMA.TABLES
 WHERE TABLE_SCHEMA='data_base' 
   AND (TABLE_TYPE='BASE TABLE' OR TABLE_TYPE='VIEW')
```

  #### 获取表字段名  
       
```sql
select * from table_name where 1<>1
```

  #### 获取表主键  
```sql
SELECT  col.name AS ColumnName ,  
        col.max_length AS DataLength ,  
        col.is_nullable AS IsNullable ,  
        t.name AS DataType ,
        ( SELECT TOP 1 ind.is_primary_key
            FROM sys.index_columns ic
        LEFT JOIN sys.indexes ind ON ic.object_id = ind.object_id AND ic.index_id = ind.index_id AND ind.name LIKE 'PK_%'
           WHERE ic.object_id = obj.object_id
             AND ic.column_id = col.column_id
        ) AS IsPrimaryKey
 FROM   sys.objects obj
INNER JOIN sys.columns col ON obj.object_id = col.object_id
LEFT JOIN sys.types t ON t.user_type_id = col.user_type_id
 WHERE  obj.name = 'table_name'
```

#### 获取表字段详细信息
```sql
SELECT 
  [表名]       = Case When A.colorder=1 Then D.name Else '' End, 
  [表说明]     = Case When A.colorder=1 Then isnull(F.value,'') Else '' End, 
  [字段序号]   = A.colorder, 
  [字段名]     = A.name, 
  [字段说明]   = isnull(G.[value],''), 
  [标识]       = Case When COLUMNPROPERTY( A.id,A.name,'IsIdentity')=1 Then '√'Else '' End, 
  [主键]       = Case When exists(SELECT 1 FROM sysobjects Where xtype='PK' and parent_obj=A.id and name in ( 
											SELECT name FROM sysindexes WHERE indid in( SELECT indid FROM sysindexkeys WHERE id = A.id AND colid=A.colid))) 
										  then '√' 
                      else '' 
								  end, 
  [类型]       = B.name, 
  [占用字节数] = A.Length, 
  [长度]       = COLUMNPROPERTY(A.id,A.name,'PRECISION'), 
  [小数位数]   = isnull(COLUMNPROPERTY(A.id,A.name,'Scale'),0), 
  [允许空]     = Case When A.isnullable=1 Then '√'Else '' End, 
  [默认值]     = isnull(E.Text,'') 
FROM syscolumns A 
Left Join systypes B On A.xusertype=B.xusertype 
Inner Join sysobjects D On A.id=D.id and D.xtype='U' and D.name<>'dtproperties' 
Left Join syscomments E on A.cdefault=E.id 
Left Join sys.extended_properties G on A.id=G.major_id and A.colid=G.minor_id 
Left Join sys.extended_properties F On D.id=F.major_id and F.minor_id=0 
  where d.name='table_name'
Order By 
  A.id,A.colorder 
```
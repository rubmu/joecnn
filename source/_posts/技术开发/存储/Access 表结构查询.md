---
title: Access 表结构查询
date: 2017/07/07 17:00:00
tags: [access]
---

#### 获取所有表  

```c#
var dt = conn.GetOleDbSchemaTable(OleDbSchemaGuid.Tables, null);
var query = dt.AsEnumerable()
              .Where(x => x.Field<string>("TABLE_TYPE") == "TABLE" ||
                          x.Field<string>("TABLE_TYPE") == "VIEW")
              .Select(x => x.Field<string>("TABLE_NAME"));
```

#### 获取表字段  

```sql
select * from table_name where 1<>1
```

#### 获取表主键  

```c#
DataTable dt = conn.GetOleDbSchemaTable(OleDbSchemaGuid.Primary_Keys, 
                    new object[] { null, null, tableName });
```

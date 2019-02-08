---
title: DataTable 常用操作
date: 2013/07/12
tags: [c#]
---

* 创建  
`DataTable dt = new DataTable()`

* 读取  
`dt.Rows[i]["columnName"].ToString()  `

* 创建新行
`DataRow row = dt.newRow()`

* 创建新列   
`dt.Columns.Add("columnName",typeof(string))`

* 添加新行  
`dt.Rows.Add(row)`

* 复制结构  
`dt.clone()` 

* 过滤  
`DataRow [] rows = dt.Select(" xxx="+xxx)`

* 排序  

```c#
DataView dv = dt.DefaultView;  
dv.Sort = " xxx desc";
DataTable dtSort=dv.ToTable();
```

* 删除  

```c#
for(int i=dt.Rows.Count;i>=0;i--) { 
    dt.Rows[i].Delete();
}
```

* 消除重复

```c#
Array columnName = dt.AsEnumerable()
                     .Select(row =>row[columnName])
                     .Distinct()
                     .ToArray(); 
ArrayList temp = ArrayList.Adapter(columnName); 
```
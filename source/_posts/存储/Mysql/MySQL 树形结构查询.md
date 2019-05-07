---
title: MySQL 树形结构查询
date: 2017/04/06
tags: [mysql]
---

在 ```oracle``` 中可以使用 ```Hierarchical Queries``` 通过 ```CONNECT BY``` 查询当前节点下所有节点，但在 ```mysql``` 中没有此功能，需要使用函数或者存储过程进行查询。

#### 0. 数据准备
> 树形结构，单侧树可以看做链表结构。
```sql
-- 树形结构
create table treeNodes (
      id int primary key,
      nodename varchar(20),
      pid int 
);
```

#### 1. 函数获得子节点
```sql
-- 查询树状节点函数
CREATE FUNCTION `getChildLst`(rootId INT)
 RETURNS varchar(1000)
 BEGIN
	 DECLARE sTemp VARCHAR(1000);
	 DECLARE sTempChd VARCHAR(1000);

	 SET sTemp = '$';
	 SET sTempChd =cast(rootId as CHAR);

	 WHILE sTempChd is not null DO
		 SET sTemp = concat(sTemp,',',sTempChd);
		 SELECT group_concat(id) INTO sTempChd FROM treeNodes WHERE FIND_IN_SET(pid,sTempChd)>0;
	 END WHILE;
	 RETURN sTemp;
END

-- 通过节点 rootid 查询该节点下的子节点树：  
select getChildLst(1);

-- 使用 FIND_IN_SET 配合查询节点信息：  
select * from treeNodes where FIND_IN_SET(id, getChildLst(1));
```
- **优点：** 简单、没有递归层数的限制(```max_sp_recursion_depth = 255```)。
- **缺点：** 查询结果 ```returns varchar(1000)``` 受字符长度限制，无法支持无限层树。

#### 2. 临时表与递归存储过程
```sql
-- 用于查询、初始化临时表的存储过程
 CREATE PROCEDURE showChildList (IN rootId INT)
 BEGIN
  CREATE TEMPORARY TABLE IF NOT EXISTS tmpLst (
    `sno` int primary key auto_increment COMMENT '排序',
	`id` int COMMENT '节点id',
	`depth` int COMMENT '节点深度');

  DELETE FROM tmpLst;

  CALL createChildLst(rootId,0);

  SELECT tmpLst.*, treeNodes.* FROM tmpLst,treeNodes WHERE tmpLst.id=treeNodes.id ORDER BY tmpLst.sno;
 END;
 
 -- 构建树结构存储过程
 CREATE PROCEDURE createChildLst (IN rootId INT,IN nDepth INT)
 BEGIN
  DECLARE done INT DEFAULT 0;
  DECLARE b INT;
  DECLARE cur1 CURSOR FOR SELECT id FROM treeNodes where pid=rootId;
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;
 
  insert into tmpLst values (null, rootId, nDepth);
 
  OPEN cur1;
 
  FETCH cur1 INTO b;
  WHILE done=0 DO
    CALL createChildLst(b,nDepth+1);
	FETCH cur1 INTO b;
  END WHILE;
 
  CLOSE cur1;
 END;
 
 -- 调用查询节点
 call showChildLst(1);
```
- **优点：** 可以更灵活处理、显示层数，并可以按照树的遍历顺序显示结构。
- **缺点：** 递归层数限制255

#### 3. 中间表和存储过程
> 由于 ```mysql``` 不允许在同一语句中对临时表多次引用，只能用普通表来实现，需要负责清理这个表。 

``` sql
-- 构建树结构存储过程
DROP PROCEDURE IF EXISTS showTreeNodes;
CREATE PROCEDURE showTreeNodes (IN rootid INT)
BEGIN
 DECLARE tLevel int ;
 DROP TABLE IF EXISTS tmpList;
 CREATE TABLE tmpList (
  `id` int,
  `level` int,
  `sort` varchar(8000)
 );
 
 Set tLevel = 0 ;
 INSERT into tmpList SELECT id, `tLevel`, ID FROM treeNodes WHERE PID = rootid;
 WHILE ROW_COUNT()>0 DO
  SET tLevel = tLevel + 1 ;
  INSERT into tmpList 
   SELECT A.ID, tLevel ,concat(B.sort, A.ID) FROM treeNodes A, tmpList
    WHERE A.PID=B.ID AND B.`level` = tLevel - 1  ;
 END WHILE;
  
END;

-- 调用存储过程，生成树结构
CALL showTreeNodes(0);

-- 查询结果
SELECT concat(SPACE(B.`level`*2),'+--',A.nodename)
  FROM treeNodes A,tmpList B 
 WHERE A.ID=B.ID 
 ORDER BY B.sort;
```
- **优点：** 可以显示层数、可以按照树的遍历顺序得到结果、没有递归最大层数限制。
- **缺点：** 由于 ```msql``` 中对临时表的限制，只能使用普通表，需要做后续数据清理工作。
---
title: Oracle 分区表
date: 2017/08/01
tags: [oracle]
---

### 一、分区表
&emsp;&emsp;随着表的不断增大，对于新纪录的增加、查找、删除等的维护也更加困难。对于数据库中的超大型表，可通过把它的数据分成若干个小表，从而简化数据库的管理活动。对于每一个简化后的小表，我们称为一个单个的分区。  
　　对于分区的访问，我们不需要使用特殊的SQL查询语句或特定的DML语句，而且可以单独的操作单个分区，而不是整个表。同时也可以将不同分区的数据放置到不同的表空间。  
　　对于外部应用程序来说，虽然存在不同的分区，且数据位于不同的表空间，***但逻辑上仍然是一张表***，可以使用SQL*Loader，IMPDP，EXPDP，Import，Export等工具来装载或卸载分区表中的数据。
　　
### 二、何时进行分区
- 当表达到GB大小且继续增长
- 需要将历史数据和当前的数据分开单独处理，比如历史数据仅仅需要只读，而当前数据则实现DML

### 三、分区的特性
- 共性：不同的分区之间必须有相同的逻辑属性，比如表名，列名，数据类型，约束等
- 个性：各个分区可以有不同的物理属性，比如pctfree, pctused, and tablespaces
- 独立性：即使某些分区不可用，其他分区仍然可用
- 特殊性：含有 **LONG**、**LONGRAW** 数据类型的表不能进行分区

### 四、分区的优点
1、提高查询性能：只需要搜索特定分区，而非整张表，提高查询速度  
2、节约维护时间：单个分区的数据装载，索引重建，备份，维护等将远小于整张表的维护时间  
3、节约维护成本：可以单独备份和恢复每个分区  
4、均衡I/O：将不同的分区映射到不同的磁盘以平衡I/O，提高并发

### 五、Oracle 分区类型
> 范围分区、散列分区、列表分区、组合分区

#### 1. Range 分区：行映射到基于列值范围的分区  
Range 分区，又成为范围分区，基于分区键值的范围将数据映射到所建立的分区。  
创建分区时必须指定以下内容：  
- 分区方法：range  
- 分区列  
- 标识分区边界的分区描述  

使用 Range 分区的时候，要记住几条规则：  
- 每个分区都包含 **VALUES LESS THAN** 字名，定义了分区的上层边界。任何等于和大于分区键值的二进制值都被添加到下一个高层分区中。  
- 所有的分区，除了第一个，如果低于VALUES LESS THAN所定义的下层边界，都放在前面的分区中。
- MAXVALUE 可以用来定义最高层的分区。MAXVALUE 表示了虚拟的无限值

示例：

```sql
-- 创建分区
create table sal_range   
            (salesman_id number(5),
            salesman_name varchar2(30),
            sales_amount number(10),
            sales_date date)
            partition by range (sales_date)   --创建基于日期的范围分区并存储到不同的表空间
            (
            partition sal_jan2000 values less than(to_date('02/01/2000',
                'DD/MM/YYYY')) tablespace sal_range_jan2000,
            partition sal_feb2000 values less than(to_date('03/01/2000',
                'DD/MM/YYYY')) tablespace sal_range_feb2000,
            partition sal_mar2000 values less than(to_date('04/01/2000',
                'DD/MM/YYYY')) tablespace sal_range_mar2000,
            partition sal_apr2000 values less than(to_date('05/01/2000',
                'DD/MM/YYYY')) tablespace sal_range_apr2000
            );

-- 查询分区中数据
select * from r partition (p1)

-- 添加分区
ALTER TABLE r add partition p5 values less than (xxx ) tablespace xx;

-- 查看分区表信息
 SELECT table_name,partition_name,subpartition_count,tablespace_name,user_stats 
  FROM user_tab_partitions;
```

####  2. Hash分区：散列分区
Hash 分区是根据分区字段的 Hash 值进行的散列分区，在下面这种
情况下，使用hash分区比range分区更好：
- 事先不知道需要将多少数据映射到给定范围的时候
- 分区的范围大小很难确定，或者很难平衡的时候
- Range 分区使数据得到不希望的聚集时
- 性能特性，如并行DML、分区剪枝和分区连接很重要的时候

创建散列分区时，必须指定以下信息
- 分区方法：hash
- 分区列
- 分区数量或单独的分区描述

创建hash分区有两种方法：一种方法是指定分区数量，另一种方法是指定分区的名字，但两者不能同时指定。

```sql
-- 方法一：指定分区数量
create table dept2 (deptno number,deptname varchar2(32))
partition by hash(deptno) partitions 4;

-- 方法二：指定分区的名字
create table sales_hash
        (salesman_id number(5),
        salesman_name varchar2(30),
        sales_amount number(10),
        week_no number(2))
partition by hash (salesman_id) partitions 4
store in (data1,data2,data3,data4) --指定表空间
```

#### 3. List分区：列表分区
List分区可以控制如何将行映射到分区中去，可以定义具体值指定到具体的分区。  
不同于Range分区和Hash分区：
- Range 分区与分区相关联，为分区列假设了一个值的自然范围，故不可能将该值的范围以外的分区组织到一起
- Hash 分区时不允许对数据的划分进行控制，因为系统使用的是散列函数来划分数据的
- List 分区的优点在于按照自然的方式将无序和不相关的数据集合分组
- List 分区不支持多列分区，如果将表按列分区，那么分区键就只能有表的一个单独列组成

List分区时必须指定的以下内容：
- 分区方法：list
- 分区列
- 分区描述，每个描述指定一串文字值(值的列表),它们是分区列(它们限定将被包括在分区中的行)的离散值

示例：

```sql
-- 创建分区
create table sales_list
        (salesman_id number(5),
        salesman_name varchar2(30),
        sales_state varchar2(20),
        sales_amount number(10),
        sales_date date)
partition by list (sales_state)
(
    partition sales_west values ('California','Hawaii') tablespace x,
    partition sales_east values ('New York','Virginia') tablespace y,
    partition sales_central values ('Texas','Illinois') tablespace z,
    partition sales_other values(DEFAULT) tablespace o
);

-- 添加分区
alter table sales3 add partition hk values ('HK') tablespace xx
```

#### 4. Composite Partitioning：合成分区、组合分区
组合分区使用 Range 方法分区，在每个子分区中使用 Hash 方法进行再分区。组合分区比 Range 分区更容易管理，充分使用了 Hash 分区的并行优势。组合分区支持历史数据和条块数据两者。  
创建组合分区时，需要指定如下内容：
- 分区方法：range
- 分区列
- 标识分区边界的分区描述
- 子分区方法：hash
- 子分区列
- 每个分区的子分区数量，或子分区的描述

示例：  

```sql
-- 创建分区 Range分区，Hash子分区
create table sales_composite
        (salesman_id number(5),
        salesman_name varchar2(30),
        sales_amount number(10),
        sales_date date)
partition by range(sales_date)
subpartition by hash(salesman_id) subpartitions 4
store in (tbs1,tbs2,tbs3,tbs4)
(
    partition sales_jan2000 values less than(to_date('02/01/2000','DD/MM/YYYY')),
    partition sales_feb2000 values less than(to_date('03/01/2000','DD/MM/YYYY')),
    partition sales_mar2000 values less than(to_date('04/01/2000','DD/MM/YYYY'))
);

--Range分区，List子分区
create table T_TRACK 
        (
            N_TRACK_ID           NUMBER(20)     NOT NULL, 
            C_COMP_CDE           VARCHAR2(6),
            T_TRACK_TM           DATE           NOT NULL,
            C_CAR_NO             VARCHAR2(50)
        )
partition by range(T_TRACK_TM)
subpartition by list(C_COMP_CDE)
(
    partition P_2009_11 values less than (to_date('2009-12-01','yyyy-MM-dd'))
    (
        subpartition P_2009_11_P1013 values('P1013')
    )
);
```

### 六、分区相关操作
1. 添加分区：

```sql
alter table T_TRACK add partition P_2005_04
values less than(to_date('2005-05-01','yyyy-MM-dd'))
(
    subpartition P_2005_04_P1013 values('P1013'),
    subpartition P_2005_04_P1013 values('P1014'),
    subpartition P_2005_04_P1013 values('P1015'),
    subpartition P_2005_04_P1013 values('P1016')
)
```
2. 删除分区：

```sql
alter table T_TRACK drop partition p_2005_04;
```

3. 添加子分区：

```sql
alter table T_TRACK
modify partition P_2005_01
add subpartition P_2005_01_P1017 values('P1017');
```

4. 删除子分区：

```sql
alter table T_TRACK drop subpartition p_2005_01_p1017;
```

5. 截断一个分区表中的一个分区的数据：

```sql
--这种方式会使全局分区索引无效
alter table sales3  truncate partition sp1

--这种方式全局分区索引不会无效            
alter table sales3 truncate partition sp1 update indexes
```

6. 截断分区表的子分区：

```sql
alter table comp truncate subpartition sub1
```

7. 截断带有约束的分区表

```sql
--禁用约束
alter table sales disable constraint dname_sales1

--截断分区
alter table sales truncate partitoin dec

--启用约束
alter table sales enable constraint dname_sales1
```

8. 查看一个表是不是分区表

```sql
select table_name,partitioned from user_tables;
```

9. 将一个表的分区从一个表空间移动到另一个表空间：

```sql
--查看分区在哪个表空间
SELECT TABLE_OWNER,TABLE_NAME,PARTITION_NAME,TABLESPACE_NAME, SUBPARTITION_COUNT
  FROM DBA_TAB_PARTITIONS WHERE TABLE_OWNER='SCOTT';

--移动分区
alter table sales move partiton sp1 tablespace tp;

--检查是否移动成功
SELECT TABLE_OWNER,TABLE_NAME,PARTITION_NAME,TABLESPACE_NAME,SUBPARTITION_COUNT
  FROM DBA_TAB_PARTITIONS WHERE TABLE_OWNER='SCOTT';

--移动表空间后，要重建索引，否则索引会变得无效
alter index xxx rebuild
```

10. 合并分区：

```sql
--合并后的分区名，不能是边界值较低的那个
alter table sales3 merge partitons sp1,sp3 into partition sp3
```

11. 与分区表相关的数据字典视图：
- DBA_TAB_PARTITIONS
- DBA_IND_PARTITIONS
- DBA_TAB_SUBPARTITIONS
- DBA_IND_SUBPARTITIONS

参考文档：  
[1] Oracle 关于分区的在线文档：http://download.oracle.com/docs/cd/B19306_01/server.102/b14220/partconc.htm#sthref2604



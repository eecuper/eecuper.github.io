---
layout: post
title: ORACLE 常用资源
category: 资源
tags: orale
keywords: oracle , tool
---

## Oracle 常用视图

### 1 查看用户

user_users
all_user
dba_user
特权用户：V$pwfile_users

### 2 查看权限

user_role_privs
all_role_privs
dba_role_privs
user_sys_privs
all_sys_privs
dba_sys_privs
user_tab_privs
all_tab_privs
dba_tab_privs
user_col_privs
all_col_privs
dba_col_privs
session_privs

### 3 查看表

user_tables
all_tables
dba_tables

### 4 查看列

user_tab_columns
all_tab_columns
dba_tab_columns

### 5 查看对象

user_tobjects
all_objects
dba_objects

### 6 查看段

user_segments
all_segments
dba_segments

### 7查看索引

user_index
all_index
dba_index

### 8 查看索引的字段

user_ind_columns
all_ind_columns
dba_ind_columns

### 9查看序列号

user_sequences
all_sequences
dba_sequences

### 10 查看视图

user_view

### 11 查看同义词

user_synonyms

### 12 查看约束条件

user_constraints

### 13 查看源代码

all_source

### 后注：

grantee：指被授者，即所要查询的用户的用户名
username：用户名
 

## Oracle常用数据字典表

### 1、 查看当前用户的缺省表空间

select username,default_tablespace from user_users; 

### 2、 查看当前用户的角色

select * from user_role_privs;

### 3、 查看当前用户的系统权限和表级权限

select * from user_sys_privs;
select * from user_tab_privs;

### 4、 查看用户下所有的表

select * from user_tables;

### 5、 查看用户下所有的表的列属性

select * from USER_TAB_COLUMNS where table_name=:table_Name;

### 6、 显示用户信息(所属表空间)

select default_tablespace, temporary_tablespace
  from dba_users
 where username = 'GAME';

### 7、 显示当前会话所具有的权限

select * from session_privs;

### 8、 显示指定用户所具有的系统权限

select * from dba_sys_privs where grantee='GAME';

### 9、 显示特权用户

select * from v$pwfile_users;

### 10、 显示用户信息(所属表空间)

select default_tablespace,temporary_tablespace 
from dba_users where username='GAME';

### 11、 显示用户的PROFILE

select profile from dba_users where username='GAME';

### 2 表

1、 查看用户下所有的表

select * from user_tables;

2、 查看名称包含log字符的表

select object_name,object_id from user_objects
where instr(object_name,'LOG')>0;

3、 查看某表的创建时间

select object_name,created from user_objects where object_name=upper('&table_name');

4、 查看某表的大小

select sum(bytes)/(1024*1024) as "size(M)" from user_segments
where segment_name=upper('&table_name');

5、 查看放在Oracle的内存区里的表

select table_name,cache from user_tables where instr(cache,'Y')>0;

### 3 索引

1、 查看索引个数和类别

select index_name,index_type,table_name from user_indexes order by table_name;

2、 查看索引被索引的字段

select * from user_ind_columns where index_name=upper('&index_name');

3、 查看索引的大小

select sum(bytes)/(1024*1024) as "size(M)" from user_segments
where segment_name=upper('&index_name');

4 序列号

1、 查看序列号，last_number是当前值

select * from user_sequences;

## 5 视图

1、 查看视图的名称

select view_name from user_views;

2、 查看创建视图的select语句

set view_name,text_length from user_views;
set long 2000; 说明：可以根据视图的text_length值设定set long 的大小
select text from user_views where view_name=upper('&view_name');

## 6 同义词

1、 查看同义词的名称

select * from user_synonyms;

## 7 约束条件

1、 查看某表的约束条件

select constraint_name, constraint_type,search_condition, r_constraint_name
from user_constraints where table_name = upper('&table_name');
select c.constraint_name,c.constraint_type,cc.column_name
from user_constraints c,user_cons_columns cc
where c.owner = upper('&table_owner') and c.table_name = upper('&table_name')
and c.owner = cc.owner and c.constraint_name = cc.constraint_name
order by cc.position;

## 8 存储函数和过程

1、 查看函数和过程的状态

select object_name,status from user_objects where object_type='FUNCTION';
select object_name,status from user_objects where object_type='PROCEDURE';

2、 查看函数和过程的源代码

select text from all_source where owner=user and name=upper('&plsql_name');

## 9 常用的数据字典：

dba_data_files:通常用来查询关于数据库文件的信息

dba_db_links:包括数据库中的所有数据库链路，也就是databaselinks、

dba_extents:数据库中所有分区的信息

dba_free_space:所有表空间中的自由分区

dba_indexs:关于数据库中所有索引的描述

dba_ind_columns:在所有表及聚集上压缩索引的列

dba_objects:数据库中所有的对象

dba_rollback_segs:回滚段的描述

dba_segments:所有数据库段分段的存储空间

dba_synonyms:关于同义词的信息查询

dba_tables:数据库中所有数据表的描述

dba_tabespaces:关于表空间的信息

dba_tab_columns:所有表描述、视图以及聚集的列

dba_tab_grants/privs:对象所授予的权限

dba_ts_quotas:所有用户表空间限额

dba_users:关于数据的所有用户的信息

dba_views:数据库中所有视图的文本

## 10 常用的动态性能视图：

v$datafile：数据库使用的数据文件信息

v$librarycache：共享池中SQL语句的管理信息

v$lock：通过访问数据库会话，设置对象锁的所有信息

v$log：从控制文件中提取有关重做日志组的信息

v$logfile有关实例重置日志组文件名及其位置的信息

v$parameter：初始化参数文件中所有项的值

v$process：当前进程的信息

v$rollname：回滚段信息

v$rollstat：联机回滚段统计信息

v$rowcache：内存中数据字典活动/性能信息

v$session:有关会话的信息

v$sesstat：在v$session中报告当前会话的统计信息

v$sqlarea：共享池中使用当前光标的统计信息，光标是一块内存区域，有Oracle处理SQL语句时打开、

v$statname：在v$sesstat中报告各个统计的含义

v$sysstat：基于当前操作会话进行的系统统计

v$waitstat：出现一个以上会话访问数据库的数据时的详细情况、当有一个以上的会话访问同一信息时，可出现等待情况、

总结了一下这些，彻底区别了视图与数据字典，也不那么容易混淆、嘿嘿!!!

## 11 常用SQL查询：

### 1、查看表空间的名称及大小

```
select t.tablespace_name, round(sum(bytes/(1024*1024)),0) ts_size
from dba_tablespaces t, dba_data_files d
where t.tablespace_name = d.tablespace_name
group by t.tablespace_name;
```
 
### 2、查看表空间物理文件的名称及大小

```
select tablespace_name, file_id, file_name,
round(bytes/(1024*1024),0) total_space
from dba_data_files
order by tablespace_name;
```
 
### 3、查看回滚段名称及大小

```
select segment_name, tablespace_name, r.status, 
(initial_extent/1024) InitialExtent,(next_extent/1024) NextExtent, 
max_extents, v.curext CurExtent
From dba_rollback_segs r, v$rollstat v
Where r.segment_id = v.usn(+)
order by segment_name;
```
 
### 4、查看控制文件

select name from v$controlfile;

### 5、查看日志文件

select member from v$logfile;

### 6、查看表空间的使用情况

```
select sum(bytes)/(1024*1024) as free_space,tablespace_name 
from dba_free_space
group by tablespace_name;
 
SELECT A.TABLESPACE_NAME,A.BYTES TOTAL,B.BYTES USED, C.BYTES FREE,
(B.BYTES*100)/A.BYTES "% USED",(C.BYTES*100)/A.BYTES "% FREE"
FROM SYS.SM$TS_AVAIL A,SYS.SM$TS_USED B,SYS.SM$TS_FREE C
WHERE A.TABLESPACE_NAME=B.TABLESPACE_NAME AND A.TABLESPACE_NAME=C.TABLESPACE_NAME;
```

### 7、查看数据库库对象

```
select owner, object_type, status, count(*) count# from all_objects group by owner, object_type, status;
```

### 8、查看数据库的版本　

```
Select version FROM Product_component_version 
Where SUBSTR(PRODUCT,1,6)='Oracle';
```

### 9、查看数据库的创建日期和归档方式

Select Created, Log_Mode, Log_Mode From V$Database; 

### 10、捕捉运行很久的SQL

column username format a12 
column opname format a16 
column progress format a8 

```
select username,sid,opname, 
round(sofar*100 / totalwork,0) || '%' as progress, 
time_remaining,sql_text 
from v$session_longops , v$sql 
where time_remaining <> 0 
and sql_address = address 
and sql_hash_value = hash_value 
```
 
### 11、查看数据表的参数信息

```
SELECT   partition_name, high_value, high_value_length, tablespace_name,
pct_free, pct_used, ini_trans, max_trans, initial_extent,
next_extent, min_extent, max_extent, pct_increase, FREELISTS,
freelist_groups, LOGGING, BUFFER_POOL, num_rows, blocks,
empty_blocks, avg_space, chain_cnt, avg_row_len, sample_size,
last_analyzed
FROM dba_tab_partitions
--WHERE table_name = :tname AND table_owner = :towner
ORDER BY partition_position
```
 
### 12、查看还没提交的事务

select * from v$locked_object;
select * from v$transaction;

### 13、查找object为哪些进程所用

```
select 
p.spid,
s.sid,
s.serial# serial_num,
s.username user_name,
a.type  object_type,
s.osuser os_user_name,
a.owner,
a.object object_name,
decode(sign(48 - command),
1,
to_char(command), 'Action Code #' || to_char(command) ) action,
p.program oracle_process,
s.terminal terminal,
s.program program,
s.status session_status   
from v$session s, v$access a, v$process p   
where s.paddr = p.addr and
s.type = 'USER' and    
a.sid = s.sid   and
a.object='SUBSCRIBER_ATTR'
order by s.username, s.osuser
```

### 14、回滚段查看

```
select rownum, sys.dba_rollback_segs.segment_name Name, v$rollstat.extents 
Extents, v$rollstat.rssize Size_in_Bytes, v$rollstat.xacts XActs, 
v$rollstat.gets Gets, v$rollstat.waits Waits, v$rollstat.writes Writes, 
sys.dba_rollback_segs.status status from v$rollstat, sys.dba_rollback_segs, 
v$rollname where v$rollname.name(+) = sys.dba_rollback_segs.segment_name and 
v$rollstat.usn (+) = v$rollname.usn order by rownum
```
 
### 15、耗资源的进程（top session）

```
select s.schemaname schema_name,    decode(sign(48 - command), 1, 
to_char(command), 'Action Code #' || to_char(command) ) action,    status 
session_status,   s.osuser os_user_name,   s.sid,         p.spid ,         s.serial# serial_num,   
nvl(s.username, '[Oracle process]') user_name,   s.terminal terminal,    
s.program program,   st.value criteria_value  from v$sesstat st,   v$session s  , v$process p   
where st.sid = s.sid and   st.statistic# = to_number('38') and   ('ALL' = 'ALL' 
or s.status = 'ALL') and p.addr = s.paddr order by st.value desc,  p.spid asc, s.username asc, s.osuser asc
```
 
### 16、查看锁（lock）情况

```
select /*+ RULE */ ls.osuser os_user_name,   ls.username user_name,   
decode(ls.type, 'RW', 'Row wait enqueue lock', 'TM', 'DML enqueue lock', 'TX', 
'Transaction enqueue lock', 'UL', 'User supplied lock') lock_type,   
o.object_name object,   decode(ls.lmode, 1, null, 2, 'Row Share', 3, 
'Row Exclusive', 4, 'Share', 5, 'Share Row Exclusive', 6, 'Exclusive', null) 
lock_mode,    o.owner,   ls.sid,   ls.serial# serial_num,   ls.id1,   ls.id2    
from sys.dba_objects o, (   select s.osuser,    s.username,    l.type,     
l.lmode,    s.sid,    s.serial#,    l.id1,    l.id2   from v$session s,     
v$lock l   where s.sid = l.sid ) ls  where o.object_id = ls.id1 and    o.owner 
<> 'SYS'   order by o.owner, o.object_name
```

### 17、查看等待（wait）情况

```
SELECT v$waitstat.class, v$waitstat.count count, SUM(v$sysstat.value) sum_value 
FROM v$waitstat, v$sysstat WHERE v$sysstat.name IN ('db block gets', 
'consistent gets') group by v$waitstat.class, v$waitstat.count
```

### 18、查看sga情况

```
SELECT NAME, BYTES FROM SYS.V_$SGASTAT ORDER BY NAME ASC
19、查看catched object
SELECT owner,              name,              db_link,              namespace,  
 type,              sharable_mem,              loads,              executions,   
locks,              pins,              kept        FROM v$db_object_cache
```

### 20、查看V$SQLAREA

```
SELECT SQL_TEXT, SHARABLE_MEM, PERSISTENT_MEM, RUNTIME_MEM, SORTS, 
VERSION_COUNT, LOADED_VERSIONS, OPEN_VERSIONS, USERS_OPENING, EXECUTIONS, 
USERS_EXECUTING, LOADS, FIRST_LOAD_TIME, INVALIDATIONS, PARSE_CALLS, DISK_READS,
BUFFER_GETS, ROWS_PROCESSED FROM V$SQLAREA
```

### 21、查看object分类数量

```
select decode (o.type#,1,'INDEX' , 2,'TABLE' , 3 , 'CLUSTER' , 4, 'VIEW' , 5 , 
'SYNONYM' , 6 , 'SEQUENCE' , 'OTHER' ) object_type , count(*) quantity from 
sys.obj$ o where o.type# > 1 group by decode (o.type#,1,'INDEX' , 2,'TABLE' , 3 
, 'CLUSTER' , 4, 'VIEW' , 5 , 'SYNONYM' , 6 , 'SEQUENCE' , 'OTHER' ) union select 
'COLUMN' , count(*) from sys.col$ union select 'DB LINK' , count(*) from 
```

### 22、按用户查看object种类

```
select u.name schema,   sum(decode(o.type#, 1, 1, NULL)) indexes,   
sum(decode(o.type#, 2, 1, NULL)) tables,   sum(decode(o.type#, 3, 1, NULL)) 
clusters,   sum(decode(o.type#, 4, 1, NULL)) views,   sum(decode(o.type#, 5, 1, 
NULL)) synonyms,   sum(decode(o.type#, 6, 1, NULL)) sequences,   
sum(decode(o.type#, 1, NULL, 2, NULL, 3, NULL, 4, NULL, 5, NULL, 6, NULL, 1)) 
others   from sys.obj$ o, sys.user$ u   where o.type# >= 1 and    u.user# = 
o.owner# and   u.name <> 'PUBLIC'   group by u.name    order by 
sys.link$ union select 'CONSTRAINT' , count(*) from sys.con$
```
 
### 23、有关connection的相关信息

#### 1）查看有哪些用户连接

```
select s.osuser os_user_name,    decode(sign(48 - command), 1, to_char(command),
'Action Code #' || to_char(command) ) action,     p.program oracle_process,     
status session_status,    s.terminal terminal,    s.program program,    
s.username user_name,    s.fixed_table_sequence activity_meter,    '' query,    
0 memory,    0 max_memory,     0 cpu_usage,    s.sid,   s.serial# serial_num    
from v$session s,    v$process p   where s.paddr=p.addr and    s.type = 'USER'  
order by s.username, s.osuser
```

#### 2）根据v.sid查看对应连接的资源占用等情况

```select n.name, 
v.value, 
n.class,
n.statistic#  
from  v$statname n, 
v$sesstat v 
where v.sid = 71 and 
v.statistic# = n.statistic# 
order by n.class, n.statistic#
```

#### 3）根据sid查看对应连接正在运行的sql

```
select /*+ PUSH_SUBQ */
command_type, 
sql_text, 
sharable_mem, 
persistent_mem, 
runtime_mem, 
sorts, 
version_count, 
loaded_versions, 
open_versions, 
users_opening, 
executions, 
users_executing, 
loads, 
first_load_time, 
invalidations, 
parse_calls, 
disk_reads, 
buffer_gets, 
rows_processed,
sysdate start_time,
sysdate finish_time,
'>' || address sql_address,
'N' status 
from v$sqlarea
where address = (select sql_address from v$session where sid = 71)
```
 
#### 4）根据v.sid查看对应连接的资源占用等情况

```select n.name, 
v.value, 
n.class,
n.statistic#  
from  v$statname n, 
v$sesstat v 
where v.sid = 71 and 
v.statistic# = n.statistic# 
order by n.class, n.statistic#
3）根据sid查看对应连接正在运行的sql
select /*+ PUSH_SUBQ */
command_type, 
sql_text, 
sharable_mem, 
persistent_mem, 
runtime_mem, 
sorts, 
version_count, 
loaded_versions, 
open_versions, 
users_opening, 
executions, 
users_executing, 
loads, 
first_load_time, 
invalidations, 
parse_calls, 
disk_reads, 
buffer_gets, 
rows_processed,
sysdate start_time,
sysdate finish_time,
'>' || address sql_address,
'N' status 
from v$sqlarea
where address = (select sql_address from v$session where sid = 71)
```

### 24．查询表空间使用情况

```select a.tablespace_name "表空间名称",
100-round((nvl(b.bytes_free,0)/a.bytes_alloc)*100,2) "占用率(%)",
round(a.bytes_alloc/1024/1024,2) "容量(M)",
round(nvl(b.bytes_free,0)/1024/1024,2) "空闲(M)",
round((a.bytes_alloc-nvl(b.bytes_free,0))/1024/1024,2) "使用(M)",
Largest "最大扩展段(M)",
to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') "采样时间" 
from  (select f.tablespace_name,
sum(f.bytes) bytes_alloc,
sum(decode(f.autoextensible,'YES',f.maxbytes,'NO',f.bytes)) maxbytes 
from dba_data_files f 
group by tablespace_name) a,
(select  f.tablespace_name,
sum(f.bytes) bytes_free 
from dba_free_space f 
group by tablespace_name) b,
(select round(max(ff.length)*16/1024,2) Largest,
ts.name tablespace_name 
from sys.fet$ ff, sys.file$ tf,sys.ts$ ts 
where ts.ts#=ff.ts# and ff.file#=tf.relfile# and ts.ts#=tf.ts# 
group by ts.name, tf.blocks) c 
where a.tablespace_name = b.tablespace_name and a.tablespace_name = c.tablespace_name
```

### 25. 查询表空间的碎片程度 

```select tablespace_name,count(tablespace_name) from dba_free_space group by tablespace_name 
having count(tablespace_name)>10; 
alter tablespace name coalesce; 
alter table name deallocate unused; 
create or replace view ts_blocks_v as 
select tablespace_name,block_id,bytes,blocks,'free space' segment_name from dba_free_space 
union all 
select tablespace_name,block_id,bytes,blocks,segment_name from dba_extents; 
select * from ts_blocks_v; 
select tablespace_name,sum(bytes),max(bytes),count(block_id) from dba_free_space 
group by tablespace_name;
```

### 26、查询有哪些数据库实例在运行

```
select inst_name from v$active_instances;
//取得服务器的IP 地址
select utl_inaddr.get_host_address from dual
//取得客户端的IP地址
select sys_context('userenv','host'),sys_context('userenv','ip_address') from dual
```
 
 

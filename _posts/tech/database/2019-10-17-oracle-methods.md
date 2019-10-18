---
layout: post
title: oracle 使用收集
category: 技术
tags: orale
keywords: oracle , tool
---

### 横列转换 PIVOT 和 UNPIVOT 使用

```
select a.wc_id,a.name,a.org_id,a.xy,b.name oper_name from rank_d_wc_xy a,rank_d_users b where a.org_id=b.org_id
```


```
CREATE OR REPLACE VIEW RANK_DV_WC_XY_TABLE AS
SELECT wc_id,org_id,oper_name,
终端销售,
宽带续费
FROM (select a.wc_id,a.name,a.org_id,a.xy,b.name oper_name from rank_d_wc_xy a,rank_d_users b where a.org_id=b.org_id)
PIVOT(MAX(xy)
 FOR name IN(
  '终端销售' as 终端销售,
  '宽带续费' as 宽带续费
  )
) order by wc_id , oper_name;
```


### 树形结构表查询当前位置层级

```
select CONNECT_BY_ROOT(ID) 顶级节点ID,  
       CONNECT_BY_ROOT(area) 顶级节点名称,
       CONNECT_BY_ISLEAF 是否叶节点,
       SUBSTR(SYS_CONNECT_BY_PATH(area,'->'),3) 当前位置,
       RPAD(' ',2*(lvl-1))||area 缩进展示,
       a.* 
       from rank_wgh_area a START WITH pid =1 CONNECT BY PRIOR  id=pid
```


### oracle 查询树结构数据

```
SELECT a.*,LEVEL, RPAD(' ',2*(LEVEL-1)) || area shu
FROM rank_wgh_area a
START WITH id=1
CONNECT BY pid = PRIOR id
ORDER SIBLINGS BY id;

--树
SELECT * FROM t_sys_dept where dept_status in (0,1)  START WITH dept_parent_id =10 CONNECT BY PRIOR  dept_id=dept_parent_id
```


### plsql 中获取当前用户的方法

```
select USER  from dual;
```

### 剔重

```
根据rpt_month ,config_id  分组并取分组后前三3 数据
select t.*,row_number()over (partition by rpt_month,config_id order by amount desc) rn from rank_user_info t) where rn<=
```

### 排名

```
rank()over(partition by ... order by ...)
dise_rank()over(partition by ... order by ...) 是否允许相同分值排名相同
```

### 查询指定表表结构

```
select a.column_name,data_type,decode(nullable,'Y',0,1) notnull,data_default,decode(a.column_name,b.column_name,1,0) pk from user_tab_columns a,(select column_name from user_constraints c,user_cons_columns col where c.constraint_name=col.constraint_name and c.constraint_type='P'and c.table_name='FT_161213101144') b where table_name='FT_161213101144'
and a.column_name=b.column_name(+)
```

### 字符串表达式求值

```
WITH T AS
( SELECT '1+2+3+4' express FROM DUAL
   UNION ALL
   SELECT '21+2+32+4'  FROM DUAL
   UNION ALL
   SELECT '41+22+13+4' FROM DUAL
  )
SELECT dbms_aw.eval_number(express ) FROM T
```


### 提取字段中的数字信息

```
select t20,translate(t20,'1234567890.'||t20,'1234567890.') as "保留数字"   from macc.lmf_1402 where t20 is not null 
```

### 某个字符出现的次数 Oracle

```
select 字段, length(replace(字段, '-', '--'))-length(字段)   as   字符出现次数   from   表名

length(translate(bill_id,'abcdefghijklmnopqrstuvwxyz.'||bill_id,'abcdefghijklmnopqrstuvwxyz.'))>0
```

### for update 引起 scn 值跳跃增加 bug 解决办法

```
select t.* , rowid from table_name t 
替代
select ...  for update 
```

### 指定某个菜单下所有报表访问记清单

```
select b.menu_id,c.menu_name,a.rpt_id,d.rpt_name,a.view_oper_name,a.view_oper_date,a.view_ip
from mz_user.ls_rpt_view_log a,
     mz_user.ls_rpt_menu_link b,
     mz_user.t_sys_menu c,
     mz_user.ls_rpt_design d
where b.rpt_id=a.rpt_id(+)  
  and a.rpt_id=d.id   
  and b.menu_id=c.menu_id
  and c.menu_status=1
  and b.menu_id in (select menu_id FROM mz_user.t_sys_menu a
  START WITH menu_id=5000108431 CONNECT BY PRIOR  menu_id=menu_parent_id and menu_status=1)
```

### 随即更新时间并且控制一定的范围

```
update t set timestamp=timestamp + to_number(to_date('20130701', 'YYYYMMDD') - trunc(to_date(to_char( timestamp,'YYYYMMDD'),'YYYYMMDD') - DBMS_RANDOM.VALUE(1,180),'DD'))
以上是更新时间控制在 201307-201312 期间
```

 
### 中位值

median(column)



### 角色对应菜单矩阵

```
select distinct *from (
select role_name,
                 decode(count(distinct 县市业务统计)over(partition by role_name),1,'×',0,'×','√') 县市业务统计,
                 decode(count(distinct 登陆日志 )over(partition by role_name),1,'×',0,'×','√') 登陆日志 ,
                 decode(count(distinct 用户管理 )over(partition by role_name),1,'×',0,'×','√') 用户管理 ,
                 decode(count(distinct 角色管理 )over(partition by role_name),1,'×',0,'×','√') 角色管理 ,
                 decode(count(distinct 营业统计 )over(partition by role_name),1,'×',0,'×','√') 营业统计 ,
                 decode(count(distinct 业务统计 )over(partition by role_name),1,'×',0,'×','√') 业务统计 ,
                 decode(count(distinct 取号清单 )over(partition by role_name),1,'×',0,'×','√') 取号清单 ,
                 decode(count(distinct 营业窗口统计)over(partition by role_name),1,'×',0,'×','√') 营业窗口统计,
                 decode(count(distinct 营业业务统计)over(partition by role_name),1,'×',0,'×','√') 营业业务统计,
                 decode(count(distinct 部门管理 )over(partition by role_name),1,'×',0,'×','√') 部门管理 ,
                 decode(count(distinct 县市统计 )over(partition by role_name),1,'×',0,'×','√') 县市统计 ,
                 decode(count(distinct 营业时段统计)over(partition by role_name),1,'×',0,'×','√') 营业时段统计
                 
from (
select b.role_name,
case when a.menu_name = '县市业务统计' then '√' else '×' end as 县市业务统计,
case when a.menu_name = '登陆日志' then '√' else '×' end as 登陆日志 ,
case when a.menu_name = '用户管理' then '√' else '×' end as 用户管理 ,
case when a.menu_name = '角色管理' then '√' else '×' end as 角色管理 ,
case when a.menu_name = '营业统计' then '√' else '×' end as 营业统计 ,
case when a.menu_name = '业务统计' then '√' else '×' end as 业务统计 ,
case when a.menu_name = '取号清单' then '√' else '×' end as 取号清单 ,
case when a.menu_name = '营业窗口统计' then '√' else '×' end as 营业窗口统计,
case when a.menu_name = '营业业务统计' then '√' else '×' end as 营业业务统计,
case when a.menu_name = '部门管理' then '√' else '×' end as 部门管理 ,
case when a.menu_name = '县市统计' then '√' else '×' end as 县市统计 ,
case when a.menu_name = '营业时段统计' then '√' else '×' end as 营业时段统计
  from t_sys_role b,t_sys_menu a ,t_sys_role_menu c
where c.rm_menu_id = a.menu_id
and b.role_id = c.rm_role_id
group by b.role_name, a.menu_name))
```
---
layout: post
title: mysql 中 FIND_IN_SET 函数执行非常慢的某种写法
category: 技术
tags: mysql
keywords: mysql , find_in_set
---

### 自定义函数用例

```
DELIMITER $$ 
CREATE   FUNCTION fn_fx_ids(uid INT) RETURNS VARCHAR(2000)
BEGIN
  DECLARE str VARCHAR(1000);
  DECLARE cid VARCHAR(2000);
  SET str = '$';
  SET cid = CAST(uid AS CHAR);
  WHILE cid IS NOT NULL DO  
    SET str = CONCAT(str, ',', cid);
    SELECT  GROUP_CONCAT(fx_user) INTO cid  FROM  ims_zhvip_user  WHERE FIND_IN_SET(pfx_user, cid);
  END WHILE;
  RETURN str;
END $$
```

### 1、需求场景

a) 表说明：商品分类关系表[t_class_product]，[classId]  分类主键，fn_class_childs(1) 返回所有子分类主键字符串;
b) 功能说明： 依据 分类主键 查询该分类下的所有商品 
SELECT a.* FROM ims_zhvip_user a WHERE FIND_IN_SET(fx_user,fn_fx_ids(12213897))


### 2、问题定位

a) 之前操作：新上传几千个商品，然后就发现查询数据非常慢，长达10多秒；
b) 开始以为 [fn_class_childs()] 函数执行慢，结果执行非常快;
c) 然后怀疑是不是 [FIND_IN_SET] 函数 查询数据多就非常慢，
     然后查找函数的效率问题，但发现至少查询几十,上百万数据才有影响，而且也不会需要10几秒的时间;
d) 结果分开查询都很快,  合起来就慢的很，使用 explain 分析也没发现什么问题，最后怀疑可能每次比较都可能调用函数[fn_class_childs()]
SELECT a.* FROM ims_zhvip_user a,(SELECT fn_fx_ids(12213897) fx_users) t WHERE FIND_IN_SET(pfx_user,fx_users)·


### 3、解决方式

a) 既然找到可能的问题，就自然想到缓存查询函数结果，但又不想改的太复杂；
b) 使用 explain 可以发现 使用了临时表存储结果，查询速度恢复以前的十几毫秒;

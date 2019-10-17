---
layout: post
title: ORACLE 运行监控(2) 表结构
category: 资源
tags: orale
keywords: oracle , job
---

```
-- Create table
create table LS_RPT_JOB
(
  ID          NUMBER,
  RPT_ID      NUMBER,
  NAME        VARCHAR2(200),
  REQUEST     VARCHAR2(100),
  MANAGER     VARCHAR2(100),
  MS          VARCHAR2(2000),
  SQL         CLOB,
  LAST_DATE   DATE,
  THIS_DATE   DATE,
  TIMER       VARCHAR2(100),
  FAILURES    NUMBER default 0,
  STATUS      VARCHAR2(10),
  MSG         VARCHAR2(4000),
  EXT1        NUMBER,
  EXT2        NUMBER,
  EXT3        NUMBER,
  CREATE_DATE DATE,
  REMARK      VARCHAR2(200),
  STATE       NUMBER default 1
)


create table T_PROC_ERRLOG_B
(
  TREATDATE DATE,
  PROC_NAME CHAR(16),
  ERRMSG    VARCHAR2(4000),
  JOB_ID    VARCHAR2(100),
  REMARK    CHAR(1)
)
```
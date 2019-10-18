---
layout: post
title: ORACLE 运行监控(1)创建执行一次JOB自动消除写法实例
category: 技术
tags: orale
keywords: oracle , job
---

```
v_var(v_index):='CRM_任务运行监控('||v_id||');';
v_pros:=v_pros||v_var(v_index);

sys.DBMS_JOB.submit (
        job => x, -- x为JOB自增ID ， 定义变量只需要定义一个数字变量即可如：x number;
        what => v_var(v_index),
        next_date => SYSDATE,
        interval => NULL,  --这里必须为空，标示只执行一次，并且自动销毁
        --TO_DATE('30-04-2014 00:00:00', 'dd-mm-yyyy hh24:mi:ss')
        --interval => 'trunc(sysdate+1)',
        no_parse => FALSE);
       
       
     --SYS.DBMS_OUTPUT.put_line ('Job Number is: ' || TO_CHAR (x));
     --COMMIT; 
```
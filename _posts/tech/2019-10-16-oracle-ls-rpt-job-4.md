---
layout: post
title: ORACLE 运行监控(4) 存储调用
category: 资源
tags: orale
keywords: oracle , job
---

```
create or replace procedure CRM_任务运行监控_调用 is
v_sqlerrm         varchar2(3000);

cursor  c_wait_run is select id from ls_rpt_job where status=2;

v_job  c_wait_run%rowtype;
                  
begin
      
     INSERT INTO t_proc_errlog_c (treatdate,proc_name)
         VALUES (SYSDATE,'调用扫描...');
     COMMIT; 
     
     open c_wait_run ;
     
        loop
            fetch c_wait_run into v_job;
            
                  exit when c_wait_run%notfound;
                  
                  INSERT INTO t_proc_errlog_c (treatdate,proc_name,job_name)
                       VALUES (SYSDATE,'调用',to_char(v_job.id));
                   COMMIT;
                  
                  CRM_任务运行监控(to_char(v_job.id));
              
        end loop;
        
      if c_wait_run%isopen then
        close c_wait_run;
        commit;
      end if;

exception when others then
v_sqlerrm := '[proc_name]:CRM_任务运行监控_调用,[local]:1,[err msg]:'||to_char(sqlcode)||substrb(sqlerrm,1,400);
insert into T_PROC_ERRLOG_b(treatdate,errmsg)values(sysdate,v_sqlerrm);
commit;

end CRM_任务运行监控_调用;

```
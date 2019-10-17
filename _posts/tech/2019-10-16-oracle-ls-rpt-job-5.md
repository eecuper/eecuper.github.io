---
layout: post
title: ORACLE 运行监控(5) 并行调用
category: 资源
tags: orale
keywords: oracle , job
---

```
CREATE OR REPLACE PROCEDURE CRM_任务运行监控_定时并行 IS
x NUMBER; --最新job序号
V_SQLERRM VARCHAR2(3000);
v_str VARCHAR2(50); --时分
v_type VARCHAR2(10); --频率
v_dd NUMBER; --日
v_hh NUMBER; --时
v_mi NUMBER; --分
hh_curr NUMBER; --当前时
mi_curr NUMBER; --当前分
dd_curr NUMBER; --当前日
ww_curr NUMBER; --当前周X
v_curr VARCHAR2(8); --当天
v_flag NUMBER; --是否执行标记 1 执行 0 掠过
v_id NUMBER;
v_pros VARCHAR2(3000);--并行过程字符串
TYPE T_TAB IS TABLE OF VARCHAR2(30) INDEX BY BINARY_INTEGER;
--索引是数组的下标
V_VAR T_TAB;
V_INDEX NUMBER;
CURSOR c_jobs IS
       SELECT ID,
              NAME,
              timer,
              status,
              last_date,
              to_char(this_date,'yyyymmdd') this_date
              FROM ls_rpt_job
              WHERE state=1 --正常使用
              AND status IN (0,-1) --已停止,错误停止
              AND nvl(failures,0)<=15; --执行错误次数 6 次以下
             
           
       
v_job c_jobs%ROWTYPE;
--timer 数据库存储样例: 每日: DAY|10:45
-- 每周: WEEK|3|9:00
-- 每月: MONTH|15|17:00
BEGIN
 
  --记录一次扫描
  INSERT INTO t_proc_errlog_c (treatdate,proc_name,job_name)
         VALUES (SYSDATE,'CRM_任务运行监控_定时并行','开始扫描...');
  COMMIT;
           
  v_flag:=0; --默认不执行
 
  hh_curr:=to_number(to_char(SYSDATE,'hh24'));
  mi_curr:=to_number(to_char(SYSDATE,'mi'));
 
  dd_curr:=to_number(to_char(SYSDATE,'dd'));
  ww_curr:=to_number(to_char(SYSDATE-1,'D')); --每周是重礼拜天算第一天,所以需要减1
 
  v_curr :=to_char(SYSDATE,'yyyymmdd');
 
  IF c_jobs%ISOPEN THEN
     CLOSE c_jobs;
  END IF;
  OPEN c_jobs;
  V_INDEX:=1; --数字下标
  LOOP
  FETCH c_jobs INTO v_job ;
        EXIT WHEN c_jobs%NOTFOUND; --OR v_flag=1
       
        v_type:=substr(v_job.timer,0,instr(v_job.timer,'|')-1);
       
       
        IF v_type='DAY' THEN
           v_str :=substr(v_job.timer,instr(v_job.timer,'|')+1);
        ELSE
           v_str :=substr(v_job.timer,instr(v_job.timer,'|',1,2)+1);
           v_dd :=to_number(substr(v_job.timer,instr(v_job.timer,'|')+1,instr(v_job.timer,'|',1,2)-instr(v_job.timer,'|')-1));
        END IF;
       
        v_hh :=substr(v_str,0,instr(v_str,':')-1);
        v_mi :=substr(v_str,instr(v_str,':')+1);
       
        --DBMS_OUTPUT.put_line(' id '||v_job.id||' name '||v_job.name);
        --dbms_output.put_line('hh_curr :'||hh_curr||' mi_curr :'||mi_curr);
        --DBMS_OUTPUT.put_line('v_type : '||v_type||' v_str : '||v_str||' v_dd : '||v_dd||' v_hh : '||v_hh||' v_mi : '||v_mi);
        --1.如果是每天则计算 时 分 即可
        IF v_type='DAY' THEN
           -------------------------------共用部分----------------------------
           IF v_job.this_date<>v_curr THEN
              IF v_hh<hh_curr THEN
                 
                 v_flag:=1;
                 v_id:=v_job.id;
                 
              ELSIF v_hh=hh_curr THEN
                 IF v_mi<mi_curr THEN
                   
                    v_flag:=1;
                    v_id:=v_job.id;
                   
                 END IF;
              END IF;
           END IF;
           -------------------------------共用部分----------------------------
           
           
        --2.如果是每星期则计算 星期 时 分
        ELSIF v_type='WEEK' THEN
           IF v_dd=ww_curr THEN
               -----------------------------------------------------------
               IF v_job.this_date<>v_curr THEN
                  IF v_hh<hh_curr THEN
                     
                     v_flag:=1;
                     v_id:=v_job.id;
                     
                  ELSIF v_hh=hh_curr THEN
                     IF v_mi<mi_curr THEN
                       
                        v_flag:=1;
                        v_id:=v_job.id;
                       
                     END IF;
                  END IF;
               END IF;
               -----------------------------------------------------------
           END IF;
       
       
        --3.如果是每月则计算 日 时 分
        ELSIF v_type='MONTH' THEN
           IF v_dd=dd_curr THEN
               -----------------------------------------------------------
               IF v_job.this_date<>v_curr THEN
                  IF v_hh<hh_curr THEN
                     
                     v_flag:=1;
                     v_id:=v_job.id;
                     
                  ELSIF v_hh=hh_curr THEN
                     IF v_mi<mi_curr THEN
                       
                        v_flag:=1;
                        v_id:=v_job.id;
                       
                     END IF;
                  END IF;
               END IF;
               -----------------------------------------------------------
           END IF;
           
        END IF;
       
        --DBMS_OUTPUT.put_line(v_type||' v_id : '||v_id);
       
        IF nvl(v_id,0)>0 THEN
           
           v_flag:=1;
           
           --dbms_output.put_line('v_var.count 数量:'||v_var.count);
           
           FOR i IN 1..v_var.count LOOP
                IF v_var(i) IS NOT NULL AND v_var(i)='CRM_任务运行监控('||v_id||');' THEN
                   v_flag:=0;
                END IF;
           END LOOP;
           
           IF v_flag=1 THEN
           
               v_var(v_index):='CRM_任务运行监控('||v_id||');';
               v_pros:=v_pros||v_var(v_index);
               
               sys.DBMS_JOB.submit (
                        job => x,
                        what => v_var(v_index),
                        next_date => SYSDATE,
                        interval => NULL,
                        --TO_DATE('30-04-2014 00:00:00', 'dd-mm-yyyy hh24:mi:ss')
                        --interval => 'trunc(sysdate+1)',
                        no_parse => FALSE);
                       
                       
                     SYS.DBMS_OUTPUT.put_line ('Job Number is: ' || TO_CHAR (x));
                     COMMIT;
                     
                     INSERT INTO t_proc_errlog_c (treatdate,proc_name,job_name)
                             VALUES (SYSDATE,'创建新job'||x,v_var(v_index));
                     COMMIT;
   
             
           ELSE
               v_var(v_index):='0';
           END IF;
           v_index:=v_index+1;
         
        END IF;
       
  END LOOP;
 
  IF c_jobs%ISOPEN THEN
     CLOSE c_jobs;
  END IF;
  --dbms_output.put_line(v_pros);
 
 
 
   --dbms_job.run(x);
 
 
  --最后运行调用 (停用=下面IF段执行不起作用 v_flag:=0)
  v_flag:=0;
  IF v_flag=1 THEN
 
 
     INSERT INTO ls_rpt_job_do_log VALUES
        (' id '||v_job.id||' name '||v_job.name,SYSDATE);
       
        INSERT INTO ls_rpt_job_do_log VALUES
        ('v_type : '||v_type||' v_str : '||v_str||' v_dd : '||v_dd||' v_hh : '||v_hh||' v_mi : '||v_mi,SYSDATE);
        COMMIT;
       
       
     UPDATE ls_rpt_job SET status=1 WHERE id=v_id;
     COMMIT;
     
     --DBMS_OUTPUT.put_line(' 执行调用 : '||v_id);
     INSERT INTO ls_rpt_job_do_log VALUES ('CRM_任务运行监控_定时并行'||v_id,SYSDATE);
     INSERT INTO t_proc_errlog_c (treatdate,proc_name,job_name)
         VALUES (SYSDATE,'CRM_任务运行监控_定时并行',to_char(v_id));
     COMMIT;
     
     --CRM_任务运行监控(v_id);
     
     
  END IF;
 
 
  --删除半年之前日志记录
  DELETE FROM t_proc_errlog_c
  WHERE to_char(treatdate,'yyyymm')<=to_char(add_months(SYSDATE,-1),'yyyymm');
  COMMIT;
 
EXCEPTION WHEN OTHERS THEN
V_SQLERRM := TO_CHAR(SQLCODE)||SUBSTRB(SQLERRM,1,400);
--加入错误日志
INSERT INTO T_PROC_ERRLOG_B(TREATDATE,PROC_NAME,ERRMSG,JOB_ID)
       VALUES
            (SYSDATE,'CRM_任务运行监控_定时并行',V_SQLERRM,v_id);
COMMIT;
END CRM_任务运行监控_定时并行;
```
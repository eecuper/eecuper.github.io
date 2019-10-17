---
layout: post
title: ORACLE 运行监控(3)任务监控存储过程
category: 技术
tags: orale
keywords: oracle , job
---

```
--JOB可选参数配置
create table LS_RPT_JOB_PARA
(
  PARA_TYPE VARCHAR2(20) default 'date_time',
  PARA_CODE VARCHAR2(100),
  PARA_VAL  VARCHAR2(100),
  STATUS    NUMBER default 1
)


JOB数据表结构
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







运行日志表结构
create table LS_RPT_JOB_LOG
(
  ID          NUMBER,
  JOB_ID      NUMBER,
  SQL         CLOB,
  CREATE_DATE DATE default sysdate,
  ERROR       VARCHAR2(4000),
  REMAR       VARCHAR2(4000)
)






CREATE OR REPLACE PROCEDURE CRM_任务运行监控(ID IN STRING) IS
V_SQLERRM         VARCHAR2(3000);

V_ID              NUMBER;                --任务ID
V_FLAG            NUMBER;                -- 判断任务是否存在

V_SQL             CLOB;                  --任务SQL  
V_LIST            T_RET_TABLE;           --SQL处理 ';' 分隔处理保存集合

V_LIST_SQL        CLOB;                  --递归集合语句   
V_TABLE_NAME      VARCHAR2(100);         --处理表名
V_TABLE_USER      VARCHAR2(100);         --表所属用户名

I                 NUMBER;                --递归行标示       
BEGIN
  
    V_ID:=TO_NUMBER(ID);
    
    --不存在调用的情况
    IF f_is_null(ID)=1 THEN 
       INSERT INTO T_PROC_ERRLOG_B(TREATDATE,ERRMSG,JOB_ID)VALUES(SYSDATE,'找不到指定运行任务',0);
        --DBMS_OUTPUT.PUT_LINE('00000000000000');
       COMMIT;
       
    --存在调用参数情况
    ELSE
    
       --1.清除历史运行日志
       DELETE FROM LS_RPT_JOB_LOG WHERE JOB_ID=V_ID;
       
       --2.设置JOB为运行状态
       UPDATE LS_RPT_JOB SET STATUS=1,MSG=NULL,this_date=SYSDATE WHERE ID=V_ID;
       COMMIT;
                   
       --3.调用任务是否存在赋值 , 调用任务SQL赋值
       SELECT NVL(COUNT(*),0) INTO V_FLAG FROM LS_RPT_JOB WHERE ID=V_ID;
       SELECT SQL INTO V_SQL FROM LS_RPT_JOB WHERE ID=V_ID;
        
       
       --存在运行任务
       IF V_FLAG>0 AND clob_is_null(V_SQL)=0 THEN 
       
          --1.1查询任务中SQL 按 ';' 分割保存结果到集合 T_RET_TABLE : V_LIST 
          SELECT f_split_clob(V_SQL,';') INTO V_LIST FROM LS_RPT_JOB WHERE ID=V_ID;
          
          I:=1;
          
          --1.2递归SQL集合逐句执行
          LOOP
           
              --1.2.1集合递归推出判断
              EXIT WHEN I>V_LIST.COUNT ;
              
              --1.2.2递归赋值 (替换语句中的换行符为空格)
              V_LIST_SQL:=REPLACE(UPPER(V_LIST(I)),CHR(32),' ');
              
              --系统不允许使用 /****/格式注释 尚未处理
              --取注释符号之前的语句
              --IF INSTR(F_TRIM_clob(V_LIST_SQL),'--')>0 THEN 
                 --V_LIST_SQL:=SUBSTR(V_LIST_SQL,0,INSTR(V_LIST_SQL,'--')-1);
              --END IF;
              
              --DBMS_OUTPUT.PUT_LINE(' 是否删除 ：');
              --DBMS_OUTPUT.PUT_LINE(INSTR(F_TRIM_clob(V_LIST_SQL),'DROP'));
              
              --DBMS_OUTPUT.PUT_LINE('--------------------------------------------------------------------------');
              --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,0,100));
              --DBMS_OUTPUT.PUT_LINE('--------------------------------------------------------------------------');
              
              --1.3删除操作   (2013-02-22: 目前仅处理了 table 类型删除操作 index view sequence 等日常使用类型将继续添加)
              IF INSTR(F_TRIM_clob(V_LIST_SQL),'DROP')>0 THEN 
              
                  V_TABLE_NAME:=SUBSTR(V_LIST_SQL,INSTR(V_LIST_SQL,'TABLE')+5,LENGTH(V_LIST_SQL));
                  
                  --1.3.1 删除表名是否存在所属用户名情况
                  IF INSTR(F_TRIM_STR(V_TABLE_NAME),'.')>=0 THEN 
                     
                     V_TABLE_USER:=SUBSTR(V_TABLE_NAME,0,INSTR(V_TABLE_NAME,'.')-1);
                     V_TABLE_NAME:=SUBSTR(V_TABLE_NAME,INSTR(V_TABLE_NAME,'.')+1,LENGTH(V_TABLE_NAME));
                     
                     
                        --DBMS_OUTPUT.PUT_LINE('用户名：'||V_TABLE_USER||' 表名：'||V_TABLE_NAME||' '||clob_is_null(V_TABLE_USER));
                        
                        --不存在用户名
                        IF f_is_null(V_TABLE_USER)=1 THEN 
                           --DBMS_OUTPUT.PUT_LINE('是否存在表：'||V_TABLE_NAME||'  1: '||V_TABLE_USER);
                           IF F_EXISTS_TABLE(V_TABLE_NAME)=1 THEN 
                               --DBMS_OUTPUT.PUT_LINE('DROP TABLE XXXX ');  
                               --DBMS_OUTPUT.PUT_LINE(V_LIST_SQL);
                               IF clob_is_null(V_LIST_SQL)=0 THEN 
                                  
                                  --计入日志
                                  INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                                  COMMIT;
                                  --执行删除表操作
                                  EXECUTE IMMEDIATE V_LIST_SQL;
                               END IF;
                               
                            END IF;
                            
                        --存在用户名 
                        ELSE
                           -- --DBMS_OUTPUT.PUT_LINE('是否存在表:'||REPLACE(V_TABLE_NAME,' ','')||'  2:'||REPLACE(V_TABLE_USER,' ',''));
                           -- --DBMS_OUTPUT.PUT_LINE(F_EXISTS_TABLE_U(REPLACE(V_TABLE_NAME,' ',''),REPLACE(V_TABLE_USER,' ','')));
                            IF F_EXISTS_TABLE_U(REPLACE(V_TABLE_NAME,' ',''),REPLACE(V_TABLE_USER,' ',''))=1 THEN 
                               IF clob_is_null(V_LIST_SQL)=0 THEN 
                                  --计入日志
                                  INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                                  COMMIT;
                                  --执行删除表操作
                                  EXECUTE IMMEDIATE V_LIST_SQL;
                               END IF;
                               
                            END IF;
                            
                        END IF;
                        
                   END IF;
                   
              --1.4 CREATE UPDATE INSERT DELETE TRUNCATE ALTER 等数据库DDL 操作
              ELSE
              
                  --DBMS_OUTPUT.PUT_LINE('INSERT 、UPDATE、 DELETE XXXX'); 
                  --DBMS_OUTPUT.PUT_LINE('*******************************************************************');
                  --DBMS_OUTPUT.PUT_LINE(V_LIST_SQL);
                  --DBMS_OUTPUT.PUT_LINE('*******************************************************************');
                  
                  IF clob_is_null(V_LIST_SQL)=0 THEN 
                     
                      --1.4.1 CREATE 创建情况
                     IF INSTR(LTRIM(V_LIST_SQL),'CREATE ')>0 THEN 
                         --DBMS_OUTPUT.PUT_LINE('CREATE--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'CREATE'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('CREATE--------------');
                                         
                         --DBMS_OUTPUT.PUT_LINE('CREATE11111111111111111');         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'CREATE'),LENGTH(V_LIST_SQL));
                         
                         --计入日志
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         --DBMS_OUTPUT.PUT_LINE('CREATE2222222222222222');
                         --执行创建操作
                         execute immediate V_LIST_SQL;
                         
                         --DBMS_OUTPUT.PUT_LINE('CREATE333333333333333');
                      --1.4.2 INSERT 插入情况
                      ELSIF INSTR(LTRIM(V_LIST_SQL),'INSERT ')>0 THEN 
                         --DBMS_OUTPUT.PUT_LINE('INSERT--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'INSERT'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('INSERT--------------');
                         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'INSERT'),LENGTH(V_LIST_SQL));
                         
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         execute immediate V_LIST_SQL;
                         
                      --1.4.3 UPDATE 更新情况
                      ELSIF INSTR(LTRIM(V_LIST_SQL),'UPDATE ')>0 THEN
                         --DBMS_OUTPUT.PUT_LINE('UPDATE--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'UPDATE'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('UPDATE--------------');
                         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'UPDATE'),LENGTH(V_LIST_SQL));
                         
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         execute immediate V_LIST_SQL;
                         
                      --1.4.4 DELETE 删除情况
                      ELSIF INSTR(LTRIM(V_LIST_SQL),'DELETE ')>0 THEN
                         --DBMS_OUTPUT.PUT_LINE('DELETE--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'DELETE'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('DELETE--------------');
                         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'DELETE'),LENGTH(V_LIST_SQL));
                         
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         execute immediate V_LIST_SQL;
                         
                      --1.4.5 TRUNCATE 清空情况
                      ELSIF INSTR(LTRIM(V_LIST_SQL),'TRUNCATE ')>0 THEN
                         --DBMS_OUTPUT.PUT_LINE('TRUNCATE--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'TRUNCATE'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('TRUNCATE--------------');
                         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'TRUNCATE'),LENGTH(V_LIST_SQL));
                         
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         execute immediate V_LIST_SQL;
                         
                      --1.4.6 ALTER 定义更改情况
                      ELSIF INSTR(LTRIM(V_LIST_SQL),'ALTER ')>0 THEN
                         --DBMS_OUTPUT.PUT_LINE('ALTER--------------');
                         --DBMS_OUTPUT.PUT_LINE(SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'ALTER'),LENGTH(V_LIST_SQL)));
                         --DBMS_OUTPUT.PUT_LINE('ALTER--------------');
                         
                         V_LIST_SQL:=SUBSTR(V_LIST_SQL,INSTR(LTRIM(V_LIST_SQL),'ALTER'),LENGTH(V_LIST_SQL));
                         
                         INSERT INTO LS_RPT_JOB_LOG (ID,JOB_ID,SQL,CREATE_DATE) VALUES (JOB_LOG.NEXTVAL,V_ID,V_LIST_SQL,SYSDATE);
                         COMMIT;
                         execute immediate V_LIST_SQL;
                         
                      END IF;
                      
                   END IF;
                  
              END IF;       
              
              --递归标示+1
              I:=I+1;
              
          END LOOP; 
          
          
       --不存在调用任务
       ELSE
          INSERT INTO T_PROC_ERRLOG_B(TREATDATE,ERRMSG,JOB_ID)VALUES(SYSDATE,'找不到指定运行任务,或执行脚本',0);
          COMMIT;
          --DBMS_OUTPUT.PUT_LINE('00000000111111111100000000');
       END IF;
       
       
       --4任务成功运行完成
       UPDATE LS_RPT_JOB SET STATUS='0' WHERE ID=V_ID;
       COMMIT;
       
    END IF;









EXCEPTION WHEN OTHERS THEN

--运行错误
UPDATE LS_RPT_JOB SET STATUS='-1',failures=nvl(failures,0)+1 WHERE ID=V_ID;
COMMIT;
--||' :　'||substr(dbms_utility.format_error_stack,1,200)    
V_SQLERRM := TO_CHAR(SQLCODE)||SUBSTRB(SQLERRM,1,400);

UPDATE LS_RPT_JOB SET MSG=V_SQLERRM WHERE ID=V_ID;
COMMIT;

--加入错误日志
INSERT INTO T_PROC_ERRLOG_B(TREATDATE,ERRMSG,JOB_ID)VALUES(SYSDATE,V_SQLERRM,ID);
COMMIT;

END CRM_任务运行监控;
```
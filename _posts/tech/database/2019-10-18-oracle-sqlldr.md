---
layout: post
title: oracle sqlldr 导入批量数据
categories: [资源 , 技术]
tags: [oracle , sqlldr]
keywords: [oracle , sqlldr]
---

![img](../../assets/img/oracle-11g.jpg)

## oracle 外部数据导入

1.直接建表然后 for update 后复制外部excel数据,然后解锁表找对应的数据列粘贴,加锁,提交即可

2.使用oracle sqlldr导入命令进行 txt 或 csv 格式数据文件导入

## sqlldr

### 命令环境安装

如果是很清楚oracle 独立功能安装方法的话直接到[官网](https://www.oracle.com/technetwork/cn/database/enterprise-edition/downloads/index.html)上下载数据库连接客户端即可,安装成功命令环境同主目录/bin一样.

### 命令语法示例

```
sqlldr 库用户/用户密码@库IP:1521/库名 control=控制文件 *.ctl路径 log=日志文件 *.log 路径

或(如果本地配置了 tns 别名)

sqlldr 库用户/用户密码@别名 control=控制文件 *.ctl路径 log=日志文件 *.log 路径

```

### 通用目录

```
D:.
│  input.ctl
│  input.log
│  导入.txt
├─cvsFiles
│      b1.csv
```


```
sqlldr mz_crm/pwdpwd@ls40 control=D:\input\input.ctl log=D:\input\input.log
```
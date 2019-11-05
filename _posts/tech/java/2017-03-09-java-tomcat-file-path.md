---
layout: post
title: tomcat 附件目录配置
category: 技术
tags: [java,tomcat]
keywords: java , tomcat , server.xml
---

host 结束标签之前添加 

```
<Context crossContext="true" docBase="e:/app" path="/app" reloadable="true"></Context>  
<Context crossContext="true" docBase="e:/app/file/ueditor" path="/ueditor" reloadable="true"></Context>             
<Context crossContext="true" docBase="e:/app/file/upload" path="/upload" reloadable="true"></Context>
```

程序上传文件使用/app 路径则直接映射保存到 e:/app 路径

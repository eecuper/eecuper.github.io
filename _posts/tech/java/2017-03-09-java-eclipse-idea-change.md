---
layout: post
title: eclipse 项目转 idea 项目遇到的问题
category: 技术
tags: [java,tomcat]
keywords: eclipse , idea 
---

### 转换心得

转换过程非常简单

转换之后的目录会自动生成 .idea , 项目名.iml 文件 类似使用eclipse同目录 .project , .myeclipse 等等项目描述文件

转换过程中主要还是需要确认jdk版本是否兼容 , 项目编译输出路径 , tomcat部署路径 , web.xml 配置文件有效性; 只要是之前使用myeclipse 插件能正常部署发布的项目然后确认以上几个配置的准确性基本能做到无障碍切换; 当然本人在转换的过程中还是遇到一些问题,与首次使用IDEA工具的熟练程度和配置项目的理解还是分不开的.所以web项目所依赖运行环境务必理解,即使使用其他开发工具均能游刃有余;

### 项目目录整理

保留 src , WebRoot 目录外其他所有eclipse 使用相关的文件和目录

### IDEA 导入

1.直接创建项目选址 import from existing source 然后其他默认下一步基本好了; 

2.点击右上角菜单 project structuer 菜单配置:

project_name : 项目名

SDK : 使用jdk 版本

project compiler output: 项目编译输出目录

modules菜单项目名称下添加 web 配置项然后分别配置 web.xml , webRoot , src 三个对应路径(在web插件已安装的情况基本会默认选中的)

libraries : 导入项目使用到的 jar 包或者maven 参考配置

facets: 选中已配置的 module 即可

artifacts: 配置web部署目标目录 , 这里可以直接配置指向到 apatch tomcat/webAPPs/ROOT 目录,则不需要添加项目名访问方式

### web容器配置

配置run/debug Configurations : 添加tomcat server 然后端口号 , 访问地址 , 使用jre 目录以及一些jvm 参数配置

### 遇到的异常处理

#### tomcat 使用 jre 环境路径配置问题

重选选择jre 为真实 jdk 安装目录路径  

Java 环境变量:         C:\Program Files\Java7\jre7

```
[2019-11-01 03:09:48,476] Artifact ROOT: Artifact is being deployed, please wait...
01-Nov-2019 15:09:48.561 严重 [RMI TCP Connection(3)-127.0.0.1] org.apache.catalina.core.ContainerBase.addChildInternal ContainerBase.addChild: start: 
    org.apache.catalina.LifecycleException: 初始化组件[StandardEngine[Catalina].StandardHost[localhost].StandardContext[]]失败。
...
Caused by: javax.xml.parsers.FactoryConfigurationError: Provider org.apache.xerces.jaxp.SAXParserFactoryImpl not found    
```


#### web.xml 文件错误标签配置或者无效配置去除

idea 针对web.xml 提示错误的标签配置,手动修改或者删除后重新部署发布后即可

org.xml.sax.SAXNotRecognizedException: http://apache.org/xml/features/allow-java-encodings

```
HTTP Status 500 – Internal Server Error
Type 异常报告

消息 AuthConfigFactory error: java.lang.reflect.InvocationTargetException

描述 服务器遇到一个意外的情况，阻止它完成请求。

Exception

java.lang.SecurityException: AuthConfigFactory error: java.lang.reflect.InvocationTargetException
    javax.security.auth.message.config.AuthConfigFactory.getFactory(AuthConfigFactory.java:87)
    org.apache.catalina.authenticator.AuthenticatorBase.findJaspicProvider(AuthenticatorBase.java:1286)
    org.apache.catalina.authenticator.AuthenticatorBase.getJaspicProvider(AuthenticatorBase.java:1276)
    org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:519)
    org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
    org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:678)
    org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
    org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:798)
    org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
    org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:810)
    org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1500)
    org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
    java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    java.lang.Thread.run(Unknown Source)
Root Cause

java.lang.reflect.InvocationTargetException
    sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
    sun.reflect.NativeConstructorAccessorImpl.newInstance(Unknown Source)
    sun.reflect.DelegatingConstructorAccessorImpl.newInstance(Unknown Source)
    java.lang.reflect.Constructor.newInstance(Unknown Source)
    javax.security.auth.message.config.AuthConfigFactory$1.run(AuthConfigFactory.java:78)
    javax.security.auth.message.config.AuthConfigFactory$1.run(AuthConfigFactory.java:68)
    java.security.AccessController.doPrivileged(Native Method)
    javax.security.auth.message.config.AuthConfigFactory.getFactory(AuthConfigFactory.java:67)
    org.apache.catalina.authenticator.AuthenticatorBase.findJaspicProvider(AuthenticatorBase.java:1286)
    org.apache.catalina.authenticator.AuthenticatorBase.getJaspicProvider(AuthenticatorBase.java:1276)
    org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:519)
    org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
    org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:678)
    org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
    org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:798)
    org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
    org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:810)
    org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1500)
    org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
    java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    java.lang.Thread.run(Unknown Source)
Root Cause

java.lang.SecurityException: org.xml.sax.SAXNotRecognizedException: http://apache.org/xml/features/allow-java-encodings
    org.apache.catalina.authenticator.jaspic.PersistentProviderRegistrations.loadProviders(PersistentProviderRegistrations.java:65)
    org.apache.catalina.authenticator.jaspic.AuthConfigFactoryImpl.loadPersistentRegistrations(AuthConfigFactoryImpl.java:347)
    org.apache.catalina.authenticator.jaspic.AuthConfigFactoryImpl.<init>(AuthConfigFactoryImpl.java:69)
    sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
    sun.reflect.NativeConstructorAccessorImpl.newInstance(Unknown Source)
    sun.reflect.DelegatingConstructorAccessorImpl.newInstance(Unknown Source)
    java.lang.reflect.Constructor.newInstance(Unknown Source)
    javax.security.auth.message.config.AuthConfigFactory$1.run(AuthConfigFactory.java:78)
    javax.security.auth.message.config.AuthConfigFactory$1.run(AuthConfigFactory.java:68)
    java.security.AccessController.doPrivileged(Native Method)
    javax.security.auth.message.config.AuthConfigFactory.getFactory(AuthConfigFactory.java:67)
    org.apache.catalina.authenticator.AuthenticatorBase.findJaspicProvider(AuthenticatorBase.java:1286)
    org.apache.catalina.authenticator.AuthenticatorBase.getJaspicProvider(AuthenticatorBase.java:1276)
    org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:519)
    org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
    org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:678)
    org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
    org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:798)
    org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
    org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:810)
    org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1500)
    org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
    java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    java.lang.Thread.run(Unknown Source)
Root Cause

org.xml.sax.SAXNotRecognizedException: http://apache.org/xml/features/allow-java-encodings
    gnu.xml.aelfred2.JAXPFactory.setFeature(JAXPFactory.java:102)
    org.apache.tomcat.util.digester.Digester.setFeature(Digester.java:532)
    org.apache.catalina.authenticator.jaspic.PersistentProviderRegistrations.loadProviders(PersistentProviderRegistrations.java:61)
    org.apache.catalina.authenticator.jaspic.AuthConfigFactoryImpl.loadPersistentRegistrations(AuthConfigFactoryImpl.java:347)
    org.apache.catalina.authenticator.jaspic.AuthConfigFactoryImpl.<init>(AuthConfigFactoryImpl.java:69)
```    


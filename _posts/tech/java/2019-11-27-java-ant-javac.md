---
layout: post
title: ant工具java命令
category: 工具
tags: [java,ant]
keywords: [java,ant]
---

文章内容引自:https://www.iteye.com/blog/286-1910617

### <javac>
编译java源文件成class文件。
```xml
<javac srcdir="${src}" destdir="${dest}" encoding="utf-8" bootclasspathref="project.classpath" includejavaruntime="true" includeantruntime="false" optimize="true"  source="1.6" target="1.6"/>  
 ```
- (1). srcdir：java源文件的文件夹，也可以在内部嵌套<src>标签
- (2). destdir：用于存放编译后class文件的文件夹，默认是当前文件夹。
- (3). includes：必须包括的文件模式的列表，以逗号或空格分隔。如果忽略，将包括所有文件。
- (4). includesfile：指定一个包含很多include的文件，同样可以用通配符来指定。
- (5). excludes：必须排除的文件模式的列表，以逗号或空格分隔。如果忽略，将不排除任何文件。
- (6). excludesfile：指定一个包含很多exclude的文件，同样可以用通配符来指定。
- (7). classpath: 编译所使用的classpath
- (8). sourcepath: 指定源资源文件夹。默认指向srcdir
- (9). bootclasspath: 启动时设置classpath
- (10). classpathref： 引用其他classpath
- (11). sourcepathref：引用其他源文件目录
- (12). bootclasspathref： 启动时引用其他classpath设置
- (13). extdirs：扩展文件的路径。
- (14). encoding：编码格式
- (15). nowarn：忽略警告信息
- (16). debug： 是否产生调试信息，默认off。
- (17). debuglevel：debug级别
- (18). optimize： 指出是否应该用优化方式编译源代码，默认为 off。
- (19). deprecation：是否给出不建议使用的API，默认值false。
- (20). target： 根据特定的vm版本生成class文件
- (21). verbose：要求编译器输出详细信息，默认no。
- (22). depend： 当运行这个任务时，首先按照顺序依次执行完依赖的任务，如果出错将停止执行。
- (23. includeAntRuntime：指出是否应在类路径中包括 Ant 运行时程序库，默认为 yes。
- (24). includeJavaRuntime： 指出是否应在类路径中包括来自执行 VM 的默认运行时程序库，默认为 no。
- (25). fork：是否执行外部的javac，默认no。
- (26). executable：当fork="yes"时，所执行的javac的完整路径。默认的是当前跑ant的java版本，fork="no"就忽略
- (27). memoryInitialSize：当是外部javac的时候，获得基本vm的初始内存大小。否则忽略。
- (28). memoryMaximumSize： 同上只不过是获得最大的内存大小。
- (29). failonerror： Ant 任务在出现编译错误的情况下是否继续执行，默认值为 True。
- (30). errorProperty：当编译失败的时候要设置的属性。
- (31). source：命令行-source的开关。 假如设置为1.4,将激活断言。默认是1.3。默认不使用-source。
- (32). compiler：设置用来选择编译器，如果不设置，将使用默认的编译器。
- (33). listfiles：是否显示被编译文件列表，默认是no。
- (34). tempdir：指定存放临时文件的地方，这个只在设置了fork和命令行超过参数长度超过4k的时候使用，默认为java.io.tmpdir
- (35). updatedProperty：更新property属性
- (36). includeDestClasses：是否包含目标class文件夹包含到classpath。默认是true。意思是之前生成的class文件自动加到classpath。

下面是一些实例：

1). 将${src}下的java文件编译到${dest}中，classpath中包括log4j.jar包，并显示调试信息，source是1.6
```xml
<javac srcdir="${src}" destdir="${dest}" classpath="log4j.jar" debug="on" source="1.6" target="1.6" />  
```

2). 将${src}下的java文件编译到${dest}中，使用外部的javac。source的等级是1.6，并且class文件是根据jdk1.6版本生成
```xml
<javac srcdir="${src}" destdir="${dest}" fork="true" source="1.6" target="1.6" />  
```

3). 此例同上，只是使用fork指定executable的javac名称为 java$javac.exe
```xml
<javac srcdir="${src}" destdir="${dest}" fork="java$$javac.exe" source="1.6" target="1.6" />  
```

4). 将${src}下的java文件编译到${dest}中,且只包含package下的java文件，并排除test下的所有文件
```xml
<javac srcdir="${src}" destdir="${dest}" includes="package/**" excludes="package/test/**" classpath="log4j.jar"  />  
```

5). 指定javac程序编译源文件，javac文件位置：/usr/local/java/jdk/bin/javac
```xml
<javac srcdir="${src}" destdir="${dest}" fork="true" executable="/usr/local/java/jdk/bin/javac" compiler="javac1.1" />  
```

6). 使用指定的CompilerAdapter
```xml
<javac srcdir="${src}" destdir="${dest}" compiler="com.myproject.MyAdapter"/>  
```
或 
```xml 
<componentdef classname="com.myproject.MyAdapter" name="myadapter" />  
<javac srcdir="${src}" destdir="${dest}">  
<myadapter/>  
</javac>  
```

### <java>
用来运行编译好的class文件，命令比较简单，结合javac的属性解释一看就懂了。
```xml
<java timeout="30" args="1,2,3" classname="com.test.ant.HelloAnt" classpath="${classPath}" />  
```
java既可以运行class文件又可以运行jar文件，只需要做相应配置即可：
```xml
<java classname="com.ant.hello.HelloAnt" classpath="${hello_jar}" />  

<java classname="test.Main">  
<arg value="-h" />  
</java>  
```

### <jar>
(1)打包成jar文件，jar命令是我们经常要使用的，我们会将项目打包成相应的jar包分发到各系统上。
```xml
<jar jarfile="${hello_jar}" basedir="${dest}" />  
<jar destfile="${dist}/hello.jar" basedir="${dest}" includes="com/test/ant/**" excludes="**/*_Temp.class" />  
```
(2)打包成可运行jar文件，即指定该jar文件的main函数。
```xml
<jar destfile="${dist}/hello.jar" basedir="${dest}" manifest="${dir}/MANIFEST.MF"/>  
<jar destfile="${dist}/hello.jar" filesetmanifest="mergewithoutmain">  
<manifest>  
<attribute name="Built-By" value="${user.name}"/>  
<attribute name="Main-Class" value="com.ant.hello.HelloAnt"/>  
<attribute name="Class-Path" value="."/>  
</manifest>  
<fileset dir="${dest}"/>  
</jar>  
```
方式1中需要在dir目录下新建一个MANIFEST.MF文件，在默认打包时未设置manifest属性的话系统会自动添加。
方式2中将属性直接赋在attribute 中，这样编译时会自动生成指定内容的MANIFEST.MF文件。

```xml
Manifest-Version: 1.0   
Class-Path: lib/slf4j-log4j.jar lib/slf4j-api-1.5.10.jar lib/log4j.jar   
Main-Class: com.ant.hello.HelloAnt  
```

### <war>
打成war包：
```xml
<war compress="true" destfile="ROOT.war" webxml="${web}/WEB-INF/web.xml">  
<zipfileset dir="${web}">  
<include name="**/*.jsp" />  
<include name="**/*.html" />  
<include name="**/*.css" />  
<include name="**/*.js" />  
<include name="**/*.properties" />  
<include name="**/*.xml" />  
<include name="**/*.xsl" />  
<include name="**/*.gif" />  
<include name="**/*.png" />  
<include name="**/*.jpg" />  
<include name="**/*.jpeg" />  
<include name="**/*.wbmp" />  
<include name="**/*.swf" />  
<include name="WEB-INF/*.tld" />  
</zipfileset>  
<lib dir="${dest}">  
<include name="lib1.jar" />  
</lib>  
<lib dir="${dest}">  
<include name="lib2.jar" />  
</lib>  
</war>  
```
### <javadoc>
为我们的代码构建JavaDoc文档。
```xml
<javadoc charset="" destdir="" stylesheetfile="" source="" >  
<doclet>  
<param/>  
</doclet>  
</javadoc>  
```
6.发布程序
其实是一系列的动作：建立目录-->编译-->打包-->移动-->启动服务器。具体业务流程还需要具体分析，只要Ant的各种命令参数运用的合理，我们可以轻松构建任何应用
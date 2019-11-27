---
layout: post
title: ant工具使用
category: 技术
tags: [java,ant]
keywords: [java,ant]
---

### 使用心得
日常开发常使用eclipse+myeclipse 或者 idea 工具,因为这些工具都是直接封装好的编译和部署到web服务器的过程;

然而初学或者需要在编译和部署的流程中插播其他拓展操作(比如复制或删除一些额外的文件到项目目录)的时候,直接使用IDE工具是不提供灵活的配置;这个时候使用ant可以弥补工具的不足;

对于开发人员所需要面对的各种PC和服务环境各部相同;

同时各个终端不一定拥有相同的和熟悉的IDE工具版本;个人更多的时候喜欢使用sublime和vscode文本编辑器进行程序直接编写(目前使用PHP语言日常sublime已经足够);

常遇到工作终端配置不够所以安装大型IDE比较消耗资源(如myeclipse高版本非常消耗内存);在非java IDE的时候使用ant开发的可以非常了解各个所以环境配置和编译部署的各个步骤的来龙去脉.而且各个环节可以自主控制;

ant 通用的功能:copy(文件/目录复制) , delete(文件/目录删除) , javac(java编译) , java(java运行) , jar(生成jar包) 等等

### ant执行异常

```java
ant XML parser factory has not been configured correctly: Provider org.apach
```

处理: 由于ant缺少xml文件解析工具包, 解决办法下载复制一个xercesImpl到jdk对应jre/lib下即可

### 使用简介
以下内容引用自: [https://www.cnblogs.com/wufengxyz/archive/2011/11/24/2261797.html](https://www.cnblogs.com/wufengxyz/archive/2011/11/24/2261797.html)

#### 1,什么是ant
ant是构建工具

#### 2,什么是构建
概念到处可查到，形象来说，你要把代码从某个地方拿来，编译，再拷贝到某个地方去等等操作，当然不仅与此，但是主要用来干这个

#### 3,ant的好处
跨平台   --因为ant是使用java实现的，所以它跨平台

使用简单--与ant的兄弟make比起来

语法清晰--同样是和make相比

功能强大--ant能做的事情很多，可能你用了很久，你仍然不知道它能有多少功能。当你自己开发一些ant插件的时候，你会发现它更多的功能。

#### 4,ant的兄弟make
ant做的很多事情，大部分是曾经有一个叫make的所做的，不过对象不同，make更多应用于c/c++ ,ant更多应用于Java。当然这不是一定的，但大部分人如此。


### ant使用
#### 一,构建ant环境
要使用ant首先要构建一个ant环境，步骤很简单：

- 1),安装jdk，设置JAVA_HOME ,PATH ,CLASS_PATH(这些应该是看这篇文章的人应该知道的)
- 2),下载ant 地址http://www.apache.org/找一个你喜欢的版本，或者干脆最新的版本
- 3),解压ant 你得到的是一个压缩包，解压缩它，并把它放在一个尽量简单的目录，例如D:\ant-1.6虽然你不一 定要这么做，但这么做是有好处的。
- 4),设置ANT_HOME, PATH中添加ANT_HOME目录下的bin目录(我设置的：ANT_HOME:D:\apache-ant-1.8.2,PATH:%ANT_HOME%\bin)
- 5),测试一下你的设置,开始-->运行-->cmd进入命令行-->键入 ant 回车,如果看到
Buildfile: build.xml does not exist!
Build failed
那么恭喜你你已经完成ant的设置

#### 二,体验ant
就像每个语言都有HelloWorld一样，一个最简单的应用能让人感受一下Ant

1,首先你要知道你要干什么，我现在想做的事情是：

编写一些程序

编译它们

把它打包成jar包

把他们放在应该放置的地方

运行它们

这里为了简单起见只写一个程序，就是HelloWorld.java程序代码如下：

```java
package test.ant;
public class HelloWorld{
public static void main(String[] args){
   System.out.println("Hello world1");
}
};
```

2，为了达到上边的目的，你可以手动的用javac 、copy 、jar、java来完成，但是考虑一下如果你有成百上千个类，在多次调试，部署的时候，一次次的javac 、copy、jar、
java那将是一份辛苦的工作。现在看看ant怎么优雅的完成它们。

要运行ant需要有一个build.xml虽然不一定要叫这个名字，但是建议你这么做

下边就是一个完整的build.xml，然后我们来详细的解释每一句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<project name="HelloWorld" default="run" basedir=".">
<property name="src" value="src"/>
<property name="dest" value="classes"/>
<property name="hello_jar" value="hello1.jar"/>
<target name="init">
   <mkdir dir="${dest}"/>
</target>
<target name="compile" depends="init">
   <javac srcdir="${src}" destdir="${dest}"/>
</target>
<target name="build" depends="compile">
   <jar jarfile="${hello_jar}" basedir="${dest}"/>
</target>
<target name="run" depends="build">
   <java classname="test.ant.HelloWorld" classpath="${hello_jar}"/>
</target>
<target name="clean">
   <delete dir="${dest}" />
   <delete file="${hello_jar}" />
</target>
<target name="rerun" depends="clean,run">
   <ant target="clean" />
   <ant target="run" />
</target>
</project>
```

解释：

```xml 
<?xml version="1.0" encoding="UTF-8" ?>
```
build.xml中的第一句话，没有实际的意义
```xml 
<project name="HelloWorld" default="run" basedir=".">
</project>
```
ant的所有内容必须包含在这个里边，name是你给它取的名字，basedir故名思意就是工作的根目录 .代表当前目录。default代表默认要做的事情。
```xml 
<property name="src" value="src"/>
```
类似程序中的变量，为什么这么做想一下变量的作用
```xml 
<target name="compile" depends="init">
   <javac srcdir="${src}" destdir="${dest}"/>
</target>
```
把你想做的每一件事情写成一个target ，它有一个名字，depends是它所依赖的target，在执行这个target 例如这里的compile之前ant会先检查init是否曾经被执行过，如果执行
过则直接直接执行compile，如果没有则会先执行它依赖的target例如这里的init，然后在执行这个target
如我们的计划

编译：
```xml 
<target name="compile" depends="init">
<javac srcdir="${src}" destdir="${dest}"/>
</target>
```
做jar包:
```xml 
<target name="build" depends="compile">
<jar jarfile="${hello_jar}" basedir="${dest}"/>
</target>
```
运行：
```xml 
<target name="run" depends="build">
<java classname="test.ant.HelloWorld" classpath="${hello_jar}"/>
</target>
```
为了不用拷贝，我们可以在最开始定义好目标文件夹，这样ant直接把结果就放在目标文件夹中了

新建文件夹:
```xml 
<target name="init">
<mkdir dir="${dest}"/>
</target>
```
为了更多一点的功能体现，又加入了两个target

删除生成的文件
```xml 
<target name="clean">
<delete dir="${dest}" />
<delete file="${hello_jar}" />
</target>
```
再次运行，这里显示了如何在一个target里边调用其他的target
```xml 
<target name="rerun" depends="clean,run">
<ant target="clean" />
<ant target="run" />
</target>
```
好了，解释完成了，下边检验一下你的ant吧

新建一个src的文件夹，然后把HelloWorld.java按照包目录放进去

做好build.xml文件,最好将这些放到一个文件夹中,在cmd中进入该文件夹,

在命令行下键入ant ,你会发现一个个任务都完成了。每次更改完代码只需要再次键入ant

有的时候我们可能并不想运行程序，只想执行这些步骤中的某一两个步骤，例如我只想重新部署而不想运行，键入

```java
ant build
```

ant中的每一个任务都可以这样调用ant + target name
好了，这样一个简单的ant任务完成了。


#### 什么时候使用ant
也许你听到别人说起ant，一时冲动准备学习一下ant，当你看完了上边的第一个实例，也许你感觉ant真好，也许你感觉ant不过如此，得出这些结论都不能说错，虽然ant很好用，
但并不是在任何情况下都是最好的选择，例如windows上有更多更简单，更容易使用的工具，比如eclipse+myeclipse eclipse+wtp等等，无论是编译，部署，运行使用起来比ant更
容易，方便但有些情况则是ant发挥的好地方：

#### 服务器上部署的时候
当你的程序开发完成，部署人员要部署在服务器上的时候，总不能因为因为安装一个程序就配置一个eclipse+myeclipse吧，ant在这个时候是个很好的选择，因为它小巧，容易配
置，你带着你写好的build.xml到任何一台服务器上，只需要做简单的修改（一些设定，例如目录），然后一两个命令完成，这难道不是一件美好的事情吗。

#### linux操作系统
很多时候是这样的，程序开发是在windows下，但是程序要在linux或者unix上运行，在linux或者
在unix(特别是unix上)部署是个麻烦的事情，这个时候ant的特点又出来了，因为ant是跨平台的，你在build.xml可以在大多数操作系统上使用，基本不需要修改。

#### 当服务器维护者不懂编程的时候
很多人都有过这样的经历，使用你们程序的人，并不懂得写程序。你得程序因为版本更新，因为修正bug需要一次又一次得重新部署。这个时候你会发现教一个人是如此得困难。但
是有ant后，你只需要告诉他，输入ant xxx等一两个命令，一切ok.

以上是我遇到得一些情况。

看完以上得情况，好好考虑一下，你是否需要使用ant，如果是继续。

进一步学习一个稍微复杂一点点的ant

在实际的工作过程中可能会出现以下一些情况，一个项目分成很多个模块，每个小组或者部门负责一个模块，为了测试，他们自己写了一个build.xml,而你负责把这些模块组合到
一起使用，写一个build.xml

这个时候你有两种选择：

1,自己重新写一个build.xml ，这将是一个麻烦的事情

2,尽量利用他们已经写好的build.xml，减少自己的工作
举个例子：

假设你下边有三个小组，每个小组负责一个部分，他们分别有一个src 和一个写好的build.xml

这个时候你拿到他们的src，你需要做的是建立三个文件夹src1 ,src2, src3分别把他们的src和build.xml放进去，然后写一个build.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<project name="main" default="build" basedir=".">
<property name="bin" value="${basedir}\bin" />
<property name="src1" value="${basedir}\src1" />
<property name="src2" value="${basedir}\src2" />
<property name="src3" value="${basedir}\src3" />
<target name="init">
   <mkdir dir="${bin}" />
</target>
<target name="run">
   <ant dir="${src1}" target="run" />
   <ant dir="${src2}" target="run" />
   <ant dir="${src3}" target="run" />
</target>
<target name="clean">
   <ant dir="${src1}" target="clean" />
   <ant dir="${src2}" target="clean" />
   <ant dir="${src3}" target="clean" />
</target>
<target name="build" depends="init,call">
   <copy todir="${bin}">
    <fileset dir="${src1}">
     <include name="*.jar" />
    </fileset>
    <fileset dir="${src2}">
     <include name="*.jar" />
    </fileset>
    <fileset dir="${src3}">
     <include name="*.jar" />
    </fileset>
   </copy>
</target>
<target name="rebuild" depends="build,clean">
   <ant target="clean" />
   <ant target="build" />
</target>
</project>
```
ok你的任务完成了。

ok,上边你完成了任务，但是你是否有些感触呢，在那些build.xml中，大多数是重复的，而且更改一次目录需要更改不少东西。是否能让工作做的更好一点呢，答案是肯定的。

引入两个东西：

1,propery

2,xml include

这两个东西都有一个功能，就是能把build.xml中<propery />中的内容分离出来，共同使用

除此之外它们各有特点：

propery的特点是维护简单，只需要简单的键值对，因为并不是所有人都喜欢xml的格式

xml include的特点是不单可以提取出属性来，连target也可以。

还是以前的例子：

例如我们想把src1 src2 src3这三个属性从xml中提出来，可以新建一个文件叫all.properties

里边的内容

```properties
src1=D:\\study\\ant\\src1
src2=D:\\study\\ant\\src2
src3=D:\\study\\ant\\src3
```

然后你的build.xml文件可以这样写，别人只需要更改配置文件，而不许要更改你的build.xml文件了
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<project name="main" default="build" basedir=".">
<property file="all.properties" />
<property name="bin" value="${basedir}\bin" />
<target name="init">
   <mkdir dir="${bin}" />
</target>
<target name="run">
   <ant dir="${src1}" target="run" />
   <ant dir="${src2}" target="run" />
   <ant dir="${src3}" target="run" />
</target>
<target name="clean">
   <ant dir="${src1}" target="clean" />
   <ant dir="${src2}" target="clean" />
   <ant dir="${src3}" target="clean" />
</target>
<target name="build" depends="init,call">
   <copy todir="${bin}">
    <fileset dir="${src1}">
     <include name="*.jar" />
    </fileset>
    <fileset dir="${src2}">
     <include name="*.jar" />
    </fileset>
    <fileset dir="${src3}">
     <include name="*.jar" />
    </fileset>
   </copy>
</target>
<target name="rebuild" depends="build,clean">
   <ant target="clean" />
   <ant target="build" />
</target>
<target name="test">
   <ant dir="${src1}" target="test" />
   <ant dir="${src2}" target="test" />
   <ant dir="${src3}" target="test" />
</target>
</project>
```
如果你自己看的话你会看到这样一个target
```xml
<target name="test">
<ant dir="${src1}" target="test" />
<ant dir="${src2}" target="test" />
<ant dir="${src3}" target="test" />
</target>
```
有的时候你想给每个小组的build.xml加入几个target，一种做法是每个里边写，然后在这里调用

但是有一种更好的方法。

你可以写一个include.xml文件，内容如下
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<property name="src" value="src"/>
<property name="dest" value="classes"/>
<target name="test" >
<ant target="run" />
</target>
```
然后更改你三个小组的build.xml文件,每个里边加入如下内容
```xml
<!--include a xml file ,it can be common propery ,can be also a target   -->
<!DOCTYPE project [
<!ENTITY share-variable SYSTEM "file:../include.xml">
]>
```

变成如下的样子

这个时候，你只要在include.xml添加propery , 添加target，三个build.xml会同时添加这些propery和target
而且不会让三个组的build.xml变得更复杂。
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--include a xml file ,it can be common propery ,can be also a target   -->
<!DOCTYPE project [
<!ENTITY share-variable SYSTEM "file:../include.xml">
]>
<project name="HelloWorld" default="run" basedir=".">
<!--use the include   -->
&share-variable;
<!--defined the property-->
<!--via include
<property name="src" value="src"/>
<property name="dest" value="classes"/>
-->
<property name="hello_jar" value="hello1.jar"/>
<!--define the op-->
<target name="init">
   <mkdir dir="${dest}"/>
</target>
<target name="compile" depends="init">
   <javac srcdir="${src}" destdir="${dest}"/>
</target>
<target name="build" depends="compile">
   <jar jarfile="${hello_jar}" basedir="${dest}"/>
</target>
<target name="run" depends="build">
   <java classname="test.ant.HelloWorld" classpath="${hello_jar}"/>
</target>
<target name="clean">
   <delete dir="${dest}" />
   <delete file="${hello_jar}" />
</target>
<target name="rerun" depends="clean,run">
   <ant target="clean" />
   <ant target="run" />
</target>
</project>
```

掌握了上边的那些内容之后，你就知道如何去写一个好的ant，但是你会发现当你真的想去做的时候，你不能马上作出好的build.xml，因为你知道太少的ant的默认提供的命令.这
个时候如果你想完成任务，并提高自己，有很多办法：

1,很多开源的程序都带有build.xml，看看它们如何写的

2,ant的document，里边详细列写了ant的各种默认命令，及其丰富

3,google，永远不要忘记它

ok,在这之后随着你写的ant build越来越多，你知道的命令就越多，ant在你的手里也就越来越强大了。
这个是一个慢慢积累的过程。

ant的例子很好找，各种开源框架都会带有一个build.xml仔细看看，会有很大收获

另外一个经常会用到的，但是在开源框架的build.xml一般没有的是cvs

如果使用的是远程的cvs，可以这样使用
```xml
<xml version="1.0" encoding="utf-8"?>
<project>
      <property name="cvsroot" value=":pserver:wang:@192.168.1.2:/cvsroot"/>
      <property name="basedir" value="/tmp/testant/"/>
      <property name="cvs.password" value="wang"/>
      <property name="cvs.passfile" value="${basedir}/ant.cvspass"/>
      <target name="initpass">
              <cvspass cvsroot="${cvsroot}" password="${cvs.password}" passfile="${cvs.passfile}"/>
      </target>
      <target name="checkout" depends="initpass">
              <cvs cvsroot="${cvsroot}" command="checkout" cvsrsh="ssh" package="myproject" dest="${basedir}"
               passfile="${cvs.passfile}"/>
       </target>
</project>
```
在eclipse里边先天支持ant，所以你可以在eclipse里边直接写build.xml

因为eclipse提供了提示功能，自动补充功能，它能让你事半功倍。

使用方法，只需要建立一个工程，然后建立一个叫build.xml的文件。然后就可以在里边写你的ant build了

但是时刻记住http://www.apache.org/永远能找到你需要的东西


### 使用案例

rpt_2017 部署到adcloud 生成环境ant 编译打包测试
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--模式执行build -->
<project name="adcloud" default="build" basedir=".">

   <property name="tomcat.home" value="E:/apache-tomcat-8.5.47"/>
   <property name="dist" value="/app/dist"/>
   <property name="tmp" value="/app/tmp"/>
   <!-- <property file="xx.properties" /> 这里如果是配置文件路径 , 后面变量引用也是一样 -->

    <!-- 初始ant所需目录和文件 -->
    <target name="init">
        <!-- 会在所属驱动盘根目录创建两个文件夹 -->
        <echo message="环境初始化.." />
        <mkdir dir="${dist}"/>
        <mkdir dir="${tmp}"/>
        <!--复制webRoot目录到${tmp}目录 ./标示当前目录根据 ant 相对命令执行所处目录(执行是进入到项目名下执行) -->
        <echo message="文件备份.." />
        <copydir src="./WebRoot" dest="${tmp}" forceoverwrite="true">
            <!--复制文件筛选规则(这里标示目录下所有文件) -->
            <include name="**/*" />
        </copydir>
        <!--先删除再创建 -->
        <deltree dir="${tmp}/WEB-INF/classes"/>
        <mkdir dir="${tmp}/WEB-INF/classes"/>
    </target>

    <!--编译 依赖 init 已经执行成功的基础上 -->
    <target name="compile" depends="init" >
        <!--执行javac命令 srcdir:源目录 destdir:编译后存储目录 -->
        <echo message="批量编译.." />
        <javac encoding="UTF-8"   srcdir="./src"  destdir="${tmp}/WEB-INF/classes"  debug="on" debuglevel="lines,vars,source" nowarn="true" failonerror="true">
            <!-- javac源文件所依赖jar包文件所处目录 -->
            <classpath>
                <fileset dir="${tmp}/WEB-INF/lib" includes="*.jar"/>
            </classpath>
            <!-- 编译该目录下所有文件 -->
            <include name="**/*.*"/>
        </javac>

        <!-- 编辑过程直接复制配置类文件到目标目录 -->
        <copydir src="./src/" dest="${tmp}/WEB-INF/classes" forceoverwrite="true">
            <include name="**/*.properties"/>
            <include name="**/*.log"/>
            <include name="**/*.xml"/>
        </copydir>
    </target> 

    <!-- 部署构建发布所需*.war包 依赖 compile步骤 -->
    <target name="build" depends="compile">
        <echo message="打包部署war.." />
        <jar jarfile="${tomcat.home}/webapps/ROOT.war">
            <fileset dir="${tmp}/">
                <include name="**/*.*" />
            </fileset>
        </jar>        
    </target>

    <!-- 清理临时目录 -->
    <target name="clean">
        <deltree dir="${tmp}"/>
        <deltree dir="${dist}"/>
        <deltree dir="${tomcat.home}/webapps/ROOT"/>
        <delete file="${tomcat.home}/webapps/ROOT.war"/>
    </target>

    <!-- 标签内无任何操作,但是会自动调用依赖clean , build 操作; 即先清理临时目录,然后再构建项目 -->
    <target name="create" depends="clean,build">
    </target>

    <!-- 修改JSP页面重新复制到 tomcat目录,不用重新编译  -->
    <target name="modify.reload">               
        <echo message="删除*.jsp文件"></echo>
        <delete verbose="true" includeemptydirs="true">
            <fileset dir="${tomcat.home}/webapps/ROOT" includes="**/*.jsp" defaultexcludes="false"/>
        </delete>

        <!-- <echo message="删除非 *.jsp文件"></echo>
        <delete verbose="true" includeemptydirs="true">
            <fileset dir="${tomcat.home}/webapps/ROOT" includes="**/*.jsp" defaultexcludes="false"/>
        </delete> -->

        <echo message="复制WebRoot下所有jsp文件到tomcat"></echo>
        <copydir src="./WebRoot" dest="${tomcat.home}/webapps/ROOT" forceoverwrite="true">
            <include name="**/*.jsp" />
        </copydir>         
    </target>

    <target name="stop_tomcat">  
        <echo message="停止tomcat..." />
        <exec executable="cmd" dir="${tomcat.home}/bin" failonerror="false"   
                    output="${log.file}" append="true" >  
            <!-- <arg value="/c" /> -->     
            <env key="CATALINA_HOME" path="${tomcat.home}"/>  
            <arg value="/c shutdown.bat" />     
        </exec>  
    </target>  
  
    <target name="start_tomcat" depends="build">  
        <echo message="启动tomcat..." /> 
        <exec executable="cmd" dir="${tomcat.home}/bin"  failonerror="false"   
                    output="${log.file}" append="true" >  
             <!-- <arg value="/c" /> -->    
             <env key="CATALINA_HOME" path="${tomcat.home}"/>  
             <arg value="/c startup.bat" />     
        </exec>  
    </target>

    <!--这里是一个执行带有 main 函数的例子
    <target name="main">       
        <java classname="com.test.distribute.Distribute 类的包路径" classpathref="jar包依赖">
           <arg value="参数1"/> 
           <arg value="参数2"/> 
        </java> 
    </target>
    -->
</project>
```
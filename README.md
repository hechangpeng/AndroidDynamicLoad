---
title: ClassLoader及动态加载的一点实践
date: 2017-11-03 09:58:00
tags:
categories: 动态化
---
<img src="http://owiq5fnuk.bkt.clouddn.com/0new.jpg"/>
编译后的class文件不是一次性全部加载到虚拟机中的，而是JVM根据需要动态加载的，这个加载的过程则由ClassLoader来负责实现，并返回这个类的Class对象给虚拟机。<!-- more -->
<br/><br/>
ClassLoader分为Java原生的ClassLoader与Android的ClassLoader，这里主要讲解Android中的ClassLoader，以及如何通过ClassLoader来实现动态加载，Java的ClassLoader主要通过三个子类来实现加载不同类型的文件，分别是：BootStrapClassLoader，ExtClassLoader，AppClassLoader。
BootStrapClassLoader：加载系统的一些运行库，如JDK核心的类库%JRE_HOME%\lib下的jar和class文件，加载的路径key为sun.boot.class.path，称之为引导类加载器。<br/>
ExtClassLoader：加载扩展库的类文件，如%JRE_HOME%\lib\ext下的jar和class文件，加载的路径key为java.ext.dirs，称之为扩展类加载器。<br/>
AppClassLoader：加载应用程序classpath路径的class文件，加载的路径key为java.class.path。<br/>
<br/>
classLoader加载一个类的过程是：先从已装载的列表中查找，如果未找到，则从父类到子类依次委托实现加载的，即先把搜索类的任务委托给父类加载器，如果父类加载器查找到了这个类，则子类加载器不重新加载，如果父类加载器没有找到这个类，则由子类自己实现的查找算法去查找，如果子类加载器自己也没有查找到，则抛出ClassNotFoundException。
<br/>
![](http://owiq5fnuk.bkt.clouddn.com/loadclass.png)
<br/>
<br/><br/>
Android的类加载过程与上面是相同的。
<br/><br/>
Android中的ClassLoader有三个子类，分别是DexClassLoader，PathClassLoader，URLClassLoader。<br/>
DexClassLoader：可以加载已安装的apk以及未安装的apk（如SD卡中的apk），还有jar包，可用于实现动态加载外部apk以及资源文件。<br/>
PathClassLoader：只能用于加载系统中已安装的apk。<br/>
Android中apk内.dex文件和Java中.class文件的关联：dex文件是对class文件的二次打包，不是简单的压缩打包，而是经过了对函数表、变量表优化，指令重排序，然后打包成dex文件。
<br/><br/>
下面主要介绍使用DexClassLoader来加载外部插件apk的类与资源文件到宿主程序中：
<br/><br/>
一、加载插件apk中的类文件：
<br/>
![](http://owiq5fnuk.bkt.clouddn.com/loadActivity.png)
<center>宿主Activity代码片段</center>
<br/>
![](http://owiq5fnuk.bkt.clouddn.com/pluginClass.png)
<center>插件Activity代码片段</center>
<br/><br/><br/>
二、加载插件apk中的资源文件：
<br/>
![](http://owiq5fnuk.bkt.clouddn.com/loadResource.png)
<center>宿主Activity代码片段</center>





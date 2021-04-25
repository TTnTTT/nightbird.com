---
title: 使用IDEA搭建jdk源码阅读环境
date: 2020-11-25 21:15:37
categories:
- [技术文章,工具使用]
tags: [工具使用]
---
### 找到源码位置

在安装JDK的位置找到src.zip和javafx-src.zip，解压

如果忘了，可以在IDEA中找到目录(Projuect Structure)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200325213907.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326183101.png)

### IDEA搭建

#### 第一步：新建项目

打开IDEA，按如下步骤新建一个简单Java项目，也可以是maven等项目

create new project

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326183843.png)

新建一个简单Java项目

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326184351.png)

点击finish后，项目就创建好了

#### 第二步：添加源码

将之前解压的src.zip复制到工程目录下

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326185106.png)

#### 第三步：替换JDK关联

为了能够备注以及避免修改到其他项目所使用的内容，添加一个名为“JavaJDK1.8Funyard”的SDK

并将Sourcepath修改为当前项目src下的源码内容

**移除Sourcepath中关联的src.zip**

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326185950.png)

将项目中的JDK源码关联到Sourcepath

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326190449.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326190432.png)

修改项目使用的JDK为上面新建的

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326190945.png)

#### 第四步：解决系统资源不足(OutOfMemoryError)

依次点击File–>Settings–>Build,Execution,Deployment–>Compiler

堆大小可由原来的700改为1700

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326191409.png)

#### 第五步：解决com.sun.tools.javac.api不存在

将jdk安装目录下(不是自己新建的这个源码阅读工程)lib包添加到项目中

依然是Projuect Structure

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200325213907.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326192128.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326192520.png)

点击ok，完成

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326192924.png)

#### 第六步：解决找不到sun.awt.UNIXToolkit和sun.font.FontConfigManager

在项目中新建sun.awt包和sun.font包，分别新建UNIXToolkit.java和FontConfigManager.java

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326193413.png)

到[OpenJDK](http://openjdk.java.net/)寻找[sun.awt.UNIXToolkit](http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/e17fe591a374/src/solaris/classes/sun/awt/UNIXToolkit.java)和[sun.font.FontConfigManager](http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/e17fe591a374/src/solaris/classes/sun/font/FontConfigManager.java)的源码，分别复制到上面新建的两个类里

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326193602.png)

选择对应版本jdk

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326194120.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326194349.png)

寻找UNIXToolkit类

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326194507.png)

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326195208.png)

进入到 /src/solaris/classes/sun/awt/目录，找到UNIXToolkit

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326195427.png)

FontConfigManager也是一样，路径：`src/solaris/classes/sun/font/FontConfigManager.java`

复制进去之后可以看到还是有错误的地方，这时候就要进行清理idea缓存然后重启：

点击左上角File->Invalidate Caches/Restart…

![](/img/images/使用IDEA搭建jdk源码阅读环境/20200326201036.png)

重启之后就可以顺利运行啦*(上面新建的类中可能还会报error，但是对使用没有影响)*

写好测试类，打好断点，右键debug，F7F7F7F7F7F7一直F7，爽到
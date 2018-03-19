---
title: 使用Tomcat发布Web项目
tags:
  - Java
  - tomcat
  - 环境搭建
date: 2018-01-27 16:02:41
comments: true
categories: 环境搭建
---


### 本文基于在windows系统下通过Tomcat发布war格式的Java Web项目进行讲解,阅读本文前需要掌握之知识：
1. Windows系统环境变量的配置
2. Windows下一般二进制（exe）文件安装

### 需要用到的依赖、文件：
1. 64位jre8（Java Runtime Environment）Java运行环境（官网[下载页面](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html)）
2. Tomcat8二进制安装包[(32-bit/64-bit Windows Service Installer)](http://mirrors.hust.edu.cn/apache/tomcat/tomcat-8/v8.5.27/bin/apache-tomcat-8.5.27.exe)
3. 需要发布的war包;
<!--more-->
### 发布步奏：
#### 安装或解压jre8环境到任意目录（这里假设放置在C:\runtime\jre8）

#### 配置环境变量

配置JAVA_HOME（非必需）,值为:
```
C:\runtime\jre8
```
配置path：在环境变量path后添加(非必需，改配置只是将java.exe加入运行环境，方便测试JAVA_HOME配置是否成功)：
```
;%JAVA_HOME%\bin;
```

#### 键入Win+r cmd打开控制台，在控制台中键入
```
输入：
java -verson

输出：
java version "1.8.0_31"
Java(TM) SE Runtime Environment (build 1.8.0_31-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.31-b07, mixed mode)
```

  若控制台输出java版本信息，则表示java环境配置成功；

    注：如果报 "‘java' 不是内部或外部命令，也不是可运行的程序
    或批处理文件。" ：
    * 确定JAVA_HOME环境变量配置是否正确
    * 在任务管理器重启资源管理器
    * 若实在无法成功可暂时放弃，因为安装tomcat时是可以选择jre环境目录的的；


#### 安装tomcat：
  * 双击exe进行安装：
  {% asset_img  001.PNG %}
  * 点击 **“Next”**
  {% asset_img  002.PNG %}
  * 点击 **“I Agree”**
  {% asset_img  003.PNG %}
  * 点击 **“Next”**
  {% asset_img  004.PNG %}
  * 按照相关要求配置访问端口号（第二行,默认为8080那个文本框），若无需配置，直接点击**“Next”**
  {% asset_img  005.PNG %}
  * 选择则jre安装路径
  {% asset_img  006.PNG %}
  * 选择jre8安装路径，本文中为（C:\runtime\jre8），选择好之后点击**”next“**
  {% asset_img  007.PNG %}
  * 如需自定义tomcat安装路径，修改路径即可（**注：记住该路径马上发布项目会用到**），如无需要，直接点击**”Install“**开始安装
  {% asset_img  008.PNG %}
  * 安装完成之后，记住勾选（默认是勾选的）**'Run Apache Tomcat'**选项，然后点击**“Finish”**
  {% asset_img  009.PNG %}

#### 安装完成
这时会在windows右下角或右下角托盘中看到一个tomcat图标
{% asset_img  010.PNG %}
双击该图标，会看见以下界面，其中**Stop** **Start**按钮分别控制tomcat项目打开或关闭（当前**Start**按钮是灰的是因为tomcat服务已经启动）
{% asset_img  011.PNG %}
在浏览器种键入http://localhost:8080/ （*端口根据安装时配置不同而异*）,若出现以下界面，则说明tomcat安装成功
{% asset_img  012.PNG %}

### 发布项目

* 进入上一步安装tomcat时所选择的文件夹，会看到以下目录结构：
{% asset_img  013.PNG %}

* 将要发布的war包复制到**webapps**文件夹下，等待大约1分钟不到，会看到**webapps**下自动生成一个与war包同名的文件夹，这时根据项目启动所需时间不同可能需1~3分钟时间左右等待项目发布完成，可以在浏览器中键入http://localhost:8080/[war包生成文件夹名称] ， 若能成功访问项目页面即表示项目发布成功；

* 再次更新项目时，建议先通过tomcat操作界面点击**Stop**按钮停止tomcat服务后，删除原webapps文件夹下上次发布的war包与其对应生成的文件夹，再次将war包复制进去，然后点击**Start**按钮再次启动tomcat服务

### 注意事项
若在项目发布过程中出现，明明已经发布最新的war包，但项目页面仍旧是之前老的没更新的情况，可停止tomcat服务，然后删除tomcat下*work*以及*temp*文件夹之后，再次启动tomcat服务再次查看



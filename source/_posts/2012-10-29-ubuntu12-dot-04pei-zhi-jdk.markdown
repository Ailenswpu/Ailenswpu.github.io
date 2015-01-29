---
layout: post
title: "Ubuntu12.04配置jdk"
date: 2012-10-29 11:10
comments: true
categories: java
---
因为最近可能需要使用android开发，所以还是把jdk的环境配置起来，下面就介绍一下我在ubuntu12.04中配置jdk的具体步骤：<br/>
先去oralce的官网下载Linux下面的JDK的压缩包，我下载的是jdk-7u9-linux-i586.tar.gz，下载后直接解压。<br/>
Step1:<br/>
首先将你下载好解压好文件jdk1.7.0_09使用root权限复制到/usr/lib/jvm目录里面,运行如下命令：<br/>
	sudo cp -r ~/jdk1.7.0_09/ usr/lib/jvm<br/>
Step2:<br/>
接着配置环境变量<br/>
	sudo emacs ~./bashrc<br/>
然后保存关闭，使用source更新下<br/>
	source ~/.bashrc<br/>
接着使用env查看JAVA_HOME的值<br/>
如果JAVA_HOME=/usr/lib/jvm/jdk1.7.0_09，说明配置成功<br/>
Step3：<br/>
将系统默认的jdk改过来（ubuntu系统自带openjdk，所以需要修改）<br>
改成当前系配置的jdk1.7<br/>
	sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.7.0_09/bin/java 300<br/>
	sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.7.0_04/bin/javac 300 <br/>
再运行:<br/>
	sudo update-alternatives --config java <br>
出现：<br/>
  选择       路径                                         优先级  状态<br/>
------------------------------------------------------------<br/>
* 0            /usr/lib/jvm/java-6-openjdk-i386/jre/bin/java   1061      自动模式<br/>
  1            /usr/lib/jvm/java-6-openjdk-i386/jre/bin/java   1061      手动模式<br/>
  2            /usr/lib/jvm/jdk1.7.0_09/bin/java               300       手动模式<br/>
再输入2修改成自己的jdk路径<br/>
Step4:<br/>
然后输入java-version看到如下信息，说明jdk安装成功<br/>
java version "1.7.0_09"<br/>
Java(TM) SE Runtime Environment (build 1.7.0_09-b05)<br/>
Java HotSpot(TM) Server VM (build 23.5-b02, mixed modeb)<br/>

---
layout: post
title: "使用Eclipse远程调试tomcat上的web应用"
date: 2006-04-21 17:55
comments: true
external-url: 
categories: 工具
---

其实解决这个问题最有效的方法就安装插件：简单，直接。但是如果你不想安装太多的插件到eclipse中或者你调试使用的tomcat和eclipse不在同一机器上的话，就要使用eclipse的remote debug直接进行调试,具体配置步骤如下:  

<!-- more -->

## 1. 设置tomcat的运行环境： ##
打开startup.bat文件,找到行  
	call  " %EXECUTABLE% "  start %CMD_LINE_ARGS% 

在之前加入下面的设置:  
	SET CATALINA_OPTS = -server -Xdebug -Xnoagent -Djava.compiler = NONE -Xrunjdwp:transport = dt_socket , server = y , suspend = n , address = XXXX 

或者直接修改catalina.bat文件如下:  
	set JPDA = jpda  
	set JPDA_TRANSPORT = dt_socket  
	set JPDA_ADDRESS = XXXX  

其中XXXX为自设定的不冲突端口号.

## 2. 设置eclipse的remote debug: ##
 打开eclipse中的debug设置窗口，选择Remote Java Application ，新建一个debug项，输入服务器IP和刚才设置端口号，点ok就可以进入debug状态了。  

![](http://www.blogjava.net/images/blogjava_net/zjun/8140/r_Eclipse%20Remote%20Debug.gif)
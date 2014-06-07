---
layout: post
title: "将Java应用注册为后台服务"
date: 2006-02-28 22:57
comments: true
external-url: 
categories: Java
---

项目中有一个java应用程序，交付后用户要求要把这个程序做成后台服务程序，即：系统启动后该程序可以自动启动，并且在前台不要出现运行窗口，维护人员只要在“服务管理”(Windows)中选择启动或停止即可.
解决办法如下：
   
<!-- more -->

##Linux##
在Linux中注册后台服务程序相对容易，只需编辑/etc/init.d/boot.local文件，在boot.local文件里加上下面这句脚本：  
	/iapappserver/MessageServer2.1/run.sh  
其中run.sh是java应用的运行脚本

## Windows ##
在Windows下的情况较为复杂，需要使用Windows提供的两个工具：**instsrv.exe**([下载](http://postboy.myetang.com/files/instsrv.exe))和**Srvany.exe**([下载](http://postboy.myetang.com/files/srvany.exe)).  
**instsrv: 这个工具是把win32程序变成系统服务。**    
基本用法:
	instsrv [服务名] [应用程序绝对路径] (增加服务)  
	instsrv [服务名] REMOVE　　(删除服务)  

但是并不是所有程序都适合做系统服务的，注册成系统服务后就是无法启动的,这时就需要用到srvany.

**srvany: 这个工具就是用来解决上面的工具中的问题，你注册的时候把它当作你要的服务，在用它来启动你的应用程序。**  
基本用法:  
	instsrv [服务名] [srvany的绝对路径]

具体步骤如下:  
1.注册服务: **path\instsrv 服务名 path\srvany.exe**  
2.       regedit打开注册表  
3.       进入HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\服务名  
4.       右键-新建-项 填入 Parameters  
5.       打开Parameters，右键-新建-字符串值 Application  
6.       双击Application 填入 应用程序的绝对路径,如: D:\workspace\app\main.exe  
7.       右键-新建-字符串值AppDirectory  
8.       双击AppDirectory 填入 应用程序的工作目录,如: D:\workspace\app\  
9.       启动服务。

在**Parameters**里还可以添加很多键值：  
　　**AppDirectory** 工作目录  
　　**AppParameters** 你的应用程序的参数。  
　　**AppEnvironment** 运行环境变量。  
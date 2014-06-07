---
layout: post
title: "Struts使用多个配置文件"
date: 2006-04-27 12:39
comments: true
external-url: 
category: 开源框架
---
注：本文是对《[使用多个Struts 配置文件](https://www6.software.ibm.com/developerworks/cn/education/java/j-strutsconfig/index.html)》一文的笔记，方便记忆和查询。  

----------   
   
<!-- more -->

# 1. 使用多个struts-config.xml 文件: #
当**struts-config.xml** 变得越来越大时，维护这个文件变成了一件很痛苦的事。可以根据不同的功能或模块将**struts-config.xml** 分解为不同的文件，以方便后期的开发和维护。  

## 第一步 ##
按照功能将**struts-config.xml** 划分为几个小文件，具体做法是：
(1) 将应用程序公共的全局配置放到**struts-config.xml** 中；
(2) 将相同功能的配置放到同一个配置文件中，如：struts-sms.xml,struts-mms.xml,struts-report.xml。建议所有的配置文件都以struts-做前缀，并且在文件名中表明功能模块；

## 第二步 ##
配置**web.xml**引用多个配置文件：

	< servlet >
	     < servlet-name > message </ servlet-name >
	     < servlet-class > org.apache.struts.action.ActionServlet </ servlet-class >
	     < init-param >
	       < param-name > config </ param-name >
	       < param-value > /WEB-INF/struts-config.xml,
	                   /WEB-INF/struts-sms.xml,
	                   /WEB-INF/struts-mms.xml,
	                   /WEB-INF/struts-report.xml 
	       </ param-value >
	     </ init-param >
	
	     < load-on-startup > 1 </ load-on-startup >
	   </ servlet >

**注意**：文件间用逗号分隔，包含每个文件的完整路径。

# 2. 使用多个资源文件： #
同样的原因，实现多语言的资源文件太大也是一件痛苦的事情，我们也可以将它按照功能分解为几个文件进行维护。

## 第一步 ##
按照功能将内容放入不同的资源文件，不多赘述；    
## 第二步 ##
在struts-config.xml中放置资源文件：  
	< message-resources  parameter ="Resources"  null ="false" />  
	< message-resources  parameter ="SmsResources"  key ="sms" />  
	< message-resources  parameter ="MmsResources"  key ="mms" />  
	< message-resources  parameter ="ReportResources"  key ="report" />  

**说明**：这里面的第一个元素（没有 key 属性）成为默认的资源集。所以所有的 JSP 和 servet 都可以通过 servlet 上下文访问这组资源。其他的资源则需要通过指定后面的key值来访问资源。  

## 第三步 ##
在页面中引用资源文件：  
	< bean:message  bundle ="sms"  key ="sms.label.send.name"   />

**说明**：属性 bundle用于指定要使用的非默认包，即指定前面在struts-config.xml中的key值。

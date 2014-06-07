---
layout: post
title: "[转] 常见数据库分页SQL语句"
date: 2006-03-08 18:59
comments: true
external-url: 
categories: 数据库
---

常见数据库分页SQL语句:原文在[这里](http://http//www.360doc.com/showWeb/0/0/51724.aspx)。

----------
<!-- more -->

我们在编写MIS系统和Web应用程序等系统时，都涉及到与数据库的交互，如果数据库中数据量很大的话，一次检索所有的记录，会占用系统很大的资源，因此我们常常采用，需要多少数据就只从数据库中取多少条记录，即采用分页语句。根据自己使用过的内容，把常见数据库Sql Server,Oracle和My sql的分页语句，从数据库表中的第M条数据开始取N条记录的语句总结如下：

## SQL Server ##
从数据库表中的第M条记录开始取N条记录，利用Top关键字：注意如果Select语句中既有top，又有order by，则是从排序好的结果集中选择：
{% highlight sql %} 
	SELECT *
        FROM  ( SELECT Top N *  
                         FROM  (SELECT Top (M + N - 1) * FROM 表名称 Order by 主键 desc) t1 ) t2
        Order by 主键 asc
{% endhighlight %}

例如从表Sys_option（主键为sys_id)中从10条记录还是检索20条记录，语句如下：
{% highlight sql %}        
	SELECT * 
        FROM ( SELECT TOP 20 *
                        FROM (SELECT TOP 29 * FROM Sys_option order by sys_id desc) t1) t2
        Order by sys_id asc
{% endhighlight %}

## Oralce数据库 ##
从数据库表中第M条记录开始检索N条记录  
{% highlight sql %} 
	SELECT * 
        FROM (SELECT ROWNUM r,t1.* From 表名称 t1 where rownum < M + N) t2
        where t2.r >= M
{% endhighlight %}

 例如从表Sys_option（主键为sys_id)中从10条记录还是检索20条记录，语句如下：  
{% highlight sql %} 
    SELECT * 
        FROM (SELECT ROWNUM R,t1.* From Sys_option where rownum < 30 ) t2
         Where t2.R >= 10
{% endhighlight %}

## My sql数据库 ##
My sql数据库最简单，是利用mysql的LIMIT函数,LIMIT [offset,] rows从数据库表中M条记录开始检索N条记录的语句为：  
{% highlight sql %} 
	SELECT * FROM 表名称 LIMIT M,N
{% endhighlight %}

例如从表Sys_option（主键为sys_id)中从10条记录还是检索20条记录，语句如下：  
{% highlight sql %} 
	select * from sys_option limit 10,20
{% endhighlight %}
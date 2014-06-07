---
layout: post
title: "DisplayTag 1.1 Released, 可分页读取数据！！！"
date: 2006-02-27 20:21
comments: true
external-url: 
categories: Web开发
---

DisplayTag一直以来为人们所诟病的缺点就是它的装载数据的方式: **一次性把所有数据读取到内存中，然后再分页显示**。这样做的后果就是在处理大批量数据时力不从心,可用性急剧下降,并且在翻页显示的仅仅是内存中的数据而不是实时数据.因此人们往往只是在实现小型项目的小数量表格分页显示时才想到DisplayTag，并且因为考虑到项目的扩展往往最终弃用DiaplayTag.
<!-- more -->

但是**发布于本月12日的1.1版本彻底解决了这个问题**，新版的DisplayTag提供了两种方式实现部分装入数据:

**(1)实现接口** org.displaytag.pagination.PaginatedList,用户可以实现这个接口用来代替以前传入DisplayTag的list对象,DisplayTag将认为分页和排序的动作已经由外部动作执行了,而不会使用 原来的分页方式(即全部读入内存).但是这样用户需要自己编写分页和排序代码;

**(2)设置相应参数**,告诉DisplayTag传入的数据集由外部分页和排序,而不使用默认的方式.比如:
	<display:table name="testList" sort="external" defaultsort="1" pagesize="20" id="element" partialList="true" size="resultSize">

其中：

**sort="external"** 告诉DisplayTag传入的数据集已经由外部程序排好序了..  
**defaultsort="1"** 说明默认是升序(Descending is 2, Ascending is 1);  
**partialList="true"** 说明部分装入数据;  
**pagesize="20"** 每页显示记录数;  
**size="resultSize"** 显示记录的总条数(此参数结合PageSize，使得表格在只拿到某一页的完整数据的同时，可以知道会有多少页，并将其他的页数也列举出来，当用户实际翻页时才去获取当页数据);

关于以上翻页范例的详细内容可以查看发布包文档中的[External paging/sorting](http://displaytag.sourceforge.net/11/tut_externalSortAndPage.html) 一节。
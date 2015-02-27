---
layout: post
title: "Dojo在IE中的那些坑"
date: 2013-05-14 17:37
comments: true
external-url: 
categories: Web
---

最近的一个小项目中使用的[dojo](http://dojotoolkit.org/)作为前台页面框架。本来很简单的功能，但是发布给用户做  UAT时却发现一堆简单低级的错误，但在开发环境却始终不能重现，百思不得其解...原来用户的测试环境是 IE8，果然问题在这，之前测试是在FireFox，没想到相同的功能在IE中却报错。   

<!-- more -->

### 坑一： ###

- 症状：页面上普通按钮，在IE中确实始终会默认submit页面。  
- 原因：在[这里](http://zhidao.baidu.com/question/531818487.html)找到类似的问题：
>button元素的默认type属性的值为submit，这个是标准，和IE版本没关系。如果不喜欢可以直接用< input type='button'>代替。  

- 解决方案：在 button 强制加上 type='button' 属性  

		< button data-dojo-type="dijit.form.Button" type='button'>  
		   Add  
		< script type="dojo/method" data-dojo-event="onClick" data-dojo-args="evt">  
		   add()  
		< /script>    
		< /button>

### 坑二： ###
- 症状：在datagrid上做了 inline edit后，保存后数据不会刷新，总是更改前的就数据
- 原因： datagrid的data store默认是使用了cache,这在FireFox中是不会引起问题的，但在IE中就不能刷新数据。
- 解决方案：在datagrid中不使用缓存：
		var store= new dojo.data.ItemFileWriteStore({ 
			url : '<c:url value="/billitem.wss?method=modifiedListStore&selectId=${selectId}" />', 
			requestMethod:"post",
			urlPreventCache:true} 
		);

### 坑三： ###
- 症状：datagrid中最后修改的cell里的值总是不能保存到数据库
- 原因：datagrid是通过onApplyCellEdit事件将未保存的修改数据save到itemStore._pending(_newItem，_modifiedItems, _removedItems).但是onApplyCellEdit是通过Enter或者blur触发的，在FireFox中当用户点击当前cell以外的任意页面都会触发这个事件，但是在IE中必须要点击datagrid的其他地方才能触发，否则不会（比如：点击grid以外的save按钮）。
- 解决方案：google了一下午，最后在[这里](http://dojo-toolkit.33424.n3.nabble.com/DataGrid-last-edited-cell-does-not-save-td2358868.html)找到解决办法：
>this is a known problem, I think.  Well at least I've known about it for
a while lol. I'm lazy so I never bothered to check for a bug on it. I
always figured that I could manually trigger lost focus or something on
the cell to get it to set the value to the store.  

		< table dojoType="dojox.grid.DataGrid" onBlur="onTableBlur">---< /table>
	
		function onTableBlur()
		{
		  if (this.edit.isEditing())
		    this.edit.apply();
		} 

### 参考资料： ###
- [DataGrid last edited cell does not save](http://dojo-toolkit.33424.n3.nabble.com/DataGrid-last-edited-cell-does-not-save-td2358868.html)   
- [Event not firing for dojo's DataGrid](http://stackoverflow.com/questions/12616974/event-not-firing-for-dojos-datagrid)  
- [html中button自动提交表单问题!](http://zhidao.baidu.com/question/531818487.html)
---
layout: post
title: "HighCharts 笔记2：通过表格生成Pie Chart"
date: 2013-04-01 15:49
comments: true
external-url: 
categories: Web
---
接上一篇 [HighCharts笔记之: Bar Chart](http://zjun.github.com/2013/03/28/highcharts-json-dataformat/)，这一篇继续记录在项目中使用 Pie Chart 的情况,只是自己的一点总结和记录，以备日后翻阅。这一次是通过页面上的表格(Table)数据，生成对应的饼图（Pie Chart），具体实现如下：
>**Highcharts** 是一个用纯JavaScript编写的一个图表库, 能够很简单便捷的在web网站或是web应用程序添加有交互性的图表，并且免费提供给个人学习、个人网站和非商业用途使用。目前HighCharts支持的图表类型有曲线图、区域图、柱状图、饼状图、散状点图和综合图表。
HighCharts界面美观，由于使用JavaScript编写，所以不需要像Flash和Java那样需要插件才可以运行，而且运行速度快。另外HighCharts还有很好的兼容性，能够完美支持当前大多数浏览器。

<!-- more -->

### Pie Chart ###
![](http://farm9.staticflickr.com/8539/8609442562_2945899e60.jpg)

### HTML Code ###
	<body>

		<div class="modal-header">
			<button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
			<h3 id="myModalLabel">${title}</h3>
		</div>
		<div class="modal-body">
			<div id="container2" style="width: 100%; height: 280px"></div>
	
			<table id="datatable" class="table table-striped table-bordered table-hover table-condensed">
				<thead>
					<tr>
						 <th>User</th>
						 <th>${title}</th>
					</tr>
				</thead>
				<tbody>
					<c:forEach items="${userSum}" var="item">
					<tr>
						<th>${item.user }</th>
						<td>${item.sum }</td>
					</tr>
					</c:forEach>
				</tbody>
			</table>
		</div>
		<div class="modal-footer">
			<button class="btn" data-dismiss="modal" aria-hidden="true">Close</button>
		</div>
	
	</body>

### Javascript Code ###
	var chart;
	Highcharts.visualize = function(table, options) {
	   
	    // the data series
	    options.series = [];
	    var l= options.series.length; 
	    options.series[l] = {
	      name: '${title}',
	      data: []
	    };
	    
	    $('tbody tr', table).each( function(i) {
	      	var tr = this;
	      	var th = $('th', tr).text();
	      	var td =parseFloat($('td', tr).text());
	      	options.series[0].data.push({name:th,y:td});
	    });
	    chart = new Highcharts.Chart(options);
	}
	   
	// On document ready, call visualize on the datatable.
	$(document).ready(function() {         
	   var table = document.getElementById('datatable'),
	   options = {
	         chart: {
	            renderTo: 'container2',
	            defaultSeriesType: 'pie'
	         },
	         title: {
	            text: '${title}'
	         },
	         tooltip: {
	            formatter: function() {
	               return '<b>'+ this.series.name +'</b><br/>'+
	               this.point.name +' '+ this.y;
	            }
	         },
	         plotOptions: {
	             pie: {
	                 allowPointSelect: true,
	                 cursor: 'pointer'
	             }
	         }
	      };
	   
	                     
	   Highcharts.visualize(table, options);
	});

这种做法在 Coding 时会更简洁，但在实际运行时效率不高，因为 Pie Chart 需要等待页面上的数据都生成时才能呈现，所以总是给用户一种迟滞感，所以个人感觉还是通过 JSon 的方式生成图表更好（更快）。
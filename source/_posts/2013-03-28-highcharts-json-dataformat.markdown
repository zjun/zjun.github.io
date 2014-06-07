---
layout: post
title: "HighCharts笔记之: Bar Chart"
date: 2013-03-28 16:52
comments: true
external-url: 
categories: Web开发 
---
最近需要做一些Web图标，研究了几个开源的第三方工具后，最后决定使用**HighCharts**开发：

**Highcharts** 是一个用纯JavaScript编写的一个图表库, 能够很简单便捷的在web网站或是web应用程序添加有交互性的图表，并且免费提供给个人学习、个人网站和非商业用途使用。目前HighCharts支持的图表类型有曲线图、区域图、柱状图、饼状图、散状点图和综合图表。
HighCharts界面美观，由于使用JavaScript编写，所以不需要像Flash和Java那样需要插件才可以运行，而且运行速度快。另外HighCharts还有很好的兼容性，能够完美支持当前大多数浏览器。

HighCharts 可以通过JSON 数据格式与后台交互，从而生成动态的图表。但是在官方的文档中关于 JSON 数据格式的资料很少，经过一下午的调试终于找到了关于柱状图和饼图的数据格式，在这里记录一下，以备后续的查询：  
<!-- more -->
## 柱状图： ##
![](http://farm9.staticflickr.com/8382/8596618053_22114c25cc_b.jpg)
JSON Data
   
	[
		{
			"data":[0,0,0,0,0,0,0,0,9,0,0,1],
			"name":"Actual"
		},
		{
			"data":[1,1,1,4,1,1,1,1,12,1,3,6],
			"name":"Target"
		}
	]

Javascript code

	<script type="text/javascript">
		$(function () {
		    var options = {
	            chart: {
	            	
	                renderTo: 'container',
	                defaultSeriesType: 'bar'
	            },
	            title: {
	                text: ''
	            },
	            subtitle: {
	                text: ''
	            },
	            xAxis: {
	                categories: ['Activity Entry', 'Activity Update', 'Blog Entry', 'Blog Comments', 'Feed','Bookmark','File','Forum Topic','Forum Reply','Wiki','Ram','Liquid'],
	                title: {
	                    text: null
	                }
	            },
	            yAxis: {
	                min: 0,
	                title: {
	                    text: 'Units',
	                    align: 'high'
	                },
	                labels: {
	                    overflow: 'justify'
	                }
	            },
	            tooltip: {
	                formatter: function() {
	                    return ''+
	                        this.series.name +': '+ this.y;
	                }
	            },
	            plotOptions: {
	                bar: {
	                    dataLabels: {
	                        enabled: true
	                    }
	                }
	            },
	            legend: {
	                layout: 'vertical',
	                align: 'right',
	                verticalAlign: 'top',
	                x: -100,
	                y: 100,
	                floating: true,
	                borderWidth: 1,
	                backgroundColor: '#FFFFFF',
	                shadow: true
	            },
	            credits: {
	                enabled: false
	            },
	            series: []
	        };
		    
		    $.getJSON('<c:url value="/action.do?method=barChart"/>', function(json) {
		    		options.series = json;//options.series.push(json);  
		    		//console.log("JSON: " + JSON.stringify(options));
		            //console.log("Render to element with ID : " + options.chart.renderTo);
		            //console.log("Number of matching dom elements : " + $("#" + options.chart.renderTo).length);
	           		new Highcharts.Chart(options);
	        	}).error(function() {console.log('error');});
		    
		    $("a[data-toggle=modal]").click(function(){
		        var target = $(this).attr('data-target');
		        var url = $(this).attr('href');
		        $(target).innerHTML='';
		        $(target).load(url);
		    });    
		});
	</script>  
   
HTML 

	<div id="container" style="width: 100%; height: 400px"></div>

Java Code - Action

	public ActionForward barChart(ActionMapping mapping, ActionForm form,
				HttpServletRequest request, HttpServletResponse response)
				throws IOException {
			request.setCharacterEncoding("UTF-8");
			response.setCharacterEncoding("UTF-8");
			response.setContentType("application/json;charset=utf-8");
	
			List<Bar> resultList = getBarData();
			JSONArray json = new JSONArray(resultList);
			String result = json.toString();// 转成json数据
	
			PrintWriter out = response.getWriter();
			out.write(result);
			out.flush();
			out.close();
	
			return null;
		}
	
		private List<Bar> getBarData() {
			BlueCommunity target = dao.getLatestTarget();
			BlueCommunity actual = dao.sumLatestActual();
	
			List<Bar> resultList = new ArrayList<Bar>();
			if (actual != null) {
				resultList.add(new Bar("Actual", actual.getValueArray()));
			} else {
				resultList.add(new Bar("Actual", new int[] { 0, 0, 0, 0, 0, 0, 0,
						0, 0, 0, 0, 0 }));
			}
			resultList.add(new Bar("Target", target.getValueArray()));
	
			return resultList;
	}

Java Code - Bar class
   
	public class Bar {
	
		private static final long serialVersionUID = 6461863786317563773L;
	
		private String name;
		private int[] data;
	
		public Bar() {
		};
	
		public Bar(String name, int[] data) {
			this.name = name;
			this.data = data;
		}
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public int[] getData() {
			return data;
		}
	
		public void setData(int[] data) {
			this.data = data;
		}
	
	}

Java Code - Community class  

	public class Community implements Serializable {
	
		private static final long serialVersionUID = -7516165631503337884L;
	
		private int id;
		private int version;
		private String remark;
		private int activity_entry;
		private int activity_update;
		private int blog_entry;
		private int blog_comments;
		private int feed;
		private int bookmark;
		private int file;
		private int forum_topic;
		private int forum_reply;
		private int wiki;
		private int iRam;
		private int liquid;
		private int user;
	
		...
		getters;
		setters;
		...
	
		public int[] getValueArray() {
			int[] array = { activity_entry, activity_update, blog_entry,
					blog_comments, feed, bookmark, file, forum_topic, forum_reply,
					wiki, iRam, liquid };
			return array;
		}
	
	}
  
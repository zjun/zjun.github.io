---
layout: post
title: "使用 LRUMap"
date: 2006-07-31 14:52
comments: true
external-url: 
categories: Java
---
使用过开源缓存包(如:[EHCache](http://ehcache.sourceforge.net/))都知道，缓存策略中主要分为FIFO,LRU,LFU等几种。而Jakarta Commons中提供的 **org.apache.commons.collections.map.LRUMap** 可用来保存最近使用的几条记录，提供了**LRU(Least Recently Used)**缓存策略的实现。

具体使用方法如下:
<!-- more -->

	import  java.util.Map;
	import  org.apache.commons.collections.map.LRUMap;
	
	LRUMap cache  =   new  LRUMap( 5  );     
	
	//  Populate the cache with 5 stock prices
	cache.put(  " MSFT " ,  new  Float(  0.03  ) );
	cache.put(  " TSC " ,  new  Float(  0.001  ) );
	cache.put(  " LU " ,  new  Float(  23.30  ) );
	cache.put(  " CSCO " ,  new  Float(  242.20  ) );
	cache.put(  " P " ,  new  Float(  10.23  ) );
	     
	//  Now use some of the entries in the cache
	Float cscoPrice   =  (Float) cache.get(  " CSCO "  );
	Float msPrice  =  (Float) cache.get(  " MSFT "  );
	Float tscPrice  =  (Float) cache.get(  " TSC "  );
	Float luPrice  =  (Float) cache.get(  " LU "  );
	Float pPrice  =  (Float) cache.get(  " P "  );
	Float msPrice2  =  (Float) cache.get(  " MSFT "  );
	  
	//  Add another price to the Map, this should kick out the LRU item.
	cache.put(  " AA " ,  new  Float(  203.20  ) );
此时,缓存中的元素为:

>[CSCO]  
[MSFT]  
[TSC]  
[LU]  
[P]  
[AA]  

由于LRU最近没有被访问,所以当AA加入时,由于超过了缓存的最大容量(5),所以被剔除了缓存.
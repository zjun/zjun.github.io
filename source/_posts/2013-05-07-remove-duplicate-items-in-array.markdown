---
layout: post
title: "剔除List中的重复值"
date: 2007-01-18 13:48
comments: true
external-url: 
categories: Java
---

###  方法一：循环元素删除 ###
	//  删除ArrayList中重复元素
	public   static   void  removeDuplicate(List list)  {
	   for  ( int  i  =   0 ; i  <  list.size()  -   1 ; i ++ )  {
	    for  ( int  j  =  list.size()  -   1 ; j  >  i; j -- )  {
	      if  (list.get(j).equals(list.get(i)))  {
	        list.remove(j);
	      }
	    }
	  }
	  System.out.println(list);
	}
	
<!-- more -->

### 方法二：通过HashSet剔除 ###
	//  删除ArrayList中重复元素
	public   static   void  removeDuplicate(List list)  {
	    HashSet h  =   new  HashSet(list);
	    list.clear();
	    list.addAll(h);
	    System.out.println(list);
	}

### 方法三： 删除ArrayList中重复元素，保持顺序 ###
	// 删除ArrayList中重复元素，保持顺序
	public   static   void  removeDuplicateWithOrder(List list)  {
	      Set set  =   new  HashSet();
	      List newList  =   new  ArrayList();
	   for  (Iterator iter  =  list.iterator(); iter.hasNext();)  {
	         Object element  =  iter.next();
	         if  (set.add(element))
	            newList.add(element);
	     }
	     list.clear();
	     list.addAll(newList);
	     System.out.println( " remove duplicate "   +  list);
	 }

---
layout: post
title: "[GoF23]java中的Proxy模式(续)"
date: 2006-04-12 11:06
comments: true
external-url: 
categories: 设计模式
---

在 [上一篇随笔](http://zjun.github.io/2006/03/21/proxy-pattern-in-java/) 中,由于时间和篇幅的关系只是简单介绍了Proxy模式的概念,并没有写到Java中对Proxy模式特有的支持,所以严格说起来是有点"名不符实",现在就接着介绍JDK中Proxy模式的实现:  
Java API中提供了对Proxy模式的支持,主要是通过反射(Reflect)包中的**Proxy**类和**InvocationHandler**接口实现,具体过程如下:  
----------
   
<!-- more -->

(1) 实现**InvocationHandler**接口,在**invoke()**方法中实现代理类要完成的操作;  
(2) 通过**Proxy.newProxyInstance(ClassLoader loader,Class[]
interfaces,InvocationHandler h)**方法生成一个代理类,从参数可以看出代理类将实现被代理对象的接口,而具体的实现过程是在上面实现的**InvocationHandler.invoke()**中定义的.

----------

我们还是用演艺圈的例子来说明：  
首先，**类的关系图**需要修改一下：  
![](http://www.blogjava.net/images/blogjava_net/zjun/8140/r_proxy2.gif)

可以看出，这里并没有经纪人(Broke)类，而是新增了一个经纪人操作类(BrokeHandler),这是因为通过Proxy.newProxyInstance()方法,Java API将自动为我们生成一个对于Artist接口的代理类(即：Broke),我们只需定义代理的操作即可．  

## 经纪人工作 ##
	/**
	 * 经纪人工作
	 * 
	 *  @author  zjun
	 *  @version  1.0 create on 2006-4-11 18:06:36
	  */
	public   class  BrokerHandler  implements  InvocationHandler  {
	
	     private  String SIGN  =   "  [经纪人工作]  " ;
	
	     //  旗下明星
	     private  Star star;
	
	     public  BrokerHandler(Star star)  {
	         this .star  =  star;
	    }
	
	     /**
	     * 签订和约
	      */
	     private   void  subcontract()  {
	        System.out.println(SIGN  +   "  签订和约  " );
	    }
	
	     /**
	     * 演出后交税
	      */
	     private   void  payTax()  {
	        System.out.println(SIGN  +   "  演出后交税  " );
	    }
	
	     /**
	     *  @see  java.lang.reflect.InvocationHandler#invoke(java.lang.Object,
	     *      java.lang.reflect.Method, java.lang.Object)
	      */
	     public  Object invoke(Object proxy, Method method, Object[] args)
	             throws  Throwable  {
	        Object obj  =   null ;
	         //  签约
	        subcontract();
	         //  安排艺人演出
	        obj  =  method.invoke(star, args);
	         //  交税
	        payTax();
	         return  obj;
	    }
	}

## 艺人 ##
	/**
	 * 艺人
	 * 
	 *  @author  zjun
	 *  @version  1.0 create on 2006-4-11 18:05:48
	  */
	public   interface  Artist  {
	     public   void  show(String showType);
	}

## 明星 ##
	/** 
	 * 明星
	 * 
	 *  @author zjun
	 *  @version 1.0 create on 2006-4-11 18:08:17
	  */
	public   class  Star  implements  Artist  {
	     private  String SIGN  =   "  [明星]  ";
	
	     public   void  show(String showType)  {
	        System.out.println(SIGN  + showType);
	    }
	
	}

## 演出赞助商 ##
	/**
	 * 演出赞助商
	 * 
	 *  @author zjun
	 *  @version 1.0 create on 2006-4-11 18:30:25
	  */
	public   class  Patron  {
	
	     /** 
	      *  @param args
	      */
	    public   static   void  main(String[] args)  {
	        Star star  =   new Star();
	        BrokerHandler broker  =   new BrokerHandler(star);
	        Artist b  = (Artist) Proxy.newProxyInstance(star.getClass()
	                .getClassLoader(), star.getClass().getInterfaces(), broker);
	        b.show( "  演电影  ");
	        b.show( "  拍电视  ");
	        b.show( "  出唱片  ");
	        b.show( "  演唱会  ");
	
	    }
	}

**[运行结果]**：
	  [ 经纪人工作 ]  签订和约   
	  [ 明星 ]  演电影   
	  [ 经纪人工作 ]  演出后交税   
	  [ 经纪人工作 ]  签订和约   
	  [ 明星 ]  拍电视   
	  [ 经纪人工作 ]  演出后交税   
	  [ 经纪人工作 ]  签订和约   
	  [ 明星 ]  出唱片   
	  [ 经纪人工作 ]  演出后交税   
	  [ 经纪人工作 ]  签订和约   
	  [ 明星 ]  演唱会   
	  [ 经纪人工作 ]   演出后交税   


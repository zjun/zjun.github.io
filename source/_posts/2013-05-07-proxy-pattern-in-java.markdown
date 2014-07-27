---
layout: post
title: "[GoF23] java中的Proxy模式"
date: 2006-03-21 23:34
comments: true
external-url: 
categories: Java
---
**代理（Proxy）模式**，顾名思义就是对象之间不直接发生联系，而是通过中间的代理人进行沟通。这种关系在现在的娱乐圈中普遍存在：明星通常都有经纪人来代理自己的演出事宜，而不是直接去联系演出商。下面就通过这个实际中的例子来说明代理模式。  
首先画出**关系类图**：  
![](http://www.blogjava.net/images/blogjava_net/zjun/8140/r_Proxy.jpg)
<!-- more -->

**演艺人士**：包括经纪人和明星，都属于娱乐圈中人。      
	/**
	 * 演艺人士
	 * 
	 *  @author  zJun
	  */
	public   interface  Artist  {
	     /**
	     * 演出活动
	      */
	     public   void  show(String showType);
	}

**经纪人**：  
	/**
	 * 经纪人（参考《Java与模式》－ "代理(Proxy)模式"一章部分代码）
	 * 
	 *  @author  zJun
	 * 
	  */
	public   class  Broker  implements  Artist, InvocationHandler  {
	
	     private  String SIGN  =   " [经纪人] " ;
	
	     //  旗下明星
	     private  Star star;
	
	     /**
	     * 签订和约
	      */
	     public   void  subcontract()  {
	        System.out.println(SIGN  +   " 签订和约 " );
	    }
	
	     /**
	     * 经纪人代明星打理一切演出事宜
	      */
	     public   void  show(String showType)  {
	         //  签约
	        subcontract();
	
	         //  通知明星演出
	         if  (star  ==   null )  {
	            star  =   new  Star();
	        }
	        star.show(showType);
	
	         //  交税
	        payTax();
	    }
	
	     /**
	     * 演出后交税
	      */
	     public   void  payTax()  {
	        System.out.println(SIGN  +   " 演出后交税 " );
	    }
	}

**明星**：  
	/**
	 * 明星
	 * 
	 *  @author  zJun
	 * 
	  */
	public   class  Star  implements  Artist  {
	
	     private  String SIGN  =   " [明星] " ;
	
	     public   void  show(String showType)  {
	        System.out.println(SIGN  +  showType);
	    }
	
	}

**赞助商**：通过联系经纪人安排明星演出
	/**
	 * 赞助商: 与经纪人联系安排演出事宜
	 * 
	 *  @author  zJun
	  */
	public   class  Patron  {
	
	     /**
	     *  @param  args
	      */
	     public   static   void  main(String[] args)  {
	        Broker broker  =   new  Broker();
	        broker.show( " 演电影 " );
	        broker.show( " 拍电视 " );
	        broker.show( " 出唱片 " );
	        broker.show( " 演唱会 " );
	    }
	
	}

**[运行结果]：**  
	[ 经纪人 ] 签订和约  
	[ 明星 ] 演电影  
	[ 经纪人 ] 演出后交税  
	[ 经纪人 ] 签订和约  
	[ 明星 ] 拍电视  
	[ 经纪人 ] 演出后交税  
	[ 经纪人 ] 签订和约  
	[ 明星 ] 出唱片  
	[ 经纪人 ] 演出后交税  
	[ 经纪人 ] 签订和约   
	[ 明星 ] 演唱会  
	[ 经纪人 ] 演出后交税  

**参考资料**：  
《Java与模式》 阎宏 电子工业出版社
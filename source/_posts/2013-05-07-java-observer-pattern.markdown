---
layout: post
title: "[GoF]java中的观察者模式(Observer)"
date: 2006-05-18 10:54
comments: true
external-url: 
categories: Java
---

Java作为一种面向对象的开发语言，对实现设计模式提供了良好的支持，并且提供了许多默认的实现，比如：通过Java中的**Observable类**和**Observer接口**可以方便的实现观察者模式。

下面我们就用一个实际的例子来说明：日常生活中说起观察者，最常见的例子可能就是天气预报，在这里我们的观察对象是地球，而我们是通过发射气象卫星这个观察者来检测地球气象变化的。
   
<!-- more -->

所以这个例子中涉及三个对象：  
**地球 (Earth)：			被观察对象**  
**气象卫星(Satellite)：    观察者**  
**气象局(WeatherService)： 客户端调用**  

**被观察对象：地球 (Earth) ** 
	import  java.util.Observable;
	
	/**
	 * 被观察对象：地球
	 * 
	 *  @author  zjun
	 *  @version  1.0 create on 2006-5-18 9:42:45
	  */
	public   class  Earth  extends  Observable  {
	     private  String weather  =   " 晴朗 " ;
	
	     /**
	     *  @return  Returns the weather.
	      */
	     public  String getWeather()  {
	         return  weather;
	    }
	
	     /**
	     *  @param  weather
	     *            The weather to set.
	      */
	     public   void  setWeather(String weather)  {
	         this .weather  =  weather;
	         //  设置变化点
	        setChanged();
	        notifyObservers(weather);
	    }
	}

**[注意]** 在需检测的对象前需要**设置变化点setChanged()和通知观察者notifyObservers()**，这两个函数是由Observable类实现的，封装了观察者模式实现的细节。

**观察者：气象卫星(Satellite)**  
	import  java.util.Observable;
	import  java.util.Observer;
	
	/**
	 * 观察对象：气象卫星
	 * 
	 *  @author  zjun
	 *  @version  1.0 create on 2006-5-18 9:46:30
	  */
	public   class  Satellite  implements  Observer  {
	     private  String weather;
	
	     public   void  update(Observable obj, Object arg)  {
	        weather  =  (String) arg;
	         //  捕获天气变化情况，反馈给检测者
	        System.out.println( " 近期天气变化： "   +  weather);
	    }
	}

**客户端调用：气象局(WeatherService)**  
	/**
	 * 客户端调用：天气预报
	 * 
	 *  @author  zjun
	 *  @version  1.0 create on 2006-5-18 9:57:19
	  */
	public   class  WeatherService  {
	
	     /**
	     *  @param  args
	      */
	     public   static   void  main(String[] args)  {
	        Earth earth  =   new  Earth();
	        
	        Satellite satellite  =   new  Satellite();
	         //  发射气象卫星
	        earth.addObserver(satellite);
	
	        System.out.println( " 天气预报： " );
	        System.out.println( " ------------ " );
	        earth.setWeather( " 台风‘珍珠’逼近 " );
	        earth.setWeather( " 大到暴雨 " );
	        earth.setWeather( " 天气炎热 " );
	    }
	}

**[运行结果]**  
	天气预报：  
	------------   
	近期天气变化：台风‘珍珠’逼近  
	近期天气变化：大到暴雨  
	近期天气变化：天气炎热  


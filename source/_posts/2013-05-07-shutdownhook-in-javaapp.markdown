---
layout: post
title: "为程序加上关闭钩子(ShutdownHook)"
date: 2006-03-12 23:38
comments: true
external-url: 
categories: Java
---

“**关闭钩子**”（ShutdownHook）是这样一个概念：向虚拟机注册一个线程，当程序退出(Ctrl+C)时虚拟机会启动这个线程,我们可以在这个线程的run()中做一些清除的工作,如:**释放数据库连接,关闭文件**等.  

## 说明 ##
shutdownhook通常用来在Ctrl+C退出时触发清理工作(多是在后台服务中，这种服务通常是24*7运行的，正常情况下是不退出的)，如果能够在程序中显式地确定退出的时机，那么最好是直接在退出前做清理，不用搞得这么复杂。

<!-- more -->

## 注册 ##

	Runtime.getRuntime().addShutdownHook(Thread t); 

## 注销 ##

	Runtime.getRuntime().removeShutdownHook(Thread t);

**[例子]**  
	/**
	 * 在这个线程中实现程序退出前的清理工作  
	 * 
	 * @author Administrator  
	 * 
	 */  
	class TestThread extends Thread {
	    boolean isTerminal = false;
	
	    public void run() {
	        while (!isTerminal) {
	               try {
	                  Thread.sleep(1000);
	               } catch (InterruptedException e) {
	                  e.printStackTrace();
	               }
	            System.out.println("run sub thread");
	        }
	    }
	
	    /**
	     * 清理工作
	     */
	    public void onTerminal() {
	        isTerminal = true;
	        System.out.println("stop sun sub thread");
	    }
	}
	
	/**
	 * ShutdownDownHook测试类
	 * 
	 * @author Administrator
	 * 
	 */
	public class TestShutdownHook extends Thread {
	    TestThread testThread;
	
	    public void addThread(TestThread t) {
	        testThread = t;
	    }
	
	    /**
	     * 实现程序退出前的清理工作
	     */
	    public void run() {
	        System.out.println("This is ShutdownHook");
	        testThread.onTerminal();
	    }
	
	    public static void main(String[] args) {
	        TestShutdownHook m = new TestShutdownHook();
	        TestThread t = new TestThread();
	        t.start();
	        m.addThread(t);
	        // 注册退出处理线程
	        Runtime.getRuntime().addShutdownHook(m);
	    }
	}

**运行结果**:    
	run sub thread  
	run sub thread  
	run sub thread  
	run sub thread  
	This is ShutdownHook  
	stop sun sub thread  

可以看到:**当程序退出时启动了TestThread线程，执行了定义的释放工作**。

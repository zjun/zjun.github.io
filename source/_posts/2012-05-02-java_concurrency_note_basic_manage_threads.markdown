---
layout: post
title: "Java并发编程笔记：线程管理"
date: 2012-05-02 16:52
comments: true
category: Java 
---
## 创建线程 ##
两种方法：  
1. 通过直接继承thread类，然后覆盖run()方法; 
2. 构建一个实现Runnable接口的类, 然后创建一个thread类对象并传递Runnable对象作为构造参数;

**启动线程：**  
只有调用start()方法才能启动一个新的执行线程。  

## 线程的属性 ##
通过以下方法访问线程属性：  
1. id: 			`thread.getId()`  
2. name: 		`thread.getName()`  
3. Priority: 	`thread.getPriority()`  
4. State: 		`thread.getState()`  

## 中断线程 ##
1. 在线程外调用线程的 `interrupt()` 方法；  
2. 在线程内判断 `isInterrupted()`，如果为true 则退出线程； 
3. 可以在线程内判断 `isInterrupted()`， 如果为true 则抛出 ` InterruptedException` 异常来进行中断处理；

**说明**： Thread 类有一个boolean类型的属性来表明线程是否被中断。当你调用线程的 `interrupt()` 方法，就代表你把这个属性设置为 true。 而 `isInterrupted()` 方法仅返回属性值。   

## 线程的睡眠和恢复 ##
1. 可以使用 `Thread.sleep()` 方法休眠线程，此方法接收一个整数作为参数，表示线程暂停运行的毫秒数；
2. 也可以使用一个有TimeUnit列举元素的sleep() 方法，使用线程类的 sleep() 方法让当前线程睡眠，它接收的参数单位是表示并转换成毫秒的: `TimeUnit.SECONDS.sleep(1);`  ；  
3. 可以通过 `thread.interrupt();` 中断休眠，唤醒线程；

**说明**：当调用sleep()方法， Thread 离开CPU并在一段时间内停止运行。在这段时间内，它是不消耗CPU时间的，使用可以执行其他任务。

## 等待线程终结 ##
当前线程调用其他线程的 join() 后， 会暂停当前线程，直到被调用线程执行完成。  

Java 提供2种形式的 join() 方法:  
1. `join (long milliseconds)`  
2. `join (long milliseconds, long nanos)`  
  
第一种 join() 方法, 这方法让调用线程等待特定的毫秒数。例如，如果thread1对象使用代码 thread2.join(1000), 那么线程 thread1暂停运行，直到以下其中一个条件发生：

1. thread2 结束运行  
2. 1000 毫秒过去了  

当其中一个条件为真时，join() 方法返回。

第二个版本的 join() 方法和第一个很像，只不过它接收一个毫秒数和一个纳秒数作为参数。  

## 守护线程 ##
**守护线程**: 这种线程的优先级非常低，通常在程序里没有其他线程运行时才会执行它。当守护线程是程序里唯一在运行的线程时，JVM会结束守护线程并终止程序。  

1. `setDaemon(true);` 设置守护线程  
2. 只能在 `start()` 方法之前可以调用 `setDaemon()` 方法。一旦线程运行了，就不能修改守护状态  
3. 可以使用 `isDaemon()` 方法来检查线程是否是守护线程（方法返回 true) 或者是使用者线程 (方法返回 false)  

## 处理线程中的 Unchecked exceptions ##
Java里有2种异常:

- 检查异常（**Checked exceptions**）: 这些异常必须强制捕获它们或在一个方法里的throws子句中。 例如， `IOException` 或者`ClassNotFoundException`

- 未检查异常（**Unchecked exceptions**）: 这些异常不用强制捕获它们。例如:`NumberFormatException`  

1. thread.run() 方法不接受 throws 子句。当一个非检查异常被抛出，默认的行为是在控制台写下stack trace并退出程序。
2. 实现 UncaughtExceptionHandler 接口并实现 uncaughtException() 方法;
3. thread.setUncaughtExceptionHandler(new ExceptionHandler());

{% highlight java %}  
public class ExceptionHandler implements UncaughtExceptionHandler{  
	public void uncaughtException(Thread t, Throwable e){  
		System.out.printf("An exception has been captured\n");  
		System.out.printf("Thread: %s\n",t.getId());  
		System.out.printf("Exception: %s: %s\n",e.getClass().getName(),e.getMessage());  
		System.out.printf("Stack Trace: \n");  
		e.printStackTrace(System.out); System.out.printf("Thread status: %s\n",t.getState());  
	}
{% endhighlight %}  

{% highlight java%}   
Thread thread=new Thread(task);  
thread.setUncaughtExceptionHandler(new ExceptionHandler());  
thread.start();  
{% endhighlight%}  

## 本地线程变量 ##
1. 本地线程变量 ThreadLocal;  
2. 本地线程变量为每个使用这些变量的线程储存属性值;  
3. 使用 get() 方法读取值和使用 set() 方法改变值;  
4. 如果第一次访问本地线程变量的值，没有值给当前的线程对象，那么本地线程变量会调用 initialValue() 方法来设置值给线程并返回初始值;  
5. 本地线程类还提供 remove() 方法，删除存储在线程本地变量里的值;  

## 线程组 ##
1. 创建线程组： ThreadGroup threadGroup = new ThreadGroup("Searcher");  
2. 加入线程组： Thread thread=new Thread(threadGroup, runnable );  
3. threadGroup.list() 得到关于 ThreadGroup ob对象信息；
4.  activeCount() 方法获取线程个数；  
5. enumerate() 方法获取与ThreadGroup对象关联的线程的列表；
threadGroup.enumerate(Thread[]);  
6. interrupt() 方法中断组里的其他线程： threadGroup.interrupt();  

## 处理线程组中的 Unchecked exceptions ##
1. override ThreadGroup 的 uncaughtException() 方法；  
2. 当一个非捕捉异常在线程内抛出，JVM会为这个异常寻找3种可能handlers：  
1). 找这个未捕捉的线程对象的异常handle;  
2). 在线程对象的ThreadGroup里寻找非捕捉异常的handler;  
3) 寻找默认非捕捉异常handle;  

如果没有 handlers存在, 那么 JVM会把异常的 stack trace 写入控制台并结束任务。  

## 线程工厂 ##
1. ThreadFactory 接口;  
2. 实现接口方法: newThread(), 接收 Runnable 对象作为参数并返回一个 Thread 对象;  
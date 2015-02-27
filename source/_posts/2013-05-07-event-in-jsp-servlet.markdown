---
layout: post
title: "JSP/Servlet中的事件处理"
date: 2006-07-20 14:23
comments: true
external-url: 
categories: Web
---


写过AWT或Swing程序的人一定对桌面程序的事件处理机制印象深刻：通过实现Listener接口的类可以在特定事件(Event)发生时，呼叫特定的方法来对事件进行响应。

其实我们在编写JSP/Servle程序时，也有类似的事件处理机制，所不同的是在JSP/Servlet中是在web.xml中注册Listener，由Container在特定事件发生时呼叫特定的实现Listener的类。
<!-- more -->

### 1. Servlet中的Listener和Event: ###

在JSP 2.0/Servlet 2.4中，共有八个Listener接口，六个Event类别。



- **ServletContextListener接口**

[**接口方法**] contextInitialized()与 contextDestroyed()

[**接收事件**] ServletContextEvent

[**触发场景**] 在Container加载Web应用程序时（例如启动 Container之后），会呼叫contextInitialized()，而当容器移除Web应用程序时，会呼叫contextDestroyed ()方法。

- **ServletContextAttributeListener**

[**接口方法**] attributeAdded()、 attributeReplaced()、attributeRemoved()

[**接收事件**] ServletContextAttributeEvent

[**触发场景**] 若有对象加入为application（ServletContext）对象的属性，则会呼叫attributeAdded()，同理在置换属性与移除属性时，会分别呼叫attributeReplaced()、attributeRemoved()。



- **HttpSessionListener**

[**接口方法**] sessionCreated()与sessionDestroyed ()

[**接收事件**] HttpSessionEvent

[**触发场景**] 在session （HttpSession）对象建立或被消灭时，会分别呼叫这两个方法。



- **HttpSessionAttributeListener**

[**接口方法**] attributeAdded()、 attributeReplaced()、attributeRemoved()

[**接收事件**] HttpSessionBindingEvent

[**触发场景**] 若有对象加入为session（HttpSession）对象的属性，则会呼叫attributeAdded()，同理在置换属性与移除属性时，会分别呼叫attributeReplaced()、 attributeRemoved()。



- **HttpSessionActivationListener**

[**接口方法**] sessionDidActivate()与 sessionWillPassivate()

[**接收事件**] HttpSessionEvent

[**触发场景**] Activate与Passivate是用于置换对象的动作，当session对象为了资源利用或负载平衡等原因而必须暂时储存至硬盘或其它储存器时（透过对象序列化），所作的动作称之为Passivate，而硬盘或储存器上的session对象重新加载JVM时所采的动作称之为Activate，所以容易理解的，sessionDidActivate()与 sessionWillPassivate()分别于Activeate后与将Passivate前呼叫。



- **ServletRequestListener**

[**接口方法**] requestInitialized()与 requestDestroyed()

[**接收事件**] RequestEvent

[**触发场景**] 在request（HttpServletRequest）对象建立或被消灭时，会分别呼叫这两个方法。



- **ServletRequestAttributeListener**

[**接口方法**] attributeAdded()、 attributeReplaced()、attributeRemoved()

[**接收事件**] HttpSessionBindingEvent

[**触发场景**] 若有对象加入为request（HttpServletRequest）对象的属性，则会呼叫attributeAdded()，同理在置换属性与移除属性时，会分别呼叫attributeReplaced()、 attributeRemoved()。



- **HttpSessionBindingListener**

[**接口方法**] valueBound()与valueUnbound()

[**接收事件**] HttpSessionBindingEvent

[**触发场景**] 实现HttpSessionBindingListener接口的类别，其实例如果被加入至session（HttpSession）对象的属性中，则会呼叫 valueBound()，如果被从session（HttpSession）对象的属性中移除，则会呼叫valueUnbound()，实现HttpSessionBindingListener接口的类别不需在web.xml中设定。

### 2. 如何注册Servlet中的事件 ###

实现上面这几个接口的类别，除了HttpSessionBindingListener外,必须在web.xml中向容器注册，容器才会在对应的事件发生时呼叫对应的类别，如：

	< listener >  
	< listener-class > demo.servlet.listener.CustomServletContextListener </ listener-class >  
	</ listener >

### 3. Servlet事件的应用实例 ###

看到这里,你也许会有疑问: 了解这些 listener和event 有什么用呢?我平时开发没有用到这些,一样也能完成任务啊.

不错,在日常的开发中很少用到这些事件处理的方面,但是在某些情况下使用事件处理机制却可以达到事半功倍的效果,例如下面两个例子:

[利用HttpSessionListener实现网站在线人数统计功能](http://www.blogjava.net/javaora/archive/2005/09/12/12760.html)

[使用ServletContextListener在服务器启动和关闭时创建和关闭缓存](http://www.blogjava.net/liuwentao253/archive/2006/11/26/83571.html)

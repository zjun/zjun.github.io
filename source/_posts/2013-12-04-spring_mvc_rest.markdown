---
layout: post
title: "Spring MVC 实现 REST service"
date: 2013-12-04 21:06:19 +0800
comments: true
category: Java
---

## web.xml ##

	<!-- 配置字符过滤器-->    
    <filter>  
        <filter-name>CharacterFilter</filter-name>  
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>  
        <init-param>  
            <param-name>encoding</param-name>  
            <param-value>UTF-8</param-value>  
        </init-param>  
    </filter>  
    
    <filter-mapping>  
        <filter-name>CharacterFilter</filter-name>  
        <url-pattern>/*</url-pattern>  
    </filter-mapping>  

	<!-- 指定spring配置文件位置 -->  
	<context-param>  
		<param-name>contextConfigLocation</param-name>  
		<param-value>  
			<!--加载多个spring配置文件 -->  
			classpath:spring-homebills-cfg.xml
		</param-value>  
	</context-param>  
	
	<!-- 定义SPRING监听器，加载spring -->  
	<listener>  
		<listener-class>  
			org.springframework.web.context.ContextLoaderListener
		</listener-class>  
	</listener>  
	
	<listener>  
		<listener-class>  
	org.springframework.web.context.request.RequestContextListener
		</listener-class>  
	</listener>   

    <servlet>  
	    <servlet-name>DispatcherServlet</servlet-name>  
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
	    <init-param>  
	        <param-name>contextConfigLocation</param-name>  
	        <param-value>classpath:homebills-servlet.xml</param-value>  
	    </init-param>  
	    <load-on-startup>1</load-on-startup>  
	</servlet>  
	    
	<servlet-mapping>  
	    <servlet-name>DispatcherServlet</servlet-name>  
	    <url-pattern>/</url-pattern>  
	</servlet-mapping>  
<!--more-->
## application-servlet.xml ##
{% highlight xml %}  
<?xml version="1.0" encoding="UTF-8"?>  
<beans  xmlns="http://www.springframework.org/schema/beans"  
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
        ">

    <!--
        指定使用注解方式配置,配置自动扫描的包名,
        base-package指定自己应用中控制器所在的包目录
        <context:component-scan/> 扫描指定的包中的类上的注解，常用的注解有： 
        @Controller 声明Action组件
        @Service 声明Service组件
        @Service("myMovieLister") 
        @Repository 声明Dao组件
        @Component 泛指组件, 当不好归类时. 
        @RequestMapping("/menu") 请求映射 
        @Resource用于注入，( j2ee提供的 ) 默认按名称装配，
        @Resource(name="beanName") 
        @Autowired用于注入，(srping提供的) 默认按类型装配 
        @Transactional(rollbackFor={Exception.class}) 事务管理 
        @ResponseBody @Scope("prototype")设定bean的作用域
    -->
    <context:component-scan base-package="com.zjun.homebills.controller" />
    
    <!-- 默认的注解映射的支持 -->
    <!-- JSR-303 support will be detected on classpath and enabled automatically -->
    <mvc:annotation-driven/>
    
    <!-- http://static.springsource.org/spring/docs/3.0.0.RC3/reference/html/ch05s07.html --><!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 配置视图层 使用jstl标签 -->
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <!-- 定义视图前缀格式 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 定义视图后缀格式 -->
        <property name="suffix" value=".jsp" />
    </bean>  
</beans>  
{% endhighlight %}  

## Controler ##
{% highlight java %}
package com.zjun.homebills.controller;  

import java.util.List;  

import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.validation.BindingResult;  
import org.springframework.validation.annotation.Validated;  
import org.springframework.web.bind.annotation.PathVariable;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RequestMethod;  

import com.zjun.homebills.domain.Account;  
import com.zjun.homebills.service.AccountService;  

@Controller  
@RequestMapping(value = "/account")  
public class AccountController {  

	@Autowired
	AccountService accountService;

	/**
	 * 
	 * 
	 * Description: 构建REST风格 /account/list的GET请求时才执行该方法的操作RequestMethod.GET表示
	 * 只处理GET请求
	 * 
	 * @param model
	 *            用于上下文参数传递
	 * @return 视图页面 account/list 结合user-servlet.xml中配置的视图模型匹配视图页面
	 *         实例中方法返回表示/WEB-INF/jsp/account/list.jsp页面
	 * 
	 */
	@RequestMapping(value = "/list", method = RequestMethod.GET)
	public String list(Model model) {
		List<Account> accounts = accountService.getAll();
		model.addAttribute("accounts", accounts);
		return "account/list";
	}

	@RequestMapping(value = "/add", method = RequestMethod.GET)
	public String add(Model model) {
		model.addAttribute("account", new Account()); // 开启ModelDriven
														// 跳转到增加页面时使用该Model
		return "account/add";
	}

	/**
	 * 
	 * Description: 添加操作 请求/account/add form表单提交时使用的post请求调用该方法
	 * 
	 * @param user
	 *            添加的User对象
	 * @param br
	 *            验证绑定
	 * @return 视图页面 添加成功 请求重定向redirect:/account/list 表示执行操作结束后请求定向为/user/users
	 *         添加失败 页面转到/WEB-INF/jsp/add.jsp 这里有验证绑定,将在视图页面展示验证错误信息
	 * @throws Exception
	 * 
	 */
	@RequestMapping(value = "/add", method = RequestMethod.POST)
	public String add(@Validated Account account, BindingResult br)
			throws Exception {
		// 需要说明的是BindingResult形参一定要跟@Validated修饰的形参后面写验证
		if (br.hasErrors()) { // 如果有错误,直接跳转到添加视图
			return "account/add"; // 服务端跳转 该跳转会自动在前面增加 forward
		}
		accountService.save(account);
		return "redirect:/account/list"; // 客户端跳转 使用 redirect
	}

	/**
	 * 
	 * 
	 * Description: 预更新操作根据用户名查询用户信息 然后数据交给携带体 展示到视图 REST风格: /更新的用户的用户名/update
	 * 
	 * @param username
	 * @PathVariable修饰 表示形参同URL中的请求参数
	 * @param model
	 *            携带数据的Model
	 * @return 视图页面/WEB-INF/jsp/account/update页面
	 * 
	 */
	@RequestMapping(value = "/{id}/update", method = RequestMethod.GET)
	public String update(@PathVariable Integer id, Model model) {
		System.out.println("获取到传入的参数值为:" + id);
		Account account = accountService.findById(id);
		model.addAttribute(account);
		return "account/update";
	}

	/**
	 * 
	 * 
	 * Description: 真正更新的操作 REST风格： /更新的id/update
	 * 
	 * @param username
	 *            带更新的用户的用户名
	 * @param user
	 *            带更新的用户的信息对象 @Validated修饰表示信息需要被验证
	 * @param br
	 *            验证信息绑定对象 必须紧跟在待验证的信息形参后面
	 * @return 视图页面 更新成功 请求重定向 /account/list 更新失败 转到/WEB-INF/jsp/account/update.jsp页面
	 * 
	 */
	@RequestMapping(value = "/{id}/update", method = RequestMethod.POST)
	public String update(@PathVariable Integer id, @Validated Account account,
			BindingResult br) {
		if (br.hasErrors()) { // 如果有错误,直接跳转到修改视图
			return "account/update";
		}
		accountService.update(account);
		return "redirect:/account/list";
	}

	/**
	 * 
	 * 
	 * Description: 删除操作 REST风格:/删除的id/delete
	 * 
	 * @param username
	 *            删除的用户名 类似表主键,可以标记到整个记录信息
	 * @return 视图页面 请求重定向到 /account/list
	 * 
	 */
	@RequestMapping(value = "/{id}/delete", method = RequestMethod.GET)
	public String delete(@PathVariable Integer id) {
		System.out.println("获取到传入的参数值为:" + id);
		accountService.deleteById(id);
		return "redirect:/account/list";
	}  
}  
 
{% endhighlight %}  

## JSP ##
{% highlight java %}  
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path;
%>

<!DOCTYPE HTML>
<html>
  <head>
    <base href="<%=basePath%>">
    <title><%=basePath%></title>
  </head>
  
  <body>
    <h2>账户</h2>   
    <p><a href="<%=basePath %>/account/add">添加账户</a></p>
    <table>
    	<thead>
    		<tr><th>ID</th><th>名称</th><th>操作</th></tr>
    	</thead>
    	<tbody>
    		<c:forEach items="${accounts}" var="account">
    		<tr>
    			<td>${account.id }</td>
    			<td>${account.name }</td>
    			<td>
    	 			<a href="<%=basePath %>/account/${account.id }/update">修改</a>
        			<a href="<%=basePath %>/account/${account.id }/delete">删除</a>
       			</td>
    		</tr>
		    </c:forEach>
    	</tbody>
    </table>
    
  </body>  
</html>  
{% endhighlight %}
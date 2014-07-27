---
layout: post
title: "常用JDBC连接字符串"
date: 2006-07-27 14:45
comments: true
external-url: 
categories: Java
---
常用数据库的JDBC连接字符串：
<!-- more -->

###  1.MySQL ###
{% highlight sql %} 
	Class.forName(  " org.gjt.mm.mysql.Driver "  );
	Connection conn  =  DriverManager.getConnection(  " jdbc:mysql://MyDbComputerNameOrIP:3306/myDatabaseName " , sUsr, sPwd );
{% endhighlight %}

### 2.Microsoft SQLServer ###
{% highlight sql %} 
	Class.forName(  " com.microsoft.jdbc.sqlserver.SQLServerDriver "  );
	Connection conn  =  DriverManager.getConnection(  " jdbc:microsoft:sqlserver://MyDbComputerNameOrIP:1433;databaseName=master " , sUsr, sPwd );
{% endhighlight %}

### 3.Oracle ###
{% highlight sql %} 
	Class.forName(  " oracle.jdbc.driver.OracleDriver "  );
	Connection conn  =  DriverManager.getConnection(  " jdbc:oracle:thin:@MyDbComputerNameOrIP:1521:ORCL " , sUsr, sPwd );
{% endhighlight %}

### 4.DB2 ###
{% highlight sql %} 
	Class.forName( " com.ibm.db2.jdbc.net.DB2Driver " );
	String url = " jdbc:db2://192.9.200.108:6789/SAMPLE "
	Connection conn  =  DriverManager.getConnection( url, sUsr, sPwd );
{% endhighlight %}

### 5.ODBC ###
{% highlight sql %} 
	Class.forName(  " sun.jdbc.odbc.JdbcOdbcDriver "  );
	Connection conn  =  DriverManager.getConnection(  " jdbc:odbc: "   +  sDsn, sUsr, sPwd );
{% endhighlight %}

### 6.PostgreSQL ###
{% highlight sql %} 
	Class.forName(  " org.postgresql.Driver "  );
	Connection conn  =  DriverManager.getConnection(  " jdbc:postgresql://MyDbComputerNameOrIP/myDatabaseName " , sUsr, sPwd );
{% endhighlight %}
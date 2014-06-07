---
layout: post
title: "MySQL 修改 root 密码"
date: 2013-09-18 21:06:19 +0800
comments: true
category: 数据库
---
## 方法1： 用SET PASSWORD命令 ##
{% highlight console %}   
mysql -u root  
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');  
{% endhighlight %}
<!--more-->
## 方法2：用mysqladmin ##
{% highlight console %}   
mysqladmin -u root password "newpass"  
{% endhighlight %}  

如果root已经设置过密码，采用如下方法  

{% highlight console %}   
mysqladmin -u root password oldpass "newpass"  
{% endhighlight %}

## 方法3： 用UPDATE直接编辑user表 ##
{% highlight console %}   
mysql -u root  
mysql> use mysql;  
mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';  
mysql> FLUSH PRIVILEGES;    
{% endhighlight %}  

## 方法4： 在丢失root密码的时候，可以这样 ##
{% highlight console %}   
mysqld_safe --skip-grant-tables&  
mysql -u root mysql  
mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';  
mysql> FLUSH PRIVILEGES;    
{% endhighlight %}  

---------------------------------------

经测试，方法三修改成功，其他三种方法没有测试。

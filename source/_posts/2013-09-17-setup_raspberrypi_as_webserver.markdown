---
layout: post
title: "把Raspberry Pi打造成 Web Server"
date: 2013-09-17 21:06:19 +0800
comments: true
category: Others
---

## Setup Raspberry Pi ##
### 安装 ###
理论上 OS 可以是任何支持 ARM v6 的 Linux 发行版，推荐使用官方的 wheezy 系统，因为这是针对树莓派定制并优化了的 Debian。  
下载地址：[http://www.raspberrypi.org/downloads](http://www.raspberrypi.org/downloads "http://www.raspberrypi.org/downloads")  
解压之后得到一个 .img 的镜像文件，比如 2013-07-26-wheezy-raspbian.img.  

在Windows下使用工具：[win32diskimager](http://sourceforge.net/projects/win32diskimager/) 把镜像文件写入 SD 卡。  

将刻好的 SD 卡插入树莓派的 SD 卡插槽，接上键盘和显示器。然后开机，也就是插上电源，树莓派没有电源开关，插电即开机。拔出电源就是关机，不过最安全的方式是使用 `sudo shutdown -h now` 或 `sudo halt` 命令正确地关闭系统，然后再拔电源。 重启使用 `sudo reboot` 命令。
<!--more--> 
### 设置 ###
第一次开机正常启动后，看到的是 Raspi-config 界面， 你可以使用键盘做一些基本的系统设置:  


- 先简单地设置一下地区、时区、键盘。注意默认的键盘布局（layout）设置是 English(UK) （因为是英国人发明的），在 Others 中把它改为 English(US)，否则 # 号的位置不对。
- 接着还可以修改默认用户 pi 的密码，官方系统的默认账号是 pi / raspberry 。
- 在 ssh 设置中启用 ssh 服务（默认已启用），这样以后就可以 ssh 远程控制了。
- 在 boot_behaviour 设置中，设置开机是否自动进入桌面环境（默认进入），选择否则只进入命令行模式。建议关闭桌面，需要的时候使用 `startx` 命令就可以进入。系统提供的桌面是 LXDE ，有一些基本功能。
- expand_rootfs 设置是让你的系统扩展到全部 SD 卡空间，默认没有使用全部 SD 卡空间，所以请执行这个选项。
- 最后，执行 Finish 完成设置，重启系统。

重启之后就不会再出现这个设置界面了，但你随时可以使用 `sudo raspi-config` 命令再次使用这个设置工具。

装好 OS 后，建议先升级一下系统，最新的更新可能会提高系统的性能：  

{% highlight console %}   
sudo apt-get update      
sudo apt-get upgrade  
{% endhighlight %} 

一些在设置系统时常用到的命令：  

- 关机: {% highlight console %} sudo shutdown -h now{% endhighlight %} 或 {% highlight console %} sudo halt{% endhighlight %}  
- 重启: {% highlight console %}sudo reboot{% endhighlight %}   
- 查看IP地址： {% highlight console %}ifconfig{% endhighlight %}  
- Set Up SSH:  
{% highlight console %}  
sudo mv /boot/boot_enable_ssh.rc /boot/boot.rc   
sudo shutdown -r now
{% endhighlight %}   

- Install FTP  
{% highlight console %}  
sudo chown -R pi /var/www  -- Take ownership of the web root  
sudo apt-get install vsftpd -- install vsftpd  
sudo service vsftpd restart -- restart vsftpd
{% endhighlight %}  

到这里，**就装好了一台 RPi**

## Setup Tomcat ##
### 安装 ###
- install tomcat server  
{% highlight console %}   
sudo apt-get install tomcat7  
{% endhighlight %}   

### 设置 ###
- link conf and webapps folders  
{% highlight console %}   
cd /usr/share/tomcat7  
sudo rm -r webapps  
sudo ln -s /var/lib/tomcat7/conf conf  
sudo ln -s /var/lib/tomcat7/webapps webapps   
{% endhighlight %}     
 
- give full access to webapps folder  
{% highlight console %}   
sudo chmod 777 /usr/share/tomcat7/webapps  
{% endhighlight %}   

- copy war file to server webapps folder (from local pc)  
{% highlight console %}   
scp <path>/<file>.war <username>@<ip>:/usr/share/tomcat7/webapps/<file>.war   
{% endhighlight %}    

- restart tomcat server  
{% highlight console %}  
sudo service tomcat7 restart    
{% endhighlight %}      

### 管理 ###
 
- tomcat server commands  
{% highlight console %}  
sudo service tomcat7 [<start> <stop> <restart>]
{% endhighlight %}     

- Where to find the web app log files:  
{% highlight console %}   
/var/log/tomcat
{% endhighlight %}     

## Setup MySQL ##
### 安装 ###
- Install MySQL server  
{% highlight console %}   
sudo apt-get install mysql-server mysql-client php5-mysql
{% endhighlight %}   

### 管理 ###
- Manage MySQL service  
{% highlight console %}    
sudo service mysql start/stop/restart  
{% endhighlight %}   

### 设置 ###
#### 远程访问 ####
By default remote access to the MySQL database server is disabled for security reasons.To allow remotely access to the database server from the web server or home, follow below quick steps.  

**1.Edit my.cnf File**    
- If you are using Debian Linux file is located at **/etc/mysql/my.cnf** location   
- If you are using Red Hat Linux/Fedora/Centos Linux file is located at **/etc/my.cnf** location   
- If you are using FreeBSD you need to create a file **/var/db/mysql/my.cnf**
Edit **/etc/my.cnf**, run:  
{% highlight console %}    
vi /etc/my.cnf  
{% endhighlight %}   
Once file opened, locate line that read as follows  

[mysqld]  
Make sure line skip-networking is commented (or remove line) and add following line  
{% highlight console %}  
bind-address=YOUR-SERVER-IP  
{% endhighlight %}   
For example, if your MySQL server IP is `65.55.55.2` then entire block should be look like as follows:
{% highlight console %}  
[mysqld]  
user            = mysql  
pid-file        = /var/run/mysqld/mysqld.pid  
socket          = /var/run/mysqld/mysqld.sock  
port            = 3306  
basedir         = /usr  
datadir         = /var/lib/mysql  
tmpdir          = /tmp  
language        = /usr/share/mysql/English  
bind-address    = 65.55.55.2  
#  skip-networking  
....  
..  
....  
{% endhighlight %}   

Where,

- **bind-address** : IP address to bind to.   
- **skip-networking** : Don’t listen for TCP/IP connections at all. All interaction with mysqld must be made via Unix sockets. This option is highly recommended for systems where only local requests are allowed. Since you need to allow remote connection this line should be removed from my.cnf or put it in comment state.  

2.Grant access to remote IP address    
- Connect to mysql server:
{% highlight console %}  
$ mysql -u root -p mysql  
{% endhighlight %}   

- Grant access to a new database  
If you want to add a new database called foo for user bar and remote IP `202.54.10.20` then you need to type the following commands at mysql> prompt:
{% highlight console %}  
mysql> CREATE DATABASE foo;  
mysql> GRANT ALL ON foo.* TO bar@'202.54.10.20' IDENTIFIED BY 'PASSWORD';  
{% endhighlight %}   

-  Grant Access To An Existing Database  
{% highlight console %}   
mysql> update db set Host='202.54.10.20' where Db='webdb';  
mysql> update user set Host='202.54.10.20' where user='webadmin';   
{% endhighlight %}   

- Test connection
{% highlight console %}   
$ mysql -u webadmin –h 65.55.55.2 –p
{% endhighlight %}   

Where  
- u webadmin: webadmin is MySQL username   
- h IP or hostname: 65.55.55.2 is MySQL server IP address or hostname (FQDN)   
- p Prompt for password  

You can also use the telnet or nc command to connect to port 3306 for testing purpose:
{% highlight console %}   
$ echo X | telnet -e X 65.55.55.2 3306  
{% endhighlight %}   

OR  
{% highlight console %}   
$ nc -z -w1 65.55.55.2 3306  
{% endhighlight %}   

Sample outputs:
{% highlight console %}   
Connection to 65.55.55.2 3306 port [tcp/mysql] succeeded!  
{% endhighlight %}   

#### MySQL 中文乱码 ###

MySQL中默认字符集的设置有四级:服务器级，数据库级，表级 。最终是字段级 的字符集设置。注意前三种均为默认设置，并不代码你的字段最终会使用这个字符集设置。所以我们建议要用`show create table table ;` 或`show full fields from tableName;` 来检查当前表中字段的字符集设置。

**1.查看默认的编码格式:**  
{% highlight console %}   
mysql> show variables like "%char%";
 
+--------------------------+---------------+  
| Variable_name | Value |  
+--------------------------+---------------+  
| character_set_client | gbk |  
| character_set_connection | gbk |  
| character_set_database | utf8 |  
| character_set_filesystem | binary |  
| character_set_results | gbk |  
| character_set_server | utf8 |  
| character_set_system | utf8 |  
+--------------------------+-------------+  
{% endhighlight %}   

注：以前2个来确定,可以使用`set names utf8`,`set names gbk`设置默认的编码格式;
{% highlight console %}   
mysql> set names utf8;   **有时候这一句很关键！**
{% endhighlight %}   

执行SET NAMES utf8的效果等同于同时设定如下：  
{% highlight console %}   
SET character_set_client='utf8';  
SET character_set_connection='utf8';  
SET character_set_results='utf8';  
{% endhighlight %}   

**2.数据库表中字段的字符集设置 :** 
 
`show create table TableName` 或 `show full columns from tableName`  

{% highlight console %}   
mysql> show create table t1;  
+-------+------------------------------------  
| Table | Create Table                       
+-------+------------------------------------  
| t1    | CREATE TABLE `t1` (  
  `id` int(11) NOT NULL,  
  `c1` varchar(30) DEFAULT NULL,  
  PRIMARY KEY (`id`)      
) ENGINE=InnoDB DEFAULT CHARSET=gbk |  
+-------+------------------------------------  
1 row in set (0.00 sec)   
                        
mysql> show full columns from t1;  
+-------+-------------+----------------+------+-----+-  
| Field | Type        | Collation      | Null | Key |   
+-------+-------------+----------------+------+-----+-  
| id    | int(11)     | NULL           | NO   | PRI |   
| c1    | varchar(30) | gbk_chinese_ci | YES  |     |   
+-------+-------------+----------------+------+-----+-  
2 rows in set (0.00 sec)   
mysql>   
{% endhighlight %}   
  
**3.当前联接系统参数**  
{% highlight console %}   
show variables like 'char%'    
>+--------------------------+----------------  
| Variable_name            | Value  
+--------------------------+----------------  
| character_set_client     | gbk  
| character_set_connection | gbk  
| character_set_database   | latin1  
| character_set_filesystem | binary  
| character_set_results    | gbk  
| character_set_server     | latin1  
| character_set_system     | utf8  
| character_sets_dir       | C:/Program File  
+--------------------------+----------------  
8 rows in set (0.00 sec)   
mysql>   
{% endhighlight %}   
 
**4.中文，请确保 表中该字段的字符集为中文兼容：**  
{% highlight console %}   
big5     | Big5 Traditional Chinese  
gb2312   | GB2312 Simplified Chinese  
gbk      | GBK Simplified Chinese  
utf8     | UTF-8 Unicode   
{% endhighlight %}   

**5.确保联接参数与这个字段字符集一致,你可以用 `set name 'charsetname';`**  
比如， `set name 'gbk';`  
 这条命令会同时修改  
{% highlight console %}   
character_set_client  
character_set_connection  
character_set_results  
{% endhighlight %}   

(如果你的这架MySQL中都为中文，则你可以在`my.ini`或`my.cnf`中加上或修改这个参数, 参数文件修改后需重启MySQL服务)
{% highlight console %}   
[mysql]
default-character-set=gbk 
{% endhighlight %}   

**Update on 2013-9-20**  
上面提到的方法都没有解决中文乱码的问题，最终采用在connection URL 中加入编码参数的方法，问题解决，终于可以正常存入和显示中文了。

{% highlight console %}   
jdbc:mysql://serverhost:3306/database?useUnicode=true&characterEncoding=utf-8
{% endhighlight %}   

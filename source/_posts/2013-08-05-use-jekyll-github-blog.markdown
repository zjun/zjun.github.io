---
layout: post  
title: "用Jekyll+Github+Markdown来写Blog"  
date: 2013-08-05 21:06:19 +0800
comments: true
category: Others
---
继续折腾，之前使用 Octpress 来写Blog,但用了一段时间以后还是觉得笨重，发布一篇新文章需要把所有页面重新发布一遍，最终还是决定使用定制更灵活的Jekyll.下面就记录一下这次迁移的步骤和遇到的问题。

以下过程在Windows7下进行：
<!--more-->
## 安装 Ruby ##
在Windows环境下推荐安装[RailsInstaller](http://railsinstaller.org/).
## 安装 Git ##
1. 在windows下安装[msysgit](http://code.google.com/p/msysgit/downloads/list).
2. 设置`SSH keys`  
  首先备份已有的keys  
{% highlight console %} 
$ ls  
config  id_rsa  id_rsa.pub  known_hosts  
$ mkdir key_backup
$ cp id_rsa* key_backup  
$ rm id_rsa*  
{% endhighlight %}  

  生成新的 SSH keys  

{% highlight console %}
$ ssh-keygen -t rsa -C "邮件地址@youremail.com"    
Generating public/private rsa key pair.  
Enter file in which to save the key (/Users、your_user_directory/.ssh/id_rsa):<回车就好>  
{% endhighlight %}

输入GitHub的密码  

{% highlight console %}
Enter passphrase (empty for no passphrase):<输入加密串>  
Enter same passphrase again:<再次输入加密串>  
{% endhighlight %}

3. 添加SSH Key到GitHub：登录GitHub，在主页上点击设置按钮，选择`SSH Keys`项，把复制的内容粘贴进去，然后点击Add Key按钮即可。  
  可以输入下面的命令，看看设置是否成功，`git@github.com`的部分不要修改：
{% highlight console %}  
$ ssh -T git@github.com  
{% endhighlight %}
4. 设置GitHub账号信息：  
{% highlight console %}
$ git config --global user.name "你的名字"  
$ git config --global user.email "your_email@youremail.com"  
{% endhighlight %}
## 安装jekyll ##
1. 把淘宝的镜像加到gem的镜像列表里 
{% highlight console %} 
gem sources --remove http://rubygems.org/  
gem sources -a http://ruby.taobao.org/  
{% endhighlight %}
  用`gem sources -l`看看现在源列表  
{% highlight console %} 
*** CURRENT SOURCES ***

http://ruby.taobao.org   
{% endhighlight %} 
2. 如果是上面这样就可以安装jekyll了
{% highlight console %} 
gem install jekyll  
{% endhighlight %} 
  安装`rdiscount`来解析Markdown文档  
{% highlight console %}
gem install rdiscount kramdown  
{% endhighlight %} 
## 建立github pages ##
1. 登录GitHub,建立一个名为 `USERNAME.github.com` 的 repositories，（注意：这里的USERNAME要与你的GitHub用户名一致）。  
2. 建立本地Blog目录：
{% highlight console %}
$ mkdir USERNAME.github.com  
$ cd USERNAME.github.com  
$ git init  
$ git add .  
$ git commit -m 'init commit'  
$ git remote add origin git@github.com:USERNAME/USERNAME.github.com.git  
$ git push  -u origin master
{% endhighlight%}
3. 等待大约10分钟，就可以通过　`USERNAME.github.io` 访问新的blog了
4. 使用脚本来新建文章  
  在本地的Blog目录下新建一个 `Rakefile` 文件，然后将[Rakefile content](https://github.com/plusjade/jekyll-bootstrap/blob/master/Rakefile)拷贝到文件中.  
  现在就可以在命令行中新建一篇文章了： 
{% highlight console %} 
rake post title="titlename"  
{% endhighlight%}
  执行该命令后，将会在`_post`的文件夹下生成形式为`YYYY-MM-dd-titlename.md`的文件，并且会提供好一些附加信息。其中，titlename就是命令中的title后引号中的值。

## 代码高亮 Pygments ##
Jekyll通过Pygments支持对代码/语法的高亮显示。  
[Pygments on github](https://github.com/mojombo/jekyll/wiki/Liquid-Extensions)  
[Pygments homepage](http://pygments.org/)  
[Supported languages list](http://pygments.org/languages/)  
1. 安装 Python:
{% highlight console %}
$ python --version  
Python 2.7.2
{% endhighlight%}
2. 安装setuptools： 下载并运行 [ez_setup.py](https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py)  
{% highlight console %}
python ez_setup.py
{% endhighlight%}
3. 安装 pygments：
{% highlight console %}
$ sudo easy_install Pygments
{% endhighlight%}
4. 生成CSS文件
{% highlight console %}
$ pygmentize -S default -f html > highlight.css
{% endhighlight%}
5. 在`config.yaml`中enable `pygmentize`:
{% highlight console %}
pygments: true
{% endhighlight%}
6. Include pygments.css
在 `/_layouts/default.html` 中include highlight.css:
{% highlight html %}
<link rel="stylesheet" href="/highlight.css">
{% endhighlight %}

### **Note: 遇到的问题** ###
在文章中使用`highlight`后每次`jekyll build`时都会遇到异常：
{% highlight console %}
C:/Ruby193/lib/ruby/gems/1.9.1/gems/posix-spawn-0.3.6/lib/posix/spawn.rb:162: warning: cannot close fd before spawn
←[31m  Liquid Exception: No such file or directory - /bin/sh in 2013-08-05-test-post.md←[0m
{% endhighlight %}   

在这里找到了原因和解决方法：[Jekyll on Windows: Pygments not working](http://stackoverflow.com/questions/17364028/jekyll-on-windows-pygments-not-working)

原来是因为 jekyll 和 Pygments 只能和 Pygments.rb 0.5.0 配合使用，但是我本机装的是 Pygments.rb 0.5.2，卸载后重新安装Pygments.rb 0.5.0，再重新build,异常消失。
{% highlight console %}
gem uninstall pygments.rb --version "=0.5.2"
gem install pygments.rb --version "=0.5.0"
{% endhighlight %}

***2013-12-20 更新：***  
Jekyll现在已经支持用[Redcarpet](https://github.com/vmg/redcarpet)来处理markdown，并且在1.1以后的版本中， `Jekyll new` 新建的站点也将默认采用Redcarpet。目前只要在`_config.yml`中进行类似如下相关设置：  

	markdown: redcarpet
	redcarpet:
	    extensions: ["fenced_code_blocks", "autolink", "tables", "strikethrough"]

代码高亮均可以采用如下格式：  
``` ruby    
require 'rubygems'    

def foo    
  puts 'foo'    
end  
```  
  
显示效果如下：  
>``` ruby  
require 'rubygems'  
>
def foo  
  puts 'foo'  
end  
>
>#comment  
>```  

## 常用命令 ##
{% highlight console %}
$ git clone git@github.com:username/username.github.com.git //本地如果无远程代码，先做这步，不然就忽略

$ cd .ssh/username.github.com //定位到你blog的目录下

$ git pull origin master //先同步远程文件，后面的参数会自动连接你远程的文件

$ git status //查看本地自己修改了多少文件

$ git add . //添加远程不存在的git文件

$ git commit * -m "what I want told to someone"

$ git push origin master //更新到远程服务器上
{% endhighlight%}	

## 参考资料 ##
[使用Github Pages建独立博客](http://beiyuu.com/github-pages/#github)  

[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)  

[写作环境搭建(git+github+markdown+jekyll)](http://site.douban.com/196781/widget/notes/12161495/note/264946576/)  

[Github Pages极简教程](http://yanping.me/cn/blog/2012/03/18/github-pages-step-by-step/)  

[Going Static with Jekyll & Deploying via GitHub for Free](http://web-design-weekly.com/2013/03/04/going-static-with-jekyll-deploying-via-github-for-free/)  

[通过GitHub Pages建立个人站点（详细步骤）](http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html)  

[Jekyll的代码高亮](http://ztpala.com/2011/10/27/code-highlighting-jekyll/)

---
layout: post
title: "使用Octopress写博客"
date: 2013-05-03 21:06:19 +0800
comments: true
categories: 工具
---
`Octopress`是利用`Jekyll`博客引擎开发的一个博客系统，生成的静态页面能够很好的在github page上展现。号称是hacker专属的一个博客系统(`A blogging framework for hackers.`)
本文就来介绍一下如何利用Octopress搭建一个Github博客。本文需要读者熟悉一些shell命令，并掌握基本的git操作。

<!--more-->

## 1. 安装 Ruby
Octopress需要Ruby环境，RVM(Ruby Version Manager)负责安装和管理Ruby的环境。所以我们先在终端输入如下命令，来安装RVM：  
```console  
curl -L https://get.rvm.io | bash -s stable --ruby  
```  
接着安装Ruby 1.9.3，在终端依次运行如下命令：  
```console   
rvm install 1.9.3  
rvm use 1.9.3  
rvm rubygems latest  
```  
完成上面的操作之后，运行`ruby --version`确认ruby 1.9.3环境已经安装好了。

参考: [Installing Ruby With RVM](http://octopress.org/docs/setup/rvm/)  

## 2. 安装Octopress
在安装Octopress之前，需要安装有`git`了，在终端输入`git --version`，可以看到电脑中的git版本，如果没有显示相关内容，请先安装[git](http://git-scm.com/)。   

git安装之后，利用git命令将octopress从github上clone到本机，如下命令：
```console
git clone git://github.com/imathis/octopress.git octopress  
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).  
ruby --version  # Should report Ruby 1.9.3  
```  

在安装相关依赖项之前，最好把`gem sources`改成淘宝的resource：  
>由于国内网络原因（你懂的），导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败。所以你会与遇到 `gem install rack` 或 `bundle install` 的时候半天没有响应，具体可以用 `gem install rails -V` 来查看执行过程。

通过一下命令修改gem sources:  
```    
$ gem sources --remove https://rubygems.org/  
$ gem sources -a https://ruby.taobao.org/  
$ gem sources -l  
*** CURRENT SOURCES *** 

https://ruby.taobao.org  
# 请确保只有 ruby.taobao.org  
$ gem install rails  
```   

参考： [RubyGems 镜像 - 淘宝网](http://ruby.taobao.org/)  

接着安装相关依赖项：  
```console
gem install bundler  
rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command  
bundle install  
```

最后安装默认的Octopress 主题。  
```
rake install
```
也可以安装定制的主题，我安装的是这个主题: [octostrap3](https://github.com/kAworu/octostrap3)   
```  
  % cd octopress   
  % git clone https://github.com/kAworu/octostrap3 .themes/octostrap3  
  % rake 'install[octostrap3]'  
  % rake generate  
```  

参考：[Octopress Setup](http://octopress.org/docs/setup/)  

## 3. 配置Octopress
Octopress的作者已经尽量让配置简化了。大多数情况下只需要配置`_config.yml`和`Rakefile`文件即可。其中Rakefile是跟博客部署相关，一般情况下并不需要修改这个文件，除非使用了rsync。

config.yml是博客重要的一个配置文件，在config.yml文件中有三大配置项：`Main Configs`、`Jekyll & Plugins`和`3rd Party Settings`。

一般，该文件中其中url是必须要填写的，这里的url是在github上创建的一个仓库地址，具体请看第四步中创建的地址。另外再修改一下`title`、`subtitle`和`author`，根据需求，在开启一些第三方组件服务。

关于_config.yml文件中的更多内容，请看这里的内容：[Configuring Octopress](http://octopress.org/docs/configuring/)

建议：最好把里面的google+ 和 twitter相关的信息全部删掉，否则由于GFW的原因，将会造成页面load很慢。

## 4. 部署到GitHub上
Github的`[Page service](http://pages.github.com/)`可以免费托管博客，并且还可以自定义域名。

首先需要在GitHub上`[创建一个仓库](https://github.com/new)`，并将仓库名称按照这样的方式进行命名：`username.github.com`。等后面配置完毕之后，我们就可以在浏览器中使用页面地址`http://username.github.com`来访问我们的博客。一般来说，博客的源码放到source分支下，生成的内容提交到master分支。

创建好仓库之后，我们需要利用octopress的一个`配置rake任务`来自动配置上面创建的仓库：可以让我们方便的部署GitHub page。在终端输入如下命令：
```
$ rake setup_github_pages   
```   
上面的命令会做一些事情(详细介绍看下面给出的参考链接)。其中最主要的就是创建一个`_deploy`目录，目录用来存放部署到master分支的内容。期间会要求你输入仓库的url，根据提示，进行输入即可。

完成上面的命令之后，我们就可以生成博客并真正的部署到仓库中了。执行如下命令：
```
rake generate  
rake deploy  
```
上面的命令首先生成博客文件，并将生成的博客文件拷贝到`_deploy/`目录下，然后将这些内容添加到git中，并commit和push到仓库的master分支。

现在可以访问`http://username.github.com`了。注意：有时候可能会有延时，要等几分钟才能打开。

至此，我们的博客已经完成基本的部署，不过博客的source需要单独提交，执行如下命令就可以将source提交到仓库的source分支下。
```
$ git add .  
$ git commit -m 'Initial source commit'  
$ git push origin source  
```
如果在部署到仓库之前，需要先预览一下博客，可以在终端输入`rake preview`命令，然后就能在浏览器中进行本地预览访问了：`http://127.0.0.1:4000/`或`http://localhost:4000/`，效果跟仓库中的一样。
参考：[Deploying to Github Pages](http://octopress.org/docs/deploying/)  

### Update:
推送是出现下面错误：
>\## Pushing generated _deploy website  
>To git@github.com:GinSmile/ginsmile.github.io.git  
> ! [rejected]        master -> master (non-fast-forward)  
>error: 无法推送一些引用到 'git@github.com:GinSmile/ginsmile.github.io.git'  
>提示：更新被拒绝，因为您当前分支的最新提交落后于其对应的远程分支。  
>提示：再次推送前，先与远程变更合并（如 'git pull ...'）。详见  
>提示：'git push --help' 中的 'Note about fast-forwards' 小节。    

在这里找到解决办法： [Octopress博客技巧](http://ginsmile.github.io/blog/2013/08/29/octopressbo-ke-ji-qiao/#.U5M_X3WSxhE)  

修改RakeFile文件（Octopress目录下）来强制推送，这里参考了stackoverflow的解决办法
```   
system "git push origin #{deploy_branch}"   
```   
修改为：  
```   
system "git push origin +#{deploy_branch}"   
```

注意：此方法使强制推送，会删除GitHub上的所有的本地分支没有的提交。记住推送完毕后再把RakeFile文件改回来。

## 5. 写博客
Octopress中文章存储在`source/_posts`目录下，并且需要按照Jekyll的命名规范对文章进行命名：`YYYY-MM-DD-post-title.markdown`。文章的名字会被当做url的一部分，而其中的日期用于对博文的区分和排序。

通过一下命令，可以正确的按照命名规范创建一个博文，并且在博文中会附带常用的一些yaml元数据。  
```
rake generate  
rake deploy    
```   
其中title为博文的文件名，创建出来的文件默认是markdown格式。上面的命令会创建出这样一个文件：`source/_posts/2013-05-03-title.markdown`。打开这个文件，可以看到里面有如下一些内容了(告诉Jekyll博客引擎如何处理博文和页面)：  
```
---  
layout: post  
title: "title"
date: 2013-05-03 16:36  
comments: true  
categories:   
---  
```
接着就可以在这个文件中写博文了。

完成之后，我们可以预览和部署博文。下面是创建并部署博文的一个完整过程：
```
$ rake new_post["New Post"]  
$ rake generate  
$ git add .  
$ git commit -am "Some comment here."   
$ git push origin source  
$ rake deploy  
```

参考：[Blogging Basics](http://octopress.org/docs/blogging/)  

## 6. 在另一台电脑上重建Octopress
Octopress的repositories中有两个分支：`source` and `master`.

其中`source`分支存储的是生成静态文件的原始文件，`master`存储的是生成的静态文件本身.

所以在另一台电脑上恢复Octopress的时候也要分两步进行恢复：

### clone `source`分支  
```
git clone -b source git@github.com:username/username.github.com.git octopress  
```
### clone `master`分支
```
cd octopress  
git clone git@github.com:username/username.github.com.git _deploy   
```   
### Setup github pages
```
gem install bundler  
$ bundle install  
$ rake setup_github_pages  
```
### 在两台电脑之间同步
#### 1. push on 1 machine
```
$ rake generate  
$ git add .  
$ git commit -am "Some comment here."   
$ git push origin source  # update the remote source branch   
$ rake deploy             # update the remote master branch  
```  
#### 2. pull on the other machine
```
$ cd octopress  
$ git pull origin source  # update the local source branch  
$ cd ./_deploy  
$ git pull origin master  # update the local master branch  
```
参考： [Clone Your Octopress to Blog From Two Places](http://blog.zerosharp.com/clone-your-octopress-to-blog-from-two-places/)

## 7. 小结：常用命令
```  
rake generate  # 生成静态页面   
rake preview   # 本地预览: http://localhost:4000   
rake deploy    # 发布到github  
rake new_post["title"]   # 新建文章
  
 # 以下命令将source  push 到github  
git add .   
git commit -m 'Initial source commit'    
git push origin source    

 # 以下命令将source pull 到本地
git pull origin source  # update the local source branch  
cd ./_deploy  
git pull origin master  # update the local master branch  

```  
## 8. 参考资料
- [利用Octopress搭建一个Github博客](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/)   
- [Ruby开源项目介绍(1)：octopress——像黑客一样写博客](http://www.yangzhiping.com/tech/octopress.html)   
- [博客搬家：从Wordpress迁移到octopress](http://www.lijinma.com/blog/2013/01/23/move-from-wordpress-to-octopress/)  
- [象写程序一样写博客：搭建基于github的博客](http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/)  
- [Octopress3](http://kaworu.github.io/octopress/setup/install/)   
- [Octopress博客技巧](http://ginsmile.github.io/blog/2013/08/29/octopressbo-ke-ji-qiao/#.U5M_X3WSxhE)  
- [Clone Your Octopress to Blog From Two Places](http://blog.zerosharp.com/clone-your-octopress-to-blog-from-two-places/)

 


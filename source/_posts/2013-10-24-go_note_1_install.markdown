---
layout: post
title: "Go笔记1：环境配置"
date: 2013-10-24 21:06:19 +0800
comments: true
category: golang
---
## Go 安装 ##
我的环境是Win7，所以这里主要记录在Win7下安装Go的过程：  

 - 首先到[这里][1]去下载安装程序，32位系统下载go1.0.3.windows-386.msi，64位系统下载go1.0.3.windows-amd64.msi；
 - 双击打开下载的文件，一路按照默认点击下一步，这个时候go已经安装到你的系统中，默认安装之后已经在你的系统环境变量中加入了c:/go/bin；
 - 打开cmd，输入go，如果出现Go的Usage信息，那么说明Go已经安装成功了；如果出现该命令不存在，那么可以检查一下PATH环境变中是否包含了Go的安装目录。
<!--more--> 
## GOPATH ##
go 命令依赖一个重要的环境变量：$GOPATH。*(注：这个不是Go安装目录)*  
在Windows中新建一个环境变量名称叫做GOPATH：`GOPATH=c:\mygo` .

$GOPATH 目录约定有三个子目录：

 - src 存放源代码（比如：.go .c .h .s等）
 - pkg 编译后生成的文件（比如：.a）
 - bin 编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH变量中）

## 开发工具 ##
Windows平台上还是有很多Go的开发工具可供选择，比如：[LiteIDE][2]， Sublime Text， IntelliJ IDEA... 这里我还是选择平时最常用最熟悉的Eclipse。步骤如下：

### Eclipse ###

 1. 下载并安装[Eclipse][3]；
 2. 安装 goclipse 插件：[http://code.google.com/p/goclipse/wiki/InstallationInstructions][4];
 3. 下载gocode，用于go的代码补全提示: `go get -u github.com/nsf/gocode` *(注:在Windows中这一步老是报错，然后自动退出，还没有找到原因，如果不能顺利下载gocode的话，可以直接下载gocode.exe文件)*
 5. 下载 [MinGW][5] 并按要求装好; *(做调试用)*
 4. 配置插件：`Windows->Reference->Go`  
    (1). 配置Go的编译器
    (2). 配置Gocode（可选，代码补全），设置Gocode路径为之前生成的gocode.exe文件;
    (3). 配置GDB（可选，做调试用），设置GDB路径为MingW安装目录下的gdb.exe文件;

接下来就可以写一个简单的Go程序来检验安装是否成功了：  
{% highlight go %}  
package main
 
import "fmt"

func main(){  
    fmt.Println("Hellow World");  
}  
{% endhighlight %}

### IntelliJ IDEA ###
上面提到的 Eclipse 插件在 Win 环境下还是会时不时的出现问题，最后在 CSDN 上看到这篇文章： 
[使用Intellij IDEA在Windwos下搭建golang开发环境 (把屎把尿超详细)](http://blog.csdn.net/slug302/article/details/16991323) ，超详细的介绍了在Win7下配置 IntelliJ IDEA 开发 Golang 环境的过程，试过了目前最好的可行方案。


  [1]: http://code.google.com/p/go/downloads/list
  [2]: http://code.google.com/p/golangide
  [3]: http://www.eclipse.org/
  [4]: http://code.google.com/p/goclipse/wiki/InstallationInstructions
  [5]: http://sourceforge.net/projects/mingw/files/MinGW/
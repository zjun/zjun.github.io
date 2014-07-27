---
layout: post
title: "Sublime Text 2快捷键及常用插件"
date: 2013-05-08 14:36
comments: true
external-url: 
categories: Others
---
SublimeText2 是我目前为止用过的最为强大的编辑器，支持但不限于 C, C++, C#, CSS, D, Erlang, HTML, Groovy, Haskell, HTML, Java, JavaScript, LaTeX, Lisp, Lua, Markdown, Matlab, OCaml, Perl, PHP, Python, R, Ruby, SQL, TCL, Textile and XML 等主流编程语言的语法高亮。拥有优秀的代码自动完成功能 (自动补齐括号，大括号等配对符号；自动补全已经出现的单词；自动补全函数名)，非常智能。 最重要的是这么一个原来高不可攀的“神器”，现在居然开源免费了，所以还不赶快试试：

<!-- more -->

# 常用快捷键： #

## 默认方式： ##

- 鼠标选中多行，按下 Ctrl+Shift+L    —->     同时编辑这些行  
- 鼠标选中文本，反复按 CTRL+D       —->     继续向下同时选中下一个相同的文本进行同时编辑  
- 鼠标选中文本，按下 Alt+F3             —->     一次性选择全部的相同文本进行同时编辑  
- Shift+鼠标右键/按住鼠标中键          —->     进行竖向多行选择  
- Ctrl+鼠标左键                                  —->     选择同时要编辑的多处文本  
- ctrl+L                                               —->     选择整行  
- ctrl+kk                                             —->     从光标处删除至行尾  
- ctrl+j                                                —->     合并行  
- ctrl+/                                               —->     注释整行  
- ctrl+y                                               —->    恢复撤销  
- ctrl+shift+enter                              —->     插入行前  
- shift+ctrl+上下键                            —->    光标与上/下行对调，非常实用  
- cmd+shift+d —-> Duplicate Line
- ctrl+shift+w —-> Wrap Selection with Tag
- cmd+r —-> Goto Symbol...
- cmd+shift+t —-> Reopen Closed File
- cmd+shift+p —-> Command Pallete

    
## 常用的Emment快捷键 ##
- ctrl+,                                                              ======> 选中标签的内容  
- shift+ctrl+;                                                   ======> 删除标签  
- shift+ctrl+r                                                   =====> 更新所有相同css属性的值(在css3中不需要在每个不同前缀都去修改了，非常好用)  
- 选中某个css属性的数值，按ctrl+上下键     =====> 值增减1(类似chrome浏览器，非常棒)  
## CodeIntel实用快捷键 ##


- ctrl+鼠标点击某一变量                                 =====> 跳转至定义该变量的行  

# 常用插件 #
### [Emmet](https://github.com/sergeche/emmet-sublime)(原名为Zen Coding)   ###

一个用于HTML和CSS高效的编程的插件，只能在HTML或CSS文件中才能打开，原先zen coding需要按快捷键 ctrl+alt+enter才能运行，现在更新了另一种方式——直接在文本中输入zen coding的语句，然后按tag键就可以实现同样功能。

### [jQuery-snippets](https://github.com/aaronpowell/sublime-jquery-snippets) ###
这个插件提供jQuery的提示功能，不过每次提示都需要快捷键ctrl+shift+p上查找，不过如果和CodeIntel插件（下面说到）配合使用能互补不足，因为两个插件都有些jQuery代码缺少。

### [Alignment](https://github.com/wbond/sublime_alignment) ###
选定要对齐的行，按ctrl+alt+a

### [BracketHighlighter](https://github.com/facelessuser/BracketHighlighter) ###
高亮显示光标所在的括号和引号，类似于代码匹配，可以匹配括号，引号等符号内的范围

### [Clipboard History](https://github.com/kemayo/sublime-text-2-clipboard-history) ###
粘贴板历史记录，方便使用复制/剪切的内容，快捷键ctrl+shift+v可调出该历史记录面板

### [Git](https://github.com/kemayo/sublime-text-2-git) ###
用法：在菜单上 Tools –> Git，可使用git的功能
git——大名鼎鼎的git，该插件可以实现大部分的git功能

### [CodeIntel](https://github.com/Kronuz/SublimeCodeIntel) ###
代码自动提示，支持大多数语言，能很好的提示大部分jquery函数

# 如何安装插件 #
最简单的方式就是到上面的插件网站上下载压缩包，然后手工拷贝到sublime得packages 目录下 
> Preferences -> Browse Packages...

但是sublime提供了更加简单和自动化的安装方式： [Package Control](http://wbond.net/sublime_packages/package_control)
>A full-featured package manager that helps discovering, installing, updating and removing packages for Sublime Text 2. It features an automatic upgrader and supports GitHub, BitBucket and a full channel/repository system. 

## 安装 Package Control ##
- Open Sublime Text 2 console:  **ctrl+`** 
- 将以下的命令粘贴到console中，并回车运行：    
	import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print('Please restart Sublime Text to finish installation')

## 通过 Package Control 安装插件 ##
- Open **Command Pallete** in sublime： 	press **ctrl+shift+p** (Windows, Linux) or **cmd+shift+p** (OS X)
- Enter **Package Control: Install Package** in the Command Pallete
- Input/select the plugin name in the list of all available packages that are available for install.

# 参考资料： #
-  [Setup Sublime Text 2](http://drewbarontini.com/setup/sublime-text/)
- [sublime快捷键及常用插件——工欲善其事，必先利其器](http://vtmerhome.com/2013/05/sublime-shortcuts-and-common-plug/)  
-  [Sublime Text 2 - 性感无比的代码编辑器！程序员必备神器！跨平台支持Win/Mac/Linux](http://www.iplaysoft.com/sublimetext.html/comment-page-1)
-  [Sublime Package Control](http://wbond.net/sublime_packages/package_control)
---
layout: post
title: "在Sublime Text2中开发Golang"
date: 2014-01-16 21:06:19 +0800
comments: true
category: golang
---
Sublime Text 2 是一个可定制程度相当高的轻量级编辑器,具有丰富的第三方插件，关键是可以无限期的免费使用。下面就介绍通过安装几种插件，将Sublime Text 2定制成为一款相当好用的Golang IDE。  
<!--more-->
## 下载  
1. 下载[Sublime Text 2](http://www.sublimetext.com/)  
2. 安装  

## 安装Package Control  
1. 按Ctrl + ` 打开console  
2. 粘贴下列代码到console并回车  
  
    import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())

重启Sublime Text 2, 重启后，如果在 Preferences菜单下，多出一个菜单项 Package Control，说明安装成功。  

## 安装 [gocode](https://github.com/nsf/gocode)
打开终端，输入以下内容（需要安装git工具):   

    go get github.com/nsf/gocode  
    go install github.com/nsf/gocode  

安装完成后，我们可以在 go/bin 目录下，发现多出了个 gocode 文件。  

## 安装 Gosublime 插件
1. Ctrl+Shift+p 打开Package Controll 输入pcip（即“Package Control: Install Package”的缩写）  
2. 输入GoSublime，回车开始安装  

## 安装 SidebarEnhancements 插件
1. Ctrl+Shift+p 打开Package Controll 输入pcip  
2. 输入SidebarEnhancements，回车开始安装  

## 安装 Go Build 插件
### 安装  
1. Ctrl+Shift+p 打开Package Controll 输入pcip  
2. 输入 [Go Build](https://github.com/cthackers/SublimeGoBuild)，回车开始安装  

如果通过Package Controll 不能安装 Go Build, 那么可以通过git手动安装：  

- For Windows:
Open a cmd, go to `%APPDATA%\Sublime Text 2\Packages` and type...
- For Linux:
Open a shell, go to `~/.config/Sublime Text 2/Packages` and type...

    `git clone git://github.com/cthackers/SublimeGoBuild.git`

### 使用  
- `F5` to run your project
- `F7` to build
- `CTRL + F5` to run tests

## 安装 Git Gutter 插件
[Git Gutter](https://github.com/jisaacks/GitGutter) is a sublime text 2/3 plugin to show an icon in the gutter area indicating whether a line has been inserted, modified or deleted.  

1. Ctrl+Shift+p 打开Package Controll 输入pcip  
2. 输入 Git Gutter，回车开始安装  

## 安装 SideBarGit 插件
[SideBarGit](https://github.com/SublimeText/SideBarGit) provides git commands on Side Bar of Files and Folders for Sublime Text 2.  

1. Ctrl+Shift+p 打开Package Controll 输入pcip  
2. 输入 SideBarGit，回车开始安装  
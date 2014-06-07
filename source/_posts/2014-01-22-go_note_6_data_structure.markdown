---
layout: post
title: "Go笔记6：Go数据结构"
date: 2014-01-22 21:06:19 +0800
comments: true
category: golang
---
## 结构与指针
定义一个叫Point的简单的结构类型，意味着内存里是两个相邻的int。  

``` go
type Point struct { X , Y int }   
```   
<!--more-->
![](http://research.swtch.com/godata1a.png)

Point{ 10, 20 }表示一个被初始化的Point对象。  
&Point{ 10, 20 }则表示一个指向被初始化的Point对象的指针。  
前者在内存中有两个数据块，而后者则存放着一个指向两个数据块的指针。

结构中的字段被依次地排列在内存里面。

``` go
type Rect1 struct { Min, Max Point }
type Rect2 struct { Min, Max *Point }
```

![](http://research.swtch.com/godata1b.png)  

Rect1是一个拥有两个Point类型字段的结构，它的一条记录包含了两条Point记录——共4个int。Rect2是一个拥有两个Point类型指针的结构，在内存里它占两个Point指针的空间。  

## 字符串
接下来我们看一个字符串的例子：  

![](http://research.swtch.com/godata2.png)   

一个字符串在内存中的表示被分成两段，一个指向字符串数据的指针和一个长度值。对s字符串进行一个切片选择，将得到一个可能不一样的指针和长度，但它们也指向同一段字节序列。这意味着，切片并不需要分配空间或者是复制数据，创建切片很容易，只需要传递明确的下标值就行了。   

## 切片
![](http://research.swtch.com/godata3.png)  
切片是对数组中一段数据的引用。在内存中它有三段数据组成：一个指向数据头的指针、切片的长度、切片的容量。长度是索引操作的上界，如：x[i] ,容量是切片操作的上界，如：x[i:j]
 
关于切片的长度(len)和容量(cap),可以通过 `len()`和`cap()`函数得到：  

``` go
arry := [5]int{1, 2, 3, 4, 5}  
fmt.Println(arry)      // [1 2 3 4 5]  
fmt.Println(len(arry)) // 5  
fmt.Println(cap(arry)) // 5  

slce := arry[1:2]        
fmt.Println(slce)       // [2]  
fmt.Println(len(slce))  // 1  
fmt.Println(cap(slce))  // 4  

slce = arry[1:3]              
fmt.Println(slce)       // [2 3]  
fmt.Println(len(slce))  // 2  
fmt.Println(cap(slce))  // 4  

slce = arry[1:4]        
fmt.Println(slce)       // [2 3 4]  
fmt.Println(len(slce))  // 3  
fmt.Println(cap(slce))  // 4  

slce = arry[1:5]
fmt.Println(slce)       // [2 3 4 5]  
fmt.Println(len(slce))  // 4  
fmt.Println(cap(slce))  // 4  
```  

从上面的例子可以看出，slice的 len 表示切片中实际存在item的的个数，cap 表示分配的内存空间的大小。__如果是通过对已有数组切片生成的slice ,默认分配的内存空间 cap 是从切片的起始位置到原有数组的结尾__。比如上面的例子： `slce := arry[1:2]`的 cap() 就是 4。

## new和make
Go有两种创建数据结构的方法：new和make。  
它们的区别于:  
- new(T)返回一个*T类型，一个可以被隐性反向引用的指针（如图中的黑色指针）   
- make(T,args)返回一个原始的T，它并不是一个指针。T中常有写隐性的指针（如图中的灰色指针）  

__new返回一个指向初始化为全0值的指针，而make返回一个复杂的结构__。  

![](http://research.swtch.com/godata4.png)

## 参考资料
[Go Data Structures](http://research.swtch.com/godata)  
[【原创翻译】深度剖析Go数据结构](http://www.oschina.net/question/1441707_141799)  
[Effective Go: slices](http://golang.org/doc/effective_go.html#slices)  
[慎用GO中的SLICE——关于SLICE的隐含陷阱](http://www.zingscript.com/category/snack)  
[The Go Blog: Arrays, slices (and strings): The mechanics of 'append'](http://blog.golang.org/slices)  
[Arrays, Slices and Maps](http://www.golang-book.com/6)  
[The Go Blog: Go Slices: usage and internals](http://blog.golang.org/go-slices-usage-and-internals)  

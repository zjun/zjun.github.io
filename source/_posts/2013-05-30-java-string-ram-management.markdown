---
layout: post
title: "关于 Java String 内存管理的几个有趣的例子"
date: 2013-05-30 21:06:19 +0800
comments: true
categories: Java
---

今天在 [OSChina](http://www.oschina.net) 上看到一篇有趣的文章：[关于String内存分配的深入探讨](http://my.oschina.net/u/551903/blog/134000),文章通过几个简单的例子，把Java中String的内存管理的方式解释的很清楚，涨了不少姿势，现根据自己的理解做些笔记在这里：

<!-- more -->

废话不多说，先上代码：  

	public class StringDemo {
	
		public static final String MESSAGE = "taobao";
	
		public static void main(String[] args) {
			
			// ---------------------------------------------
			String a = "tao" + "bao";
			String b = "tao";
			String c = "bao";
	
			System.out.println(a == MESSAGE);// true
			System.out.println((b + c) == MESSAGE);// false
	
			// ---------------------------------------------
			final String d = "tao";
			final String e = "bao";
	
			System.out.println((d + e) == MESSAGE);// true
	
			// ---------------------------------------------
			String f = "tao" + "bao";
			String g = new String("taobao");
	
			System.out.println(f == MESSAGE); // true
			System.out.println(g == MESSAGE); // false
	
			g = g.intern();
	
			System.out.println(g == MESSAGE); // true
			System.out.println(f == g.intern()); // true
			// ---------------------------------------------
		}
	}

## 笔记： ##
- Java中对于编译和运行时能够知道大小的变量（如：引用变量和基本类型的值）存放于**栈内存**   
- Java中通过 new 产生的变量和大小会在运行时变化的变量(如：数组和链表)存放于**堆内存，但指向对象的引用还是存放在栈内存**   
- **栈内存中的数据会相互共享**，如果新增变量的值在栈中已经存在，那么新变量将指向同一个地址和值，而不会新增一个空间来存放自己的值;如果没有就在该栈内存增加一个该常量，并将变量指向该常量。 
- 对于**直接赋值的字符串常量**（如String s=“Hello World”；中的Hello World）也是**存放在栈内存中**  
- 堆内存没有数据共享的特点,每次都会新增划分一个空间给新的变量，哪怕是相同的值  
- Java **编译器的优化**，会对于**字符串常量的相加**，在编译时**直接**将字符串**合并**，而不是等到运行时再合并，比如：**String a = "tao"+"bao";和String a = "taobao";编译出的字节码是一样的**  
- **Java对String的相加是通过StringBuffer实现的**，先构造一个StringBuffer,然后调用append()方法追加，然后将StringBuffer转化成String对象。StringBuffer对象**在堆内存中**，那转换成的String对象理所应当的也是在堆内存中  
- **intern()**方法会**先检查**String池(或者说成**栈内存**)中是否存在相同的字符串常量，如果有就返回  
- **final变量**不可能再次赋值了，所以存放在**栈内存里**  
  



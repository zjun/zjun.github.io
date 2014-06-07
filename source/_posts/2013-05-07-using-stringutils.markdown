---
layout: post
title: "使用 StringUtils 工具类"
date: 2006-07-27 16:01
comments: true
external-url: 
categories: Java
---

org.apache.commons.lang.StringUtils中提供许多有用的字符串操作方法，了解这些方法，我们可以避免许多不必要的重复工作。下面介绍其中比较有用的几个方法：
<!-- more -->

**检查空字符串：**  
	StringUtils.isBlank(String str);  
	StringUtils.isNotBlank(String str);

**缩写字符串：**  
	String test  =   " This is a test of the abbreviation. ";  
	System.out.println( StringUtils.abbreviate( test,  10  ) );

[**Console输出**]  
>This is...

**查找嵌套字符串：**  
	String htmlContent  =   " <html>\n "   +  
	                      "   <head>\n "   +  
	                      "     <title>Test Page</title>\n "   +  
	                      "   </head>\n "   +  
	                      "   <body>\n "   +  
	                      "     <p>This is a TEST!</p>\n "   +  
	                      "   </body>\n "   +  
	                      " </html> " ;  
	
	//  Extract the title from this XHTML content   
	String title  =  StringUtils.substringBetween(htmlContent,  " <title> " ,  " </title> " );
	System.out.println(  " Title:  "   +  title );

[**Console输出**]  
>Title: Test Page

**验证字符串：**  
	String test1  =   " ORANGE " ;  
	String test2  =   " ICE9 " ;  
	String test3  =   " ICE CREAM " ;  
	String test4  =   " 820B Judson Avenue " ;  
	boolean  t1val  =  StringUtils.isAlpha( test1 );  //  returns true  
	boolean  t2val  =  StringUtils.isAlphanumeric( test2 );  //  returns true   
	boolean  t3val  =  StringUtils.isAlphaSpace( test3 );  //  returns true  
	boolean  t4val  =  StringUtils.isAlphanumericSpace( test4 );  //  returns true  

**计算字符串出现频率：**  
	File manuscriptFile  =   new  File( " manuscript.txt " );  
	Reader reader  =   new  FileReader( manuscriptFile );  
	StringWriter stringWriter  =   new  StringWriter( );  
	while ( reader.ready( ) )  { writer.write( reader.read( ) ); }  
	String manuscript  =  stringWriter.toString( );  
	//  Convert string to lowercase  
	manuscript  =  StringUtils.lowerCase(manuscript);  
	//  count the occurrences of "futility"  
	int  numFutility  =  StringUtils.countMatches( manuscript,  " futility "  );   

**比较不同字符串：**  
	int  dist  =  StringUtils.getLevenshteinDistance(  " Word " ,  " World "  );  
	String diff  =  StringUtils.difference(  " Word " ,  " World "  );  
	int  index  =  StringUtils.indexOfDifference(  " Word " ,  " World "  );  
	System.out.println(  " Edit Distance:  "   +  dist );  
	System.out.println(  " Difference:  "   +  diff );  
	System.out.println(  " Diff Index:  "   +  index );  

[**Console输出**]  
>Edit Distance:  2  
Difference: ld  
Diff Index:  3  

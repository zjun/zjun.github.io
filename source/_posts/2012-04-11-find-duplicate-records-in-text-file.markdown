---
layout: post
title: "Find duplicate records in text file"
date: 2012-04-11 16:52
comments: true
category: Others
---
## Example: ##
	abc 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
	abc 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
	tas 3420 3562 2123 1343 2176 7654 3252 8765 5643 3452  
	aer 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
	tas 3420 3562 2123 1343 2176 7654 3252 8765 5643 3452  

## UNIX: ##

display the no of occurance and the record

{% highlight console %}   
**sort f1.txt|uniq -c**  

2 abc 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
1 aer 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
2 tas 3420 3562 2123 1343 2176 7654 3252 8765 5643 3452  
{% endhighlight %}    

display only the duplicate records  

{% highlight console %}  
**sort f1.txt|uniq -d**  

abc 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
tas 3420 3562 2123 1343 2176 7654 3252 8765 5643 3452  
{% endhighlight %}  

display distinct records

{% highlight console %}  
**sort f1.txt|uniq**  

abc 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
aer 1000 3452 2463 2343 2176 7654 3452 8765 5643 3452  
tas 3420 3562 2123 1343 2176 7654 3252 8765 5643 3452  
{% endhighlight %}  

## Windows: ##

Notepad++ can sort by line, and remove the duplicate lines at the same time.

> 1. Open the menu under: **TextFX-->TextFX** Tools  
> 2. Make sure "**sort outputs only unique...**" is checked  
> 3. select a block of text (ctrl-a to select the entire document).  
> 4. click "**sort lines case sensitive**" or "**sort lines case insensitive**"

## 参考阅读： ##

[How to find Duplicate Records in a text file](http://www.unix.com/shell-programming-scripting/85711-how-find-duplicate-records-text-file.html)  
[Shell: How To Remove Duplicate Text Lines](http://www.cyberciti.biz/faq/unix-linux-shell-removing-duplicate-lines/)  
[How to Remove Duplicate Lines in Unix](http://www.ehow.com/how_5084389_remove-duplicate-lines-unix.html)  
[remove duplicates from a text file in free editor](http://answers.yahoo.com/question/index?qid=20080522091139AAoVO8m)  


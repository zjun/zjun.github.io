---
layout: post
title: "在javascript中实现继承关系"
date: 2006-03-06 19:40
comments: true
external-url: 
categories: Web开发
---

意译自KevLinDev上一篇文章，才疏学浅，望指正，英文原文可到[这里](http://www.kevlindev.com/tutorials/javascript/inheritance/)查看。
 
----------  
javascript脚本语言是支持面向对象编程(Object Oriented Programming )的,只是javascript实现的方式比较特别，与C++和java中的实现方式不同。在javascript中我们需要借助prototype对象来访问父类的方法，下面将讨论在javascript中实现OOP中最基本的特征关系 --- **继承**。

<!-- more -->

## 首先 ##
我们从最基本的开始：**在javascript中创建对象**。创建一个对象包括2步：  
1) 创建一个和你想要创建的对象同名的函数(这里可以把这个函数理解为java中的构造函数)；  
2) 通过调用 **new 上面创建的函数名** 的方式创建一个对象实例；
{% highlight javascript %} 
// 创建同名函数
function Person(first, last) {
	this.first = first;
	this.last  = last;
}
// 创建实例
var person = new Person("John", "Dough");
{% endhighlight %}

注意：构造函数中的**this**指针指向当前的创建的对象，这与java中的表示是一致的。通过this指针我们可以操作对象的属性。

## 然后 ##
设置对象的方法：
{% highlight javascript %} 
Person.prototype.toString() {
	return this.first + " " + this.last;
}
alert( person.toString() ); // displays "John Dough"
alert( person ); // alert函数将默认调用toString()函数
{% endhighlight %}

在这里我们用到了javascript中的prototype属性。所有的javascript对象都拥有一个prototype属性，javascript就是通过这个属性来实现继承关系的。具体的实现机制是这样的：**当你访问一个对象的属性时，编译器将先查找对象的属性看有没有匹配的。如果没有找到，将继续查找这个对象的prototype属性所指的对象，看是否存在匹配的属性。如果还是没有找到，编译器将检查当前对象的prototype是否拥有prototype属性，如果有将继续查找下去。以此类推，直到查找完所有的prototype属性**。  
从这个过程不难看出，存在着一个类似于继承的访问链，可以把prototype对象指向我们要继承的父类，以此来访问父类的方法。

## 接下来 ##
我们新建一个类Employee来继承上面的Person类，并且新增一个属性id,在Employee的构造函数中只是初始化属性id，而父类中的属性将由Person的构造函数类设置：  
{% highlight javascript %} 
	function Person(first, last) {
		if ( arguments.length > 0 )
	    	this.init(first, last);
	}
	
	Person.prototype.init = function(first, last) {
	    this.first = first;
	    this.last  = last;
	}
	
	Employee.prototype = new Person();
	Employee.prototype.constructor = Employee;
	Employee.superclass = Person.prototype;
	
	function Employee(first, last, id) {
	    if ( arguments.length > 0 )
	        this.init(first, last, id);
	}
	
	Employee.prototype.init = function(first, last, id) {    
	    // Call superclass method
	    Employee.superclass.init.call(this, first, last);
	
	    // init properties
	    this.id = id;
	}
{% endhighlight %}

可以看到，我们把初始化属性的操作提取出来放到了一个init函数中，这样做是为了方便在子类中调用。注意其中的"**Employee.superclass = Person.prototype;**"，这里是一个技巧，方便下面在子类中调用父类的方法。  
另一个需要注意的地方是"**Employee.superclass.init.call(this, first, last);**":对于所有对象中的方法来说，都可以通过两个方法来调用－－"**call**" 和 "**apply**".  
这里使用了**call**方法，其中第一个参数是将在调用的方法中访问的对象，后面的参数与调用方法的参数一致。  
 **apply**方法的使用与call大致一致，不同在于除了第一个参数外，后面是一个参数数组。

下面附上完整的例子和类图：

**继承关系图**如下：  
![](http://www.blogjava.net/images/blogjava_net/zjun/8140/o_javascriptOOP.gif)

**代码**：

{% highlight javascript %} 
	/*****
	*
	*    Person constructor
	*
	*****/
	function Person(first, last) {
	    if ( arguments.length > 0 )
	        this.init(first, last);
	}
	
	/*****
	*
	*    Person init
	*
	*****/
	Person.prototype.init = function(first, last) {
	    this.first = first;
	    this.last  = last;
	};
	
	/*****
	*
	*    Person toString
	*
	*****/
	Person.prototype.toString = function() {
	    return this.first + "," + this.last;
	};
	
	
	/*****
	*
	*    Setup Employee inheritance
	*
	*****/
	Employee.prototype = new Person();
	Employee.prototype.constructor = Employee;
	Employee.superclass = Person.prototype;
	
	/*****
	*
	*    Employee constructor
	*
	*****/
	function Employee(first, last, id) {
	    if ( arguments.length > 0 )
	        this.init(first, last, id);
	}
	
	/*****
	*
	*    Employee init
	*
	*****/
	Employee.prototype.init = function(first, last, id) {    
	    // Call superclass method
	    Employee.superclass.init.call(this, first, last);
	
	    // init properties
	    this.id = id;
	}
	
	/*****
	*
	*    Employee toString
	*
	*****/
	Employee.prototype.toString = function() {
	    var name = Employee.superclass.toString.call(this);
	
	    return this.id + ":" + name;
	};
	
	
	/*****
	*
	*    Setup Manager inheritance
	*
	*****/
	Manager.prototype = new Employee;
	Manager.prototype.constructor = Manager;
	Manager.superclass = Employee.prototype;
	
	/*****
	*
	*    Manager constructor
	*
	*****/
	function Manager(first, last, id, department) {
	    if ( arguments.length > 0 )
	        this.init(first, last, id, department);
	}
	
	/*****
	*
	*    Manager init
	*
	*****/
	Manager.prototype.init = function(first, last, id, department){
	    // Call superclass method
	    Manager.superclass.init.call(this, first, last, id);
	
	    // init properties
	    this.department = department;
	}
	
	/*****
	*
	*    Manager toString
	*
	*****/
	Manager.prototype.toString = function() {
	    var employee = Manager.superclass.toString.call(this);
	
	    return employee + " manages " + this.department;
	}

{% endhighlight %}

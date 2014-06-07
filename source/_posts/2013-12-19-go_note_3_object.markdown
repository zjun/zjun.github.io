---
layout: post
title: "Go笔记3：面向对象"
date: 2013-12-19 21:06:19 +0800
comments: true
category: golang
---
Go是一个面向对象的语言，但是不同于Java，C++中略显复杂的面向对象思想，Golang 则将 OOP 思想做了极大的简化，只留下了最核心的特征：你可以给任何类型（包括内置类型）“增加”新方法。而在实现某个接口时，**只需要实现该接口要求的所有方法即可**。

接下来就是我对Go语言面向对象的特点一些总结。
<!--more-->
## 对象 ##
Golang中没有类似于Java中的Class对象，而是和C类似，通过结构体 struct 来实现的：  
{% highlight go %}  
type person struct {  
    name string  
    age int  
}  
{% endhighlight %}  

可以同下面几种方法来声明和初始化 struct 变量：  

1. 先声明，后赋值：
{% highlight go %}  
var P person  // P现在就是person类型的变量了  
P.name = "zJun"  // 赋值"Astaxie"给P的name属性  
P.age = 25  // 赋值"25"给变量P的age属性  
{% endhighlight %}  

2. 按照顺序提供初始化值  
{% highlight go %}   
P := person{"Tom", 25}  
{% endhighlight %}    

3. 通过field:value的方式初始化，可以任意顺序  
{% highlight go %}  
P := person{age:24, name:"Tom"}  
{% endhighlight %}    

4. 通过new函数分配一个指针，此处P的类型为*person  
{% highlight go %}  
P := new(person)  
{% endhighlight %}  

## 继承 ##
Golang中继承是通过**匿名变量**实现的：  
**匿名字段**，也称为嵌入字段 - 只提供类型，而不写字段名的变量。  
当匿名字段是一个struct的时候，那么这个struct所拥有的全部字段都被隐式地引入了当前定义的这个struct。  比如下面的例子：  
{% highlight go %}  
type Human struct {  
    name string  
    age int  
    weight int  
}  
 
type Student struct {  
    Human  // 匿名字段，默认Student包含了Human的所有字段  
    speciality string  
}  

func main() {  
    // 初始化一个学生  
    mark := Student{Human{"Mark", 25, 120}, "Computer Science"}  
 
    // 访问相应的字段  
    fmt.Println("His name is ", mark.name)  
    fmt.Println("His age is ", mark.age)  
    fmt.Println("His weight is ", mark.weight)  
    fmt.Println("His speciality is ", mark.speciality)  
}  
{% endhighlight %}  

## 方法 ##
上面我们已经看到在Golang中可以通过 struct 来定义对象的属性(变量)，但是还不能直接在 struct 中定义方法(method),下面我们就来看看如何在Golang中定义一个对象的方法：

### 定义 ###
method的语法如下：
{% highlight go %}  
func (r ReceiverType) funcName(parameters) (results)  
{% endhighlight %}   

其中的 ReceiverType(接收者)如果是自定义的struct类型，那么这个方法就是struct的一个方法，可以通过下面的方式调用：  
{% highlight go %}  
r.funcName(param);  
{% endhighlight %}   
### 方法的继承 ###
和前面提到的一样，方法也可以通过匿名方法的方式进行继承：如果一个对象中有一个匿名字段实现了方法，那么在这个对象中也继承了这个方法，比如下面的例子：
{% highlight go %}  
type Human struct {  
    name string  
    age int  
    phone string  
}  
 
type Student struct {  
    Human //匿名字段  
    school string  
}  
   
//在human上面定义了一个method  
func (h *Human) SayHi() {  
    fmt.Printf("Hi, I am %s you can call me on %s%n", h.name, h.phone)  
}  
 
func main() {  
    mark := Student{Human{"Mark", 25, "222-222-YYYY"}, "MIT"}
 
    mark.SayHi()  
}    
{% endhighlight %}     
### 方法重写 ###
Golang不支持方法的重载(override)，但是可以对方法进行重写(overwrite),实现的方式也很简单：**直接在子类中实现一个相同的方法**。在调用时，会优先调用子类自己实现的方法，如果没有找到才会向上寻找调用父类(匿名变量)中的方法。  


## 参考资料 ##
[Go Web 编程：面向对象](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/02.5.md)  
[Go 面向对象](http://www.oschina.net/translate/go-object-oriented-design)    

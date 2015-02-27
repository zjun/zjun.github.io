---
layout: post
title: "Go笔记2：基础语法"
date: 2013-10-27 21:06:19 +0800
comments: true
category: golang
---
## 变量 ##
**说明：**  
1. 使用var关键字是Go最基本的定义变量方式，Go把变量类型放在变量名后面；  
2. _（下划线）是个特殊的变量名，任何赋予它的值都会被丢弃；  
{% highlight go %}   
_, b := 34, 35 //将值35赋予b，并同时丢弃34  
{% endhighlight %}   
3. Go对于已声明但未使用的变量会在编译阶段报错； 
<!--more-->
{% highlight go %}  
var variableName type //定义一个名称为“variableName”，类型为"type"的变量  
var vname1, vname2, vname3 type //定义三个类型都是“type”的三个变量  
var variableName type = value //初始化“variableName”的变量为“value”值，类型是“type”  
var vname1, vname2, vname3 type= v1, v2, v3 //定义三个类型都是"type"的三个变量,并且它们分别初始化相应的值   
var vname1, vname2, vname3 = v1, v2, v3 // 定义三个变量，它们分别初始化相应的值,Go会根据其相应值的类型来初始化它们  
vname1, vname2, vname3 := v1, v2, v3 //**:=**这个符号直接取代了var和type,这种形式叫做简短声明,只能用在函数内部；在函数外部使用var方式来定义全局变量  
{% endhighlight %} 

## 常量 ##
**说明：**  
1. 在Go程序中，常量可定义为**数值**、**布尔值**或**字符串**等类型；

{% highlight go %}   
const constantName = value  
  
const Pi float32 = 3.1415926  //如果需要，也可以明确指定常量的类型
{% endhighlight %}  

## 基础类型 ##
- 布尔型：类型为bool，值是true或false，默认为false;
- 数值类型: 
  1. 整数类型: 有无符号和带符号两种 - int和uint，这两种类型的长度相同;
  2. 浮点类型: 有float32和float64两种（没有float类型），默认是float64;
  3. 复数: 它的默认类型是complex128（64位实数+64位虚数）,也有complex64(32位实数+32位虚数) ;
- 字符串: 类型是string, 是用一对双引号（""）或反引号（` `）括起来定义;
  1. Go中字符串是不可变的;
  2. 可以将字符串转换为 []byte 类型数组，修改后在转换回 []string 数组，例如下面的例子：

{% highlight go %}  
s := "hello"  
c := []byte(s)  // 将字符串 s 转换为 []byte 类型  
c[0] = 'c'  
s2 := string(c)  // 再转换回 string 类型  
fmt.Printf("%s\n", s2)  
{% endhighlight %}  

  3. Go中使用+操作符来连接两个字符串；  
  4. 使用反引号（` `）来申明多行字符串：**` 括起的字符串为Raw字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出**；
  
- 错误类型：内置有一个error类型，专门用来处理错误信息，Go的package里面还专门有一个包errors来处理错误：
{% highlight go %}  
err := errors.New("emit macho dwarf: elf header corrupted")  
if err != nil {  
    fmt.Print(err)  
}  
{% endhighlight %}  

## 分组声明 ##
同时声明多个常量、变量，或者导入多个包时，可采用分组的方式进行声明：
{% highlight go %}  
import(  
    "fmt"  
    "os"  
)  
 
const(  
    i = 100  
    pi = 3.1415  
    prefix = "Go_"  
)  
 
var(  
    i int  
    pi float32  
    prefix string  
)  
{% endhighlight %}  

## iota枚举 ##
关键字**iota**用来声明enum的时候采用，它默认开始值是0，每调用一次加1：  

{% highlight go %}  
const(  
    x = iota  // x == 0  
    y = iota  // y == 1  
    z = iota  // z == 2  
    w  // 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说w = iota，因此w == 3。其实上面y和z可同样不用"= iota"  
)
 
const v = iota // 每遇到一个const关键字，iota就会重置，此时v == 0  
 
const (  
  e, f, g = iota, iota, iota //e=0,f=0,g=0 iota在同一行值相同  
)  
{% endhighlight %}   

## 默认规则 ##
- **大写字母开头的变量**是可导出的，也就是其它包可以读取的，是**公用变量**；
- **小写字母开头的变量**就是不可导出的，是**私有变量**。
- **大写字母开头的函数**也是一样，相当于class中的带**public关键词的公有函数**；
- **小写字母开头的函数**就是有**private关键词的私有函数**。  

## array、slice、map ##
### array ###
array就是数组，定义方式如下：  
{% highlight go %}  
var arr [n]type  //n表示数组的长度，type表示存储元素的类型  
a := [3]int{1, 2, 3} // 声明了一个长度为3的int数组
 
b := [10]int{1, 2, 3} // 声明了一个长度为10的int数组，其中前三个元素初始化为1、2、3，其它默认为0
 
c := [...]int{4, 5, 6} // 可以省略长度而采用`...`的方式，Go会自动根据元素个数来计算长度  

doubleArray := [2][4]int{[4]int{1, 2, 3, 4}, [4]int{5, 6, 7, 8}} //声明了一个二维数组  

{% endhighlight %}  

**说明：**  
1. 数组不能改变长度；
2. 数组之间的赋值是值的赋值，即当把一个数组作为参数传入函数的时候，传入的其实是该数组的副本，而不是它的指针;

### slice ###
slice是一个引用类型, slice总是指向一个底层array，slice的声明也可以像array一样，只是不需要长度:
{% highlight go %}   
var fslice []int //和声明array一样，只是少了长度  

// 声明一个含有10个元素元素类型为byte的数组
var ar = [10]byte {'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'}
 
// 声明两个含有byte的slice
var a, b []byte
 
// a指向数组的第3个元素开始，并到第五个元素结束，
a = ar[2:5]
//现在a含有的元素: ar[2]、ar[3]和ar[4]
 
// b是数组ar的另一个slice
b = ar[3:5]
// b的元素是：ar[3]和ar[4]

{% endhighlight %}  

**说明：声明数组时，方括号内写明了数组的长度或使用...自动计算长度，而声明slice时，方括号内没有任何字符。**

### map ###
map的格式为  
	map[keyType]valueType  

**说明：**  
- map是无序的，每次打印出来的map都会不一样，它不能通过index获取，而必须通过key获取  
- map的长度是不固定的，也就是和slice一样，也是一种引用类型  
- 内置的len函数同样适用于map，返回map拥有的key的数量  
- map的值可以很方便的修改，通过numbers["one"]=11可以很容易的把key为one的字典值改为11  
- map和其他基本型别不同，它不是thread-safe，在多个go-routine存取时，必须使用mutex lock机制  
- map的初始化可以通过**key:val**的方式初始化值
- map有两个返回值，第二个返回值，如果不存在key，那么ok为false，如果存在ok为true
- 通过函数**delete(array,key)**删除map的元素

## make 和 new操作 ##
### new ###
new(T)分配了零值填充的T类型的内存空间，并且返回其地址：  
> **new返回指针**

### make ###
make(T, args)只能创建slice、map和channel，并且返回一个有初始值(非零)的T类型，而不是*T:  
> **make返回初始化后的（非零）值**

## 参考资料 ##

[Go Web 编程 ：Go　基础](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/02.2.md)
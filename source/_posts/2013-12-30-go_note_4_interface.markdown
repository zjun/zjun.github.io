---
layout: post
title: "Go笔记4：接口"
date: 2013-12-30 21:06:19 +0800
comments: true
category: golang
---
## 什么是接口(interface) ##
Golang中的interface是一组method的组合，我们通过interface来定义对象的一组行为。**如果某个对象实现了某个接口的所有方法，则此对象就实现了此接口。**
<!--more-->
比如：  

``` go
// 定义interface
type Men interface {
    SayHi()
    Sing(lyrics string)
}
// 定义struct
type Human struct {
    name string
    age int
    phone string
}

// Human对象实现Sayhi方法
func (h *Human) SayHi() {
    fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
}
 
// Human对象实现Sing方法
func (h *Human) Sing(lyrics string) {
    fmt.Println("La la, la la la, la la la la la...", lyrics)
}

```

在上面的例子中对象Human实现了接口Men中的所有方法，所以Human可以被看作一个Men类型的对象，照此类推：**一个接口可以被多个不同的对象思想，同理，一个对象也可以实现多个接口**。

## interface变量的值 ##
一个interface的变量，可以存实现这个interface的任意类型的对象。

## 空interface ##
空interface(`interface{}`)不包含任何的method，所以所有的类型都默认实现了空interface。空interface在我们需要存储任意类型的数值的时候相当有用，因为它可以存储任意类型的数值。它有点类似于java语言的Object对象。  

``` go
var a interface{} // 定义a为空接口  
var i int = 5
s := "Hello world"
// a可以存储任意类型的数值
a = i
a = s
```
**一个函数把interface{}作为参数，那么他可以接受任意类型的值作为参数，如果一个函数返回interface{},那么也就可以返回任意类型的值。**

## 嵌入interface ##
和struct中的匿名字段类似：如果一个interface1作为interface2的一个嵌入字段，那么interface2隐式的包含了 interface1 里面的method。

比如：io包下面的 io.ReadWriter ，他包含了io包下面的Reader和Writer两个interface。  

``` go 
type ReadWriter interface {
    Reader
    Writer
}
```

## 参考资料 ##
[Go Web编程：interface](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/02.6.md)
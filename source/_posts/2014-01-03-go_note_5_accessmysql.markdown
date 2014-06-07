---
layout: post
title: "Go笔记5：访问MySQL数据库"
date: 2014-01-03 21:06:19 +0800
comments: true
category: golang
---
## 安装驱动 ##
MySQL驱动： [https://github.com/go-sql-driver/mysql](https://github.com/go-sql-driver/mysql) 。  
```    
$ go get github.com/go-sql-driver/mysql
```  
<!--more-->
## 连接数据库 ##
``` go  
import (
	"database/sql"
	"fmt"
	_ "github.com/Go-SQL-Driver/MySQL"
) 

db, err := sql.Open("mysql", "user:password@/dbname")  
```  
## 关闭数据库连接 ##
``` go  
db.Close()
```  
## 增删改查 ##
### 查询 ###
``` go  
db, err := sql.Open("mysql", "user:password@/dbname?charset=utf8")  
checkErr(err)  

//查询数据  
rows, err := db.Query("SELECT * FROM account")  
checkErr(err)  

for rows.Next() {  
	var id int  
	var name string  
	err = rows.Scan(&id, &name)  
	checkErr(err)  
	fmt.Print(id)  
	fmt.Println(name)  
}  
```
### 新增 ###
``` go  
db, err := sql.Open("mysql", "zjun:zjun@/test?charset=utf8")  
checkErr(err)  
//插入数据  
stmt, err := db.Prepare("INSERT INTO userinfo(username, departname, created) VALUES(?,?,?)")  
checkErr(err)  

res, err := stmt.Exec("zjun", "研发部门", "2014-01-03")  
checkErr(err)  

id, err := res.LastInsertId()   
checkErr(err)  

fmt.Println(id)  
```
### 修改 ###
``` go  
//更新数据
stmt, err = db.Prepare("update userinfo set username=? where uid=?")
checkErr(err)

res, err = stmt.Exec("zjunupdate", id)
checkErr(err)

affect, err := res.RowsAffected()
checkErr(err)

fmt.Println(affect)
```
### 删除 ###
``` go  
//删除数据
stmt, err = db.Prepare("delete from userinfo where uid=?")
checkErr(err)

res, err = stmt.Exec(id)
checkErr(err)

affect, err = res.RowsAffected()
checkErr(err)

fmt.Println(affect)
```
## 参考资料 ##
[Go Web编程：使用MySQL数据库](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/05.2.md)
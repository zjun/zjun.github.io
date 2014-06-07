---
layout: post
title: "Hibernate中的继承关系"
date: 2006-08-16 15:15
comments: true
external-url: 
categories: 开源框架
---
Hibernate中支持3种形式实现继承关系：  
1. **Table per concrete class** 表与子类之间独立一对一关系  
2. **Table per subclass** 每个子类对应一张子表，并与主类共享主表  
3. **Table per class hierarchy** 表与类一对多关系  
<!-- more -->

### 1. Table per concrete class ###
**数据库**：父类没有对应的表，每个子类独立对应一张表，父类中的公共属性将在每一张表中以相同冗余字段的形式出现。  
**映射文件**：子类对应的Hibernate的配映射文件与普通映射文件没有特别区别，只是通过<class>节点的 polymorphism="implicit" 属性实现隐式多态模式来实现与父类之间的继承关系。

### 2. Table per subclass ###
**数据库**：父类单独映射一张主表，而每个子类分别映射一张子表。子表和主表通过外键相关联。  
**映射文件**：在映射文件中通过 <joined-subclass> 节点来表示主类和子类之间的继承关系，如下：  

	<hibernate-mapping>
	  <class name="BaseClass" table="T_BASE">
	    <id name="id" column="id">
	      <generator class="native"/>
	    </id>
	    <property name="name" column="name"/>
	    <!--申明子类1-->
	    <joined-subclass name="SubClass1" table="T_SUB1">
	      <key column="id"/>
	      <property name="subName1" column="subName1"/>
	    </joined-subclass>
	    <!--申明子类2-->
	    <joined-subclass name="SubClass2" table="T_SUB1">
	      <key column="id"/>
	      <property name="subName2" column="subName2"/>
	    </joined-subclass>
	  </class>
	</hibernate-mapping>

### 3. Table per class hierarchy ###
**数据库**：用一张表对应所有子类，包含所有子类字段，通过一个标示类别的字段来区分不同子类对应的数据。  
**映射文件**：通过<subclass> 和<disriminator>节点来对不同子类加以区别，如下：  

	<hibernate-mapping>
	  <class name="BaseClass" table="T_BASE">
	    <id name="id" column="id">
	      <generator class="native"/>
	    </id>
	    <discriminator column="category" type="string"/>                <------------- 声明discriminator字段
	    <property name="name" column="name"/>
	    <!--子类1-->     
	    <subclass name="SubClass1" discriminator-value="1">       <------------- discriminator为1，则是子类1
	       <property name="subName1" column="subName1"/>
	    </subclass>  
	    <!--子类2-->     
	    <subclass name="SubClass2" discriminator-value="2">       <------------- discriminator为2，则是子类2
	       <property name="subName2" column="subName2"/>
	    </subclass>
	  </class>
	<hibernate-mapping>
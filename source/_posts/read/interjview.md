---
title: interview
date: 2019-08-19
tags:
- interview
categories:
- interview
---
***
<!-- more -->
Q1.spring
Q2.简述线程的状态（生命周期）
Q3.Mybatis:#{}和${}的区别

    A:
    #{}表示一个占位符号，通过#{}可以实现 preparedStatement 向占位符中设置值，自动进行 java 类型和 jdbc 类型转换，#{}可以有效防止 sql 注入，
    ${}表示拼接 sql 串,执行的是 statement对象。
    SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题
			1. 输入用户随便，输入密码：a' or 'a' = 'a 
			2. sql：select * from user where username = 'haha' and password = 'a' or 'a' = 'a' 
    
Q4.ArrayList和LinkedList区别
Q5.数据库如何处理两个同时修改数据的操作，并只使一个操作生效。

    A:设置事务的隔离级别:
	* 隔离级别：
		1. read uncommitted：读未提交
			* 产生的问题：脏读、不可重复读、幻读
		2. read committed：读已提交 （Oracle）
			* 产生的问题：不可重复读、幻读   //
		3. repeatable read：可重复读 （MySQL默认）   //在同一次事务中其实其他事务已经提交，但你的事务没提交，你查到的数据是相同的；
			* 产生的问题：幻读
		4. serializable：串行化    //另一个事务正在操作数据的时候，会将数据锁起来，知道该事务提交后，你才能访问到数据
			* 可以解决所有的问题
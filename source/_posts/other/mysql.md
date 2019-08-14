---
title: 
date: 2019-08-02
tags:
- spring
categories:
- spring
---
spring
<!-- more -->
[中文乱码](https://www.cnblogs.com/jasonzeng/p/8341445.html)
linux系统默认编码不是utf-8
```sql
CREATE TABLE hero1(
id INT PRIMARY KEY AUTO_INCREMENT,
username VARCHAR(100),
ranking INT
)DEFAULT CHARSET=utf8;
```


SHOW VARIABLES LIKE '%char%';   //查看数据库的编码
 
SET character_set_server=utf8;  //设置数据库的编码
SET character_set_database=utf8;
---
title: 
date: 2019-0-0
tags:
- 0
categories:
- 0
---
***
<!-- more -->
sql
```sql
-- 产品表
 CREATE TABLE product( 
	 id INT PRIMARY KEY AUTO_INCREMENT,
	 productNum VARCHAR(50) NOT NULL, 
	 productName VARCHAR(50), 
	 cityName VARCHAR(50),
	 departureTime DATETIME,  
	 productPrice INT(20), 
	 productDesc VARCHAR(500), 
	 productStatus INT, 
	 CONSTRAINT product UNIQUE (id, productNum)
 )
INSERT INTO product ( productnum, productname, cityname, departureTime,productprice, productdesc, productstatus) VALUES ( '001', '北京三日游', '北京','2019-01-01', 1200, '不错的旅行', 1); 
INSERT INTO product ( productnum, productname, cityname, departureTime,productprice, productdesc, productstatus) VALUES ( '003', '上海五日游', '上海','2019-01-01', 1800, '魔都我来了', 0); 
INSERT INTO product ( productnum, productname, cityname, departureTime,productprice, productdesc, productstatus) VALUES ( '001', '北京三日游', '北京','2019-01-01', 1200, '不错的旅行', 1); 
INSERT INTO product ( productnum, productname, cityname, departureTime,productprice, productdesc, productstatus) VALUES ( '002', '北京三日游', '北京','2019-01-01', 1200, '不错的旅行', 1); 
INSERT INTO product ( productnum, productname, cityname, departureTime,productprice, productdesc, productstatus) VALUES ( '003', '上海五日游', '上海','2019-01-01', 1800, '魔都我来了', 0);
INSERT INTO product ( productnum, productname, cityname, productprice, productdesc, productstatus) VALUES ( '001', '北京三日游', '北京', 1200, '不错的旅行', 1);
SELECT * FROM product;
```
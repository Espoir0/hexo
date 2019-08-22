---
title: mysql
date: 2019-8-11
tags: 
- database
categories:
- database
---
***
<!-- more -->
#### database数据库

    1.存储数据的仓库,数据可以永久保存,查询速度快.对数据的管理方便
    2.本质上是一个文件系统，还是以文件的方式存在服务器上。
    3.所有的关系型数据库都可以使用通用的 SQL 语句进行管理  
    
    MySQL：  开源免费的数据库，小型的数据库，已经被 Oracle 收购了。MySQL6.x 版本也开始收费。
    Oracle： 收费的大型数据库，Oracle 公司的产品
    DB2 ：   IBM 公司的数据库产品,收费的。常应用在银行系统中。
    SQLite： 嵌入式的小型数据库，应用在手机端，如：Android。
    SQL Server：MicroSoft 公司收费的中型的数据库。C#、.net 等语言常使用
    
    DBMS DataBase Management System(数据库管理系统)

#### SQL
    
    Structured Query Language 结构化查询语言，是所有关系型数据库的查询规范。
    定义了操作所有关系型数据库的规则，但每一种数据库操作的方式存在一定区别 

    1.Data Definition Language (DDL 数据定义语言) 如：建库，建表
    2.Data Manipulation Language(DML 数据操纵语言)，如：对表中的记录操作增删改
    3.Data Query Language(DQL 数据查询语言)，如：对表中的查询操作
    4.Data Control Language(DCL 数据控制语言)，如：对用户权限的设置

#### DDL

    注释；
    1.  每条语句以分号结尾，如果在 SQLyog 中不是必须加的。
    2.  SQL 中不区分大小写，关键字中认为大写和小写是一样的
    3.  3 种注释：
        1. --空格      单行注释  一般用这个
        2. /* */      多行注释
        3. #          这是 mysql 特有的注释方式


    对数据库的CRUD
        C(Create 创建)
            create database db1;                           //创建数据库
            create database if not exists db2;             //创建之前查询一下是否存在，如果不存在就创建
            create database db3 character set gbk;         //设置数据库的编码
            create database if not exists db4 character set gbk; 
        R（Retrieve）:查询
            show databases;                                //查看所有数据库
            show create database aa_database;              // 查询创建数据库,查看数据库的创建信息（字符集）
        U（Update）;修改字符集
            alter database db4 character set utf8
        D(Delete)删除数据库
            drop database db4;
            drop database if exists db4                    //删除之前判断一下
        
        使用数据库：
        select database();                //查询正在使用的数据库
        use db1;                          //使用数据库：
    对表的CRUD
        数据类型：
            1. int:整数类型         age int,
            2. double:小数类型      score double(5,2)    //五位有效数字，保留两位小数
            3. date:日期，只包含年月日，yyyy-MM-d			
            4. datetime:日期，包含年月日时分秒	 yyyy-MM-dd HH:mm:ss
            5. timestamp:时间戳型	年月日时分秒	 yyyy-MM-dd HH:mm:ss	
                * 如果将来不给这个字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值
            6. varchar：字符串
                双引号和单引号都可以
        		* name varchar(20)
        			        
        表的操作
        use db1;                            //使用数据库
        show tables;                        //显示该数据库中的所有表
        desc table_name；                    //查询表信息   describe描述
        
        1. C(Create):创建                    // 注意：最后一列，不需要加逗号
                create table stu(
                    id int,
                    name varchar(32),
                    age int ,
                    score double(4,1),
                    birthday date,
                    insert_time timestamp
                );
            复制表：
                * create table stu like stu2;	  	
        2. R(Retrieve)：查询
            * show tables;          //查询某个数据库中所有的表名称		
            * desc 表名;             // 查询表结构
        3. U(Update):修改 
                alter table stu rename to stu2;        //修改表名
                alter table stu character set gbk;     //修改表的字符集
                show create table stu2;                       //查看表信息      
                alter table student add name varchar(10);        //添加一列
                alter table student change name name2 varchar(11)//修改列名称 类型
                alter table table_name modify name varchar(15);                   //修改列的类型
                alter table student drop name;                   //删除列
        4. D(Delete):删除
            *drop table stu;
            *drop table  if exists stu ;                //删除之前判断是否存在
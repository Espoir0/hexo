---
title: jdbc
date: 2019-8-19
tags:
- web基础
categories:
- web基础
---
***
<!-- more -->
#### 1.概念
Java DataBase Connectivity  Java 数据库连接， 用Java操作数据库.
JDBC本质：官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。
一般使用的时候，面向接口编程，使用多态，调用实现类的方法
使用:
```java
public class Demo1 {
    public static void main(String[] args) throws Exception {
        //1.导入驱动包
        //2.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //3.获取数据库连接对象
        Connection con= DriverManager.getConnection("jdbc:mysql://localhost:3306/aa_db","root","root");
        // 4.定义sql语句
        String sql="update meinv4 set xw=40 where id=1";
        //5.获取执行Sql对象 Statement
        Statement sm=con.createStatement();
        //6执行sql语句
        int count =sm.executeUpdate(sql);
        //7.处理结果
        System.out.println(count);
        //8. 释放资源
        sm.close();
        con.close();
    }
}
```
解析:

    1.注册驱动
    Class.forName("com.mysql.jdbc.Driver");  //在加载类的时候执行静态代码块，注册驱动
    在com.mysql.jdbc.Driver类中存在静态代码块
    ```java
    static {
        try {
            java.sql.DriverManager.registerDriver(new Driver());  //加载类的时候注册了mysql驱动
        } catch (SQLException E) {
        throw new RuntimeException("Can't register driver!");
            }
        }
    //注意：mysql5之后的驱动jar包可以省略注册驱动的步骤。因为jar包中包含了注册语句，会自动读取
    ```   
    2. DriverManager：驱动管理类
         * 功能： 1. 注册驱动：告诉程序该使用哪一个数据库驱动jar
         static void registerDriver(Driver driver) ://静态方法，注册与给定的驱动程序 DriverManager 。 
        
    3. 获取数据库连接：
    static Connection getConnection(String url, String user, String password) 
    
     参数：
    * url：jdbc:mysql://localhost:3306/db3
    * 细节：如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url简写为：jdbc:mysql:///aa_db
    
    4. Connection：//数据库连接对象,java代码和数据库之间的桥梁
    	1. 获取执行sql 的对象
    		* Statement createStatement()
    		* PreparedStatement prepareStatement(String sql)  
    	2. 管理事务：
    		* 开启事务：setAutoCommit(boolean autoCommit) ：调用该方法设置参数为false，即开启事务
    		* 提交事务：commit() 
    		* 回滚事务：rollback() 
    5. Statement：执行sql的对象
    	//执行sql
    	1. boolean execute(String sql) ：//可以执行任意的sql 了解 ,用的不多
        2. int executeUpdate(String sql) ：执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句
    	//* 返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。
    	3. ResultSet executeQuery(String sql)  ：执行DQL（select)语句
#### 练习：增改删
```java
public class Demo2 {
    public static void main(String[] args) {
        Connection conc = null;
        Statement stmt = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");    //注册驱动
            String sql ="insert into meinv4 (name,xw) values('cc',39)";    //sql语句
            //String sql="update meinv4 set xw=55 where id=5";
            //String sql="delete from meinv4 where id=6";
            //String sql="create table stu (id int,name varchar(20))";   //也可以创建表但不常用
            //获取connection 对象

            conc = DriverManager.getConnection("jdbc:mysql:///aa_db","root","root");
            //获取执行sql对象

            stmt = conc.createStatement();
            //执行sql
            int count=stmt.executeUpdate(sql);

            //输出执行结果
            System.out.println(count);
            if(count>0){
                System.out.println("添加成功");
            }else {
                System.out.println("添加失败");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
             stmt.close();
        }
    }
}
```
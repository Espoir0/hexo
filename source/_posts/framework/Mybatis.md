---
title: 
date: 2019-08-02
tags:
- Mybatis
categories:
- 框架
---
Mybatis
<!-- more -->
Mybatis 是一个优秀的基于 java 的持久层框架，它内部封装了 jdbc，使开发者只需要关注 sql 语句本身，而不需要花费精力去处理加载驱动、创建连接、创建 statement 等繁杂的过程。
Mybatis 通过 xml 或注解的方式将要执行的各种 statement 配置起来，并通过 java 对象和 statement 中
sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为 java 对象并返回。
采用 ORM 思想解决了实体和数据库映射的问题，对 jdbc 进行了封装，屏蔽了 jdbc api 底层访问细节，使我们不用与 jdbc api 打交道，就可以完成对数据库的持久化操作。

基础配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <mapper namespace="com.itheima.dao.IUserDao">
    <!-- 配置查询所有操作 --> <select id="findAll" resultType="com.itheima.domain.User">
    select * from user
    </select>
</mapper>
```

UserDao 接口就是我们的持久层接口（也可以写成 UserDao 或者 UserMapper）Dao数据访问对象


```xml
<!-- 保存用户-->

<insert id="saveUser" parameterType="com.itheima.domain.User">
    insert into user(username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address})
</insert>
``` 
parameterType 属性：
代表参数的类型，因为我们要传入的是一个类的对象，所以类型就写类的全名称。
sql 语句中使用#{}字符： 它代表占位符，相当于原来 jdbc 部分所学的?，都是用于执行语句时替换实际的数据。
具体的数据是由#{}里面的内容决定的。
|#{}中内容的写法：
由于我们保存方法的参数是 一个 User 对象，此处要写 User 对象中的属性名称。它用的是 ognl 表达式。
ognl 表达式：
    apache 提供的一种表达式语言，全称是：Object Graphic Navigation Language 对象图导航语言
    按照一定的语法格式来获取数据的。语法格式就是使用 #{对象.对象}的方式

|#{user.username}它会先去找 user 对象，然后在 user 对象中找到 username 属性，并调用
getUsername()方法把值取出来。但是我们在 parameterType 属性上指定了实体类名称，所以可以省略 user.
而直接写 username。

```sql
<!-- 根据名称模糊查询 --> 
<select id="findByName" resultType="com.itheima.domain.User" parameterType="String">
    select * from user where username like #{username}
</select>
<!-- 根据名称模糊查询 --> 
<select id="findByName" parameterType="string" resultType="com.itheima.domain.User">
select * from user where username like '%${value}%'
</select>
```
我们在上面将原来的#{}占位符，改成了${value}。注意如果用模糊查询的这种写法，那么${value}的写法就是固定的，不能写成其它名
#{}表示一个占位符号
通过#{}可以实现 preparedStatement 向占位符中设置值，自动进行 java 类型和 jdbc 类型转换，
#{}可以有效防止 sql 注入。 #{}可以接收简单类型值或 pojo 属性值。 如果 parameterType 传输单个简单类
型值，#{}括号中可以是 value 或其它名称。
${}表示拼接 sql 串
通过${}可以将 parameterType 传入的内容拼接在 sql 中且不进行 jdbc 类型转换， ${}可以接收简
单类型值或 pojo 属性值，如果 parameterType 传输单个简单类型值，${}括号中只能是 value。
parameterType 配置参数      
基 本 类 型 和 String 我 们 可 以 直 接 写 类 型 名 称 ， 也 可 以 使 用 包 名 . 类 名 的 方 式 ， 例 如 ：
java.lang.String。
实体类类型，目前我们只能使用全限定类名。
究其原因，是 mybaits 在加载时已经把常用的数据类型注册了别名，从而我们在使用时可以不写包名，
而我们的是实体类并没有注册别名，所以必须写全限定类名

resultMap 标签可以建立查询的列名和实体类的属性名称不一致时建立对应关系。从而实现封装。
在 select 标签中使用 resultMap 属性指定引用即可。同时 resultMap 可以实现将查询结果映射为复杂类
型的 pojo，比如在查询结果映射对象中包括 pojo 和 list 实现一对一查询和一对多查询。
4.2.1 定义 resultMap
<!-- 建立 User 实体和数据库表的对应关系
传智播客——专注于 Java、.Net 和 Php、网页平面设计工程师的培训
北京市昌平区建材城西路金燕龙办公楼一层 电话：400-618-9090
type 属性：指定实体类的全限定类名
id 属性：给定一个唯一标识，是给查询 select 标签引用用的。
--> <resultMap type="com.itheima.domain.User" id="userMap"> <id column="id" property="userId"/>
<result column="username" property="userName"/>
<result column="sex" property="userSex"/>
<result column="address" property="userAddress"/>
<result column="birthday" property="userBirthday"/>
</resultMap>
id 标签：用于指定主键字段
result 标签：用于指定非主键字段
column 属性：用于指定数据库列名
property 属性：用于指定实体类属性名称

Mybatis 中简化编写的 SQL 片段
<!-- 抽取重复的语句代码片段 --> 
```xml
<sql id="defaultSql">select * from user</sql>
```
 引用代码片段<!-- 配置查询所有操作 --> 
<select id="findAll" resultType="user"> <include refid="defaultSql"></include>
</select>
<!-- 根据 id 查询 --> 
<select id="findById" resultType="User" parameterType="int">

bug整理:

    1.Mybatis使用注解形式报错Could not set parameters for mapping: ParameterMapping{property='xxx'
    找了好久才发现是把其中一个#{}，写成了#(),真的坑，
    
    2.注意数据库类型和实体类属性的类型以及前端传过来的类型是否一致，如果不一致可以在get/set方法中进行转化 
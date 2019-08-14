---
title: Mybatis
date: 2019-07-26
tags:
- Mybatis
categories:
- 框架
---
xml配置mybatis环境搭建
<!-- more -->
1.创建maven项目
2.数据库建表
```xml
    <dependencies><!--mybatis 依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>

        <dependency><!--mysql-->
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.37</version>
        </dependency>

        <dependency><!--junit 单元测试-->
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```
4.  主配置文件SqlMapConfig.xml <!--mybatis的主配置信息-->
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--头文件-->
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置信息-->
<configuration>
    <!--配置环境 default中的值不是固定的，但必须和id 值相同-->
    <environments default="mysql">
    
        <!--配置mysql的环境-->
        <environment id="mysql">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--配置连接数据库的四个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis1"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="alone.aa.dao.IUserDao.xml"/>
    </mappers>
</configuration>    
```
IUSerDao.xml  映射配置文件；接口的配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--配置类名的映射-->
<mapper namespace="alone.aa.dao.IUserDao">
    <!--配置对应方法的映射，id就是方法名  ,resultType表示告诉Mybatis把sql的执行语句返回的结果封装为user对象-->
    <select id="findAll" resultType="alone.aa.domain.User">
        select * from user
    </select>
</mapper>
```
### 注解配置Mybatis
UserDao
```java
public interface IUserDao {
    //查询所有用户
    @Select("select * from user")
    List<User>  findAll();
}
```
主配置文件中：注解配置和XML配置的区别是，注解配置时Mapper的属性为class而xml配置时Mapper属性为resource	
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--头文件-->
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置信息-->
<configuration>
    <!--配置环境 default中的值不是固定的，但必须和id 值相同-->
    <environments default="mysql">
    
        <!--配置mysql的环境-->
        <environment id="mysql">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--配置连接数据库的四个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis1"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <!--主配置文件必须要：将里面的Mapper中的resourse改为class-->
        <mapper class="alone.aa.dao.IUserDao"></mapper>
    </mappers>
</configuration>    
```
测试类:
```java
public class Test1 {
    public static void main(String[] args) throws IOException {
        //1.读取配置文件
        InputStream is= Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory 工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(is);
        //3.使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User> user = userDao.findAll();
        System.out.println(user);
        //6.释放资源
        session.close();
        is.close();
    }
}
```


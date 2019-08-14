---
title: JPA
date: 2019-07-27
tags:
- JPA
categories:
- Framework
---
1. ORM简介
2. JPA简介
3. JPA使用
4. Spring Data JPA]
***
<!-- more -->
### ORM简介:
1. ORM（Object-Relational Mapping）表示对象关系映射
2. 在面向对象的软件开发中，通过ORM，就可以把对象映射到关系型数据库中，只要有一套程序能够做到建立对象与数据库的关联，操作对象就可以直接操作数据库数据，就可以说这套程序实现了ORM对象关系映射.ORM就是建立实体类和数据库表之间的关系，从而达到操作实体类就相当于操作数据库表的目的。
2. 常见的orm框架：Mybatis（ibatis）、Hibernate、
3. Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据库表建立映射关系，是一个全自动的orm框架，hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。
### JPA的介绍
1. JPA:Java Persistence API， 即Java 持久化API，是SUN公司推出的一套基于ORM的规范，内部是由一系列的接口和抽象类构成。
2. JPA可以通过注解描述对象和关系表的映射关系，并将运行期的实体对象持久化到数据库中
3. JPA的查询语言是面向对象而非面向数据库的，它以面向对象的自然语法构造查询语句，可以看成是Hibernate HQL的等价物。
4. JPA定义了独特的JPQL（Java Persistence Query Language），JPQL是EJB QL的一种扩展，它是针对实体的一种查询语言，操作对象是实体，而不是关系数据库的表，而且能够支持批量更新和修改、JOIN、GROUP BY、HAVING 等通常只有 SQL 才能够提供的高级查询特性，甚至还能够支持子查询
5. JPA是规范，Hibernate和toplink都是是一种JPA实现，就和JDBC与jdbc的驱动类似，调用jpa的时候执行的是Hibernate，而Hibernate底层又是jdbc,jdbc底层就是各驱动厂商提供的实现类
### JPA使用
#### 创建maven项目，不选择模板，直接next
#### 导入坐标

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.hibernate.version>5.0.7.Final</project.hibernate.version>
</properties>
```

```xml
	<dependencies>
		<!-- junit -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		
		<dependency><!--lombok-->
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>

		<!-- hibernate对jpa的支持包 -->
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-entitymanager</artifactId>
			<version>${project.hibernate.version}</version>
		</dependency>

		<!-- c3p0连接池 -->
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-c3p0</artifactId>
			<version>${project.hibernate.version}</version>
		</dependency>

		<!-- log日志 -->
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
		</dependency>

		<!-- Mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.6</version>
		</dependency>
	</dependencies>
```
#### JPA的配置
在resources目录下新建 `META-INF`文件夹，然后在`META-INF`文件夹下面新建 `persistence.xml`文件
在`persistence.xml`文件中配置JPA
```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://java.sun.com/xml/ns/persistence
    http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
             version="2.0">
    <!--配置持久化单元  persistence持续
        name：持久化单元名称
        transaction-type：事务类型
             RESOURCE_LOCAL：本地事务管理
             JTA：分布式事务管理 -->

    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!--配置JPA规范的服务提供商 -->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <properties>
            <!-- 数据库驱动 -->
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
            <!-- 数据库地址 -->
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/arryn_aa" />
            <!-- 数据库用户名 -->
            <property name="javax.persistence.jdbc.user" value="root" />
            <!-- 数据库密码 -->
            <property name="javax.persistence.jdbc.password" value="root" />

            <!--jpa提供者的可选配置：我们的JPA规范的提供者为hibernate，所以jpa的核心配置中兼容hibernate的配置
              show_sql :显示sql
              auto自动创建数据库表
                      create ,运行时建表，如果有表先删除在创建
                      update,运行时创建表，有表则不会创建
                      none
              format_sql : 显示sql的格式化信息
             -->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.format_sql" value="true" />
            <property name="hibernate.hbm2ddl.auto" value="create" />
        </properties>
    </persistence-unit>
</persistence>
```
#### 创建实体类`User.java`
```java
/**
 * @GeneratedValue 指定主键的生成方式。。
 *         	属性：strategy ：指定主键生成策略。
 *         	GenerationType.IDENTITY  //mysql
 *         	GenerationType.SEQUENCE  //oracle
 *         	GenerationType.AUTO      //程序自动判断
 * @Column
 *        作用：指定实体类属性和数据库表之间的对应关系
*         	属性：
*         		name：指定数据库表的列名称。
*         		unique：是否唯一
*         		nullable：是否可以为空
*         		inserttable：是否可以插入
*         		updateable：是否可以更新
*         		columnDefinition: 定义建表时创建此列的DDL
*         		secondaryTable: 从表名。如果此列不建在主表上（默认建在主表），该属性定义该列所在从表的名字搭建开发环境[重点]
*/
//要导入javax.persistence下的注解
@Data
@Entity  //声明实体类
@Table(name = "userJPA")   //建立实体类和表的映射关系
public class User {
    @Id   //配置主键
    @GeneratedValue(strategy = GenerationType.IDENTITY) //配置主键的生成策略，自增
    @Column(name = "id")  //声明该属性和数据库表中的id字段字段映射
    private Long id;
    @Column(name="name")
    private String name;
    @Column(name = "password")
    private String pwd;
}

```
#### 测试类:
```java
public class TestJPA {
    @Test
    public void test1(){
        /**
         * 创建实体管理类工厂，借助Persistence的静态方法获取
         * 		其中传递的参数为持久化单元名称，需要jpa配置文件中指定
         */
        EntityManagerFactory factory=Persistence.createEntityManagerFactory("myJpa");
        //创建实体管理类
        EntityManager em=factory.createEntityManager();
        //获取事务对象
        EntityTransaction transaction=em.getTransaction();
        //开启事务
        transaction.begin();
        //
        User user = new User();
        user.setName("李白");
        user.setPwd("1234");

        //保存  persist持续
        em.persist(user);
        //提交事务
        transaction.commit();
        //释放资源
        em.close();
        factory.close();
    }
}

```
#### JPA的API介绍
1. Persistence对象
Persistence对象主要作用是用于获取EntityManagerFactory对象的 。通过调用该类的createEntityManagerFactory静态方法，根据配置文件中持久化单元名称创建EntityManagerFactory。
2. EntityManagerFactory
EntityManagerFactory 接口主要用来创建 EntityManager 实例
由于EntityManagerFactory 是一个线程安全的对象（即多个线程访问同一个EntityManagerFactory 对象不会有线程安全问题），并且EntityManagerFactory 的创建极其浪费资源，所以在使用JPA编程时，我们可以对EntityManagerFactory 的创建进行优化，只需要做到一个工程只存在一个EntityManagerFactory 即可
创建工具类
```java
public class Utils {
    // JPA的实体管理器工
    private static EntityManagerFactory factory=null;

    static {
        factory = Persistence.createEntityManagerFactory("myJpa");
    }

    //使用工厂创建对象
    public static EntityManager getEntityManeger(){
        return factory.createEntityManager();
    }
}

```
3. EntityManager
在 JPA 规范中, EntityManager是完成持久化操作的核心对象。实体类作为普通 java对象，只有在调用 EntityManager将其持久化后才会变成持久化对象。EntityManager对象在一组实体类与底层数据源之间进行 O/R 映射的管理。它可以用来管理和更新 Entity Bean, 根椐主键查找 Entity Bean, 还可以通过JPQL语句查询实体。我们可以通过调用EntityManager的方法完成获取事务，以及持久化数据库的操作

方法说明：

	getTransaction : 获取事务对象
	persist ： 保存操作
	merge ： 更新操作
	remove ： 删除操作
	find/getReference ： 根据id查询

4. EntityTransaction

EntityTransaction是完成事务操作的核心对象

    begin：开启事务
    commit：提交事务
    rollback：回滚事务
#### 项目结构
![mark](http://pvgivgzah.bkt.clouddn.com/blog/20190730/jtBWWunkicTo.png?imageslim)
#### 测试结果:
![mark](http://pvgivgzah.bkt.clouddn.com/blog/20190730/m0Pf4WvpKu6y.png?imageslim)


    
    
### Spring Data JPA 
Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套JPA应用框架
Spring Data JPA 与 JPA和hibernate之间的关系：
JPA是一套规范，内部是有接口和抽象类组成的。hibernate是一套成熟的ORM框架，而且Hibernate实现了JPA规范，所以也可以称hibernate为JPA的一种实现方式，我们使用JPA的API编程，（面向接口编程）
Spring Data JPA是Spring提供的一套对JPA操作更加高级的封装，是在JPA规范下的专门用来进行数据持久化的解决方案。


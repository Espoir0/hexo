---
title: ssm整合入门
date: 2019-08-11
tags: 
- ssm整合
categories:
- ssm整合
---
***
<!-- more -->
### javaWeb三层架构，就是用Spring去整合其他两个框架

表现层|业务层|持久层
---|:--:|---:
SpringMVC|Spring|Mybatis

### Spring环境搭建
1.创建maven项目，不选择模板，->next
2.main->新建webapp->新建WEB-INF，并在pom.xml中添加 `<packaging>war</packaging>`
3.数据库:
```sql
CREATE TABLE hero(
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(100),
    ranking INT
    );
```
4.pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.ice</groupId>
    <artifactId>ssm</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <spring.version>5.0.2.RELEASE</spring.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <shiro.version>1.2.3</shiro.version>
        <mysql.version>5.1.6</mysql.version>
        <mybatis.version>3.4.5</mybatis.version>
    </properties>
    <dependencies>
        <!-- spring -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.6.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version> 
            <scope>compiler</scope>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!-- log start -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
            <!-- log end -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>ssm</finalName>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.2</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                        <encoding>UTF-8</encoding>
                        <showWarnings>true</showWarnings>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

</project>
```
5.resource->applicationContext.xml 配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
        <!-- 配置 spring 创建容器时要扫描的包 -->
    <context:component-scan base-package="com.espoir">
    <!--制定扫包规则，不扫描@Controller 注解的 JAVA 类，控制层交给SpringMVC处理 -->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
    </context:component-scan>
</beans>
```
6.resource->log4j.properties 配置文件
```properties
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=info, CONSOLE, LOGFILE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n

# LOGFILE is set to be a File appender using a PatternLayout.
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=d://axis.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n


```
7.创建包 domain ,controller,service,dao
8.Hero
```java
@Data
public class Hero {
    private String name;
    private Integer ranking;
}
```
9.dao
```java
public interface HeroDao {
    //查询所有Hero
    List<Hero> findAll();
    //添加Hero
    void save(Hero hero);
}
```
10.service
```java
public interface HeroService {
    List<Hero> findAll();

    void save(Hero hero);
}
```
11.serviceImpl
```java
@Service("heroService")
public class HeroServiceImpl implements HeroService {

    @Override
    public List<Hero> findAll() {
        System.out.println("Service执行");
        return null;
    }

    @Override
    public void save(Hero hero) {
    }
}
```
12.测试spring的环境搭建是否成功,输出Service执行，则环境搭建成功
```java
public class Test {

    @org.junit.Test
    public void testService(){
        //加载配置文件
        ApplicationContext ac=new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
        //获取Bean对象
        HeroService heroService = (HeroService)ac.getBean("heroService");

        heroService.findAll();
    }
}
```

### SpringMVC环境搭建
1.webapp->WEB-INF->web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5">
    <display-name>ssm_web</display-name>

    <!-- 配置 spring mvc 的核心控制器 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <!--加载springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--启动服务器创建servlet-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--解决中文乱码的过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```
2.resource->springmvc.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置创建 spring 容器要扫描的包controller -->
    <context:component-scan base-package="com.espoir">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置视图解析器-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--过滤静态资源-->
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>

    <!--开启注解-->
    <mvc:annotation-driven/>

</beans>
```
3.WEB-INF->pages->list.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        findall
    </body>
</html>
```
4.index.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <a href="/Hero/findAll"> findAll</a>
    
    </body>
</html>
```
5.Controller中测试findAll方法
```java
@Controller
@RequestMapping("/Hero")
public class HeroController {

    @RequestMapping("/findAll")
    public String findAll(){
        return "list";
    }
    
}
```
6.edit->configuration->点击加号->tomcatServer->local->然后配置启动的端口，以及项目路径等
7.在前端页面访问index.jsp测试findAll方法

### Spring整合SprngMVC
1. 启动tomcat服务器的时候，需要加载spring配置文件
2. 服务器启动的时候创建ServletContext域对象，服务器关闭的时候销毁该对象
3. 使用监听器，监听ServletContext的创建和销毁
4. 监听器去加载Spring的配置文件，创建WEB版本工厂，存储ServletContext对象

在webapp->web.xml中添加监听器
```xml
<!--配置Spring的监听器，默认加载WEB-INF目录下的 applicationContext.xml文件-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--设置配置文件的路径-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
```
controller改造
```java
@Controller
@RequestMapping("/Hero")
public class HeroController {

    @Autowired
    private HeroService heroService;

    @RequestMapping("/findAll")
    public String findAll(){
        heroService.findAll();
        return "list";
    }
}
```
启动服务器，点击index.jsp中的链接，控制台打印，则表示整合成功

### 搭建Mybatis环境
resource->SqlMapConfig.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://59.110.243.60:33306/ssm?characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="admin"/>
            </dataSource>
            
        </environment>
    </environments>

    <!--引入映射配置文件-->
    <mappers>
        <!--引入某个具体Dao  <mapper class="com.espoir.dao.HeroDao"/>-->
        <package name="com.espoir.dao"/>
    </mappers>
</configuration>
```
heroDao
```java
public interface HeroDao {
    //查询所有Hero
    @Select("select * from hero")
    List<Hero> findAll();

    @Insert("insert into hero1 (username,ranking) values(#{username},#{ranking})")
    void save(Hero hero);
}
```
测试方法
```java
@Test
public void testMybatis() throws IOException {
    //加载配置文件
    InputStream is = Resources.getResourceAsStream("SqlMapConfig.xml");
    //创建SqlSessionFactory对象
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
    //获取SqlSession对象
    SqlSession sqlSession = factory.openSession();
    //获取代理对象
    HeroDao heroDao = sqlSession.getMapper(HeroDao.class);
    //查询
    Hero hero = new Hero();
    hero.setUsername("李白");
    hero.setRanking(1);
    heroDao.save(hero);

    sqlSession.commit(); //提交事务，增删改都需要提交事务
    //释放资源
    sqlSession.close();
    is.close();
}
```
注意:使用的是linux服务器，数据库中文乱码在，查看数据库的编码，并且在url后面加上编码格式

### spring整合Mybatis
applicationContext.xml文件中添加下面代码后就可以删除Mybatis的SqlMapConfig.xml文件了
```xml
<!--spring整合Mybatis-->
<!--spring整合Mybatis-->
    <!--配置连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://59.110.243.60:33306/ssm?characterEncoding=utf8"/>
        <property name="user" value="root"/>
        <property name="password" value="admin"/>
    </bean>

    <!--配置sqlSessionFactory工厂-->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置dao所在的包-->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.espoir.dao"/>
    </bean>

    <!--配置Spring框架声明式事务管理-->
    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>  <!--引入了上面配置的数据源-->
    </bean>
    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="find*" read-only="true"/><!--配置find开头的方法，只读-->
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>

    <!--配置AOP增强-->
    <aop:config><!--impl.*ServiceIpml.*(..)) 表示Impl包下，所有Service实现类，的所有方法 -->
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.espoir.service.impl.*ServiceIpml.*(..))"/>
    </aop:config>
```
dao
```java
@Repository  //要加上注解
public interface HeroDao {
    //查询所有Hero
    @Select("select * from hero")
    List<Hero> findAll();

    @Insert("insert into hero1 (username,ranking) values(#{username},#{ranking})")
    void save(Hero hero);
}
```
index.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <a href="/hero/findAll"> findAll</a>
        <form action="/hero/save">
            姓名:<input type="text" name="username">
            排行:<input type="text" name="ranking">
            <input type="submit" value="保存">
        </form >
    </body>
</html>
```
list.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <c:forEach items="${list}" var="hero">
            ${hero.username}
        </c:forEach>
    </body>
</html>
```
controller
```java
@Controller
@RequestMapping("/hero")
public class HeroController {

    @Autowired
    private HeroService heroService;

    @RequestMapping("/findAll")
    public String findAll(Model model){
        List<Hero> list = heroService.findAll();
        //将查到的数据存入model
        model.addAttribute("list",list);
        return "list";
    }

    @RequestMapping("/save")
    public void save(Hero hero, HttpServletRequest req, HttpServletResponse resp) throws IOException {
        heroService.save(hero);//请求之后保存数据，重定向到findAll
        resp.sendRedirect(req.getContextPath()+"/hero/findAll");

    }
}

```
### 项目结构:
```
├─java
│  └─com
│      └─espoir
│          ├─controller
│          │      HeroController.java
│          │
│          ├─dao
│          │      HeroDao.java
│          │
│          ├─domain
│          │      Hero.java
│          │
│          ├─service
│          │  │  HeroService.java
│          │  │
│          │  └─impl
│          │          HeroServiceImpl.java
│          │
│          └─test
│                  Testt.java
│
├─resources
│      applicationContext.xml
│      log4j.properties
│      springmvc.xml
│
└─webapp
    │  index.jsp
    │
    └─WEB-INF
        │  web.xml
        │
        └─pages
                list.jsp

```

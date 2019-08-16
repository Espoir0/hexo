---
title: ssm整合旅游项目
date: 2019-0-0
tags:
- ssm
categories:
- ssm
---
***
<!-- more -->
#### 环境搭建 
数据库建表
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
SELECT * FROM product;
```
创建maven项目，不选模板，next
pom.xml中导入依赖
创建webapp目录，webapp->WEB-INF->pages(该目录存放页面)
webapp->index.jsp(改文件是访问页面，访问该页面跳转到WEB-INF->pages->main.jsp后台主页面)
搭建ssm环境
resource->applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx 
	http://www.springframework.org/schema/tx/spring-tx.xsd">
	
	<!-- 开启注解扫描，管理service和dao -->
	<context:component-scan base-package="com.espoir">
		<!--制定扫包规则，不扫描@Controller 注解的 JAVA 类，控制层交给SpringMVC处理 -->
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
	</context:component-scan>

	<!--spring整合Mybatis-->
	<!--读取连接数据库的配置文件-->
	<context:property-placeholder location="classpath:db.properties"/>

	<!-- 配置连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
   		<property name="driverClass" value="${jdbc.driver}" />
   		<property name="jdbcUrl" value="${jdbc.url}" />
   		<property name="user" value="${jdbc.username}" />
   		<property name="password" value="${jdbc.password}" />
   	</bean>

	<!--  声明Spring去创建SqlSessionFactoryBean -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
   		<property name="dataSource" ref="dataSource" />
   	</bean>

	<!--配置dao所在的包-->
	<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.espoir.dao"/>
	</bean>
	<!--end-->

	<!-- 配置Spring的声明式事务管理 -->
   	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
   		<property name="dataSource" ref="dataSource"/>
   	</bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>
	
</beans>














```
resource->db.properties,Mybatis数据库配置（数据库配置文件不写在Spring-mvc.xml中，方便管理）
```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://59.110.243.60:33306/ssm?characterEncoding=utf8&useSSL=false
jdbc.username=root
jdbc.password=admin
```
resource->log4j.properties
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
resource->spring-mvc.xml
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 配置创建 spring 容器要扫描的controller注解 -->
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
    <mvc:resources mapping="/img/**" location="/img/"/>
    <mvc:resources mapping="/plugins/**" location="/plugins/"/>

    <!--开启注解-->
    <mvc:annotation-driven/>

    <!--支持aop注解支持 ，Aop底层是代理技术，cglib代理，生成子类对象，proxy-target-class="true"/使用 cglib代理-->
    <aop:aspectj-autoproxy proxy-target-class="true"/>

</beans>
```
webapp->WEB-INF->web.xml(项目结构->WEB->然后把web.xml文件添加到部署文件中)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5">
    <display-name>ssm_web</display-name>

    <!--配置Spring的监听器，默认加载WEB-INF目录下的 applicationContext.xml文件-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--设置配置文件的路径-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>

    <!-- 配置 spring mvc 的核心控制器 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <!--加载springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring_mvc.xml</param-value>
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

    <!--初始加载文件-->
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>

</web-app>
```
tomcat环境配置
点击加号->tomcat server->local->配置端口，项目路径，以及要部署的项目

#### 产品添加功能
main.jsp,aside.jsp,header.jsp,project-list.jsp,project-add.jsp;
创建产品的controller,service,product,utils,类完成产品添加功能
主要细节:
1. 点击新建添加产品切换WEB-INF里面的页面，由于WEB-iNF下的资源不能直接访问，通过Controller中提供访问，然后跳转页面即可
2. 完成添加功能之后，重定向，刷新一次页面，
```java
//保存数据
@RequestMapping("/save")
public String save(Product product){
    productService.save(product);
    //添加结束后重新查找一次，刷新，
    return "redirect:findAll";
}
```
3. 使用lombok插件设置实体类的getset方法，部分方法需要自己实现，用来转化成也特格式的数据向前端展示
```java
public String getProductStatusStr() {
        if (productStatus!=null){
            if (productStatus==0){
                productStatusStr="关闭";
            }
            if (productStatus==1){
                productStatusStr="开启";
            }
        }
        return productStatusStr;

    }

    public String getDepartureTimeStr() {
        if (departureTime!=null){
            departureTimeStr=Utils.dateToString(departureTime);
        }
        return departureTimeStr;
    }
```
4. 添加功能的时候前端传过来的时间和金额是字符串，需要在set方法中转化一下，日期需要加特定注解转化
```java
//将前端字符串格式转化
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm")
private Date departureTime; // 出发时间
```
#### 用户登录
Spring Security介绍
Spring Security 的前身是 Acegi Security ，是 Spring 项目组中用来提供安全认证服务的框架。
(https://projects.spring.io/spring-security/) Spring Security 为基于J2EE企业应用软件提供了全面安全服务。特别
是使用领先的J2EE解决方案-Spring框架开发的企业软件项目。人们使用Spring Security有很多种原因，不过通常吸
引他们的是在J2EE Servlet规范或EJB规范中找不到典型企业应用场景的解决方案。 特别要指出的是他们不能再
WAR 或 EAR 级别进行移植。这样，如果你更换服务器环境，就要，在新的目标环境进行大量的工作，对你的应用
系统进行重新配 置安全。使用Spring Security 解决了这些问题，也为你提供很多有用的，完全可以指定的其他安
全特性。 安全包括两个主要操作。
“认证”，是为用户建立一个他所声明的主体。主题一般式指用户，设备或可以在你系 统中执行动作的其他系
统。
“授权”指的是一个用户能否在你的应用中执行某个操作，在到达授权判断之前，身份的主题已经由 身份验证
过程建立了。
这些概念是通用的，不是Spring Security特有的。在身份验证层面，Spring Security广泛支持各种身份验证模式，
这些验证模型绝大多数都由第三方提供，或则正在开发的有关标准机构提供的，例如 Internet Engineering Task
Force.作为补充，Spring Security 也提供了自己的一套验证功能。
Spring Security 目前支持认证一体化如下认证技术： HTTP BASIC authentication headers (一个基于IEFT RFC 的
标准) HTTP Digest authentication headers (一个基于IEFT RFC 的标准) HTTP X.509 client certificate exchange
(一个基于IEFT RFC 的标准) LDAP (一个非常常见的跨平台认证需要做法，特别是在大环境) Form-based
authentication (提供简单用户接口的需求) OpenID authentication Computer Associates Siteminder JA-SIG
Central Authentication Service (CAS，这是一个流行的开源单点登录系统) Transparent authentication context
propagation for Remote Method Invocation and HttpInvoker (一个Spring远程调用协议)
![mark](http://simon.bzk.ink/blog/20190815/PiGOlbPgXli8.png?imageslim)
web.xml中添加,
```xml

<param-value>classpath:spring_mvc.xml,classpath*:spring_security.xml</param-value>

<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
resource->创建spring_security.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
 	xmlns:security="http://www.springframework.org/schema/security"
 	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 	xsi:schemaLocation="http://www.springframework.org/schema/beans          
     http://www.springframework.org/schema/beans/spring-beans.xsd          
     http://www.springframework.org/schema/security          
     http://www.springframework.org/schema/security/spring-security.xsd">
     
     <!-- 配置不拦截的资源 -->
     <security:http pattern="/login.jsp" security="none"/>
     <security:http pattern="/failer.jsp" security="none"/>
     <security:http pattern="/css/**" security="none"/>
     <security:http pattern="/img/**" security="none"/>
     <security:http pattern="/plugins/**" security="none"/>
     
     <!-- 
     	配置具体的规则 
     	auto-config="true"	不用自己编写登录的页面，框架提供默认登录页面
     	use-expressions="false"	是否使用SPEL表达式（没学习过）
     -->
     <security:http auto-config="true" use-expressions="false">
     	<!-- 配置具体的拦截的规则 pattern="请求路径的规则" access="访问系统的人，必须有ROLE_USER的角色" -->
     	<security:intercept-url pattern="/**" access="ROLE_USER,ROLE_ADMIN"/>
     	
     	<!-- 定义跳转的具体的页面 -->
     	<security:form-login  
     		login-page="/login.jsp"
     		login-processing-url="/login.do"
     		default-target-url="/index.jsp"
     		authentication-failure-url="/failer.jsp"
     	/>
     	
     	<!-- 关闭跨域请求 -->
     	<security:csrf disabled="true"/>
     	
     	<!-- 退出 -->
     	<security:logout invalidate-session="true" logout-url="/logout.do" logout-success-url="/login.jsp" />
     	
     </security:http>
     
     <!-- 切换成数据库中的用户名和密码 -->
     <security:authentication-manager>
     	<security:authentication-provider user-service-ref="userService">
     		<!-- 配置加密的方式 -->
     		<security:password-encoder ref="passwordEncoder"/>
     	</security:authentication-provider>
     </security:authentication-manager>
     
     <!-- 配置加密类 -->
     <bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
     
     <!-- 提供了入门的方式，在内存中存入用户名和密码 
     <security:authentication-manager>
     	<security:authentication-provider>
     		<security:user-service>
     			<security:user name="admin" password="{noop}admin" authorities="ROLE_USER"/>
     		</security:user-service>
     	</security:authentication-provider>
     </security:authentication-manager>
     -->
     
  </beans>   
```
  


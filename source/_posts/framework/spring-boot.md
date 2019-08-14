---
title: spring boot入门
date: 2019-07-26
tags:
- spring-boot
categories:
- framework
---
1. SpringBoot介绍
2. Spring入门使用
3. SpringBoot整合Spring Data JPA
4. 统一异常处理
5. 异常国际化
<!-- more -->
    约定优于配置的思想

    spring-boot简化了第三方包的引用，通过提供的starter，简化了依赖包的配置

    内嵌tomcat、jetty等web容器，不需要部署war文件。

    SpringBoot不是对Spring功能上的增强，而是提供了一种快速使用Spring的方式

SpringBoot核心:

    起步依赖
    起步依赖本质上是一个Maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖

    自动配置
    Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程，
    才决定Spring配置应该用哪个，不该用哪个。该过程是Spring自动完成的。

### SpringBoot快速入门

```xml
<?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>alone.aa</groupId>
        <artifactId>spring_boot_start</artifactId>
        <version>1.0-SNAPSHOT</version>
</project>
```
SpringBoot要求，项目要继承SpringBoot的起步依赖spring-boot-starter-parent
```xml
    <parent><!--spring boot 父依赖-->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
```
SpringBoot要集成SpringMVC进行Controller的开发，所以项目要导入web的启动依赖

```xml
    <dependencies>
        <dependency>   <!--spring-boot web依赖-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```
编写SpringBoot引导类
要通过SpringBoot提供的引导类起步SpringBoot才可以进行访问

```java
@SpringBootApplication
public class MySpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApplication.class);
    }
}
```
SpringBoot代码解析

    @SpringBootApplication：标注SpringBoot的启动类，该注解具备多种功能
    SpringApplication.run(MySpringBootApplication.class) //代表运行SpringBoot的启动类，
    参数为SpringBoot启动类的字节码对象
   
    
Controller
    在引导类MySpringBootApplication同级包或者子级包中创建QuickStartController
```java
//也可以使用RestController  相当于@Controller和@ResponseBody
@Controller                    
public class QuickStartController {
    @RequestMapping("/quick")   //请求映射，此处的 /可加可不加
    @ResponseBody               //返回json格式的数据
    public String quick(){
        return "springboot 访问成功!";
    }
}
```

SpringBoot工程热部署
    
    经常在开发中反复修改类、页面等资源，每次修改后都需要重新启动才生效，这样每次启动都很麻烦，浪费了大量的时
    间，我们可以在修改代码后不重启就能生效，在 pom.xml 中添加如下配置就可以实现这样的功能，我们称之为热部署
```xml
<!--热部署配置-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
</dependency>
```
注意：IDEA进行SpringBoot热部署失败原因是因为Intellij IDEA默认情况下不会自动编译，需要对IDEA进行自动编译的设置，如下配置自动编译

然后 Shift+Ctrl+Alt+/，选择Registry

### SpringBoot结合Spring DataJPA
   
添加依赖:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
配置文件:application.yml
```yaml
server:
  port: 8888
spring:
  datasource:
      #?characterEncoding=utf-8，目的是避免调用JPA的方法存入数据库中的数据中文乱码
    url: jdbc:mysql://localhost:3306/arryn_aa?createDatabaseIfNotExist=true&serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    show-sql: true      #控制台显示sql
    #hibernate可以根据Entity属性在数据库中自动创建对应的表
    hibernate:
      dialect: org.hibernate.dialect.MySQL5Dialect
      ddl-auto: update   #update: 如果修改了Entity，重启项目，表结构会自动更新
```

```java
@Data  //lombok
@Entity
@Table(name = "user")  //@table注解声明该类对应数据库中一张表,参数`name`表示表的名称
public class User{
    @Id //主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)   //设置自增
    private Long id;
    @Column(length = 16)
    private String username;
    @Column(length = 16)
    private String pwd;
    
    //create_time根据自动封装创建时间、update_time自动更新为最新操作的时间。需要进行如下操作：   
    @CreatedDate
    @Column(name = "create_time")
    private Date createTime;
    
    @LastModifiedDate
    @Column(name = "update_time")
    private Date updateTime;
}
```
UserDao只需要继承JpaRepository 即可，然后直接使用JpaRepository提供的方法了
JpaRepository和JpaSpecificationExecutor都可以继承,两个接口方法有所不同
```java
@Repository          
public interface UserDao extends JpaRepository<User, Long> {
}
```
注: 1.JPA生成的表中的字段按字母顺序排序
    2.进行添加和修改的操作都可以调用save()方法，如果Id字段有值，那么jpa就默认识别为更新操作

### 异常处理AOP
SpringBoot引入AOP
```xml
<!--spring切面aop依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

```java
@Slf4j
@ControllerAdvice
public class GlobalExceptionHandler {
    //使用lombok 自动配置
    //private static final org.slf4j.Logger log=org.slf4j.LoggerFactory.getLogger(GlobalExceptionHandler.class);
    /**
     *统一异常处理，程序中发生名为Exception的异常时，被该方法捕获
     * 返回自定义的结果集对象
     *
     */
    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Result handle(Exception e){
        //log.debug("this is debug log:");
        //log.info("this is info log:");
        //log.error("系统异常",e);
        return new Result(0,"系统异常");
    }
}
```

### 异常信息国际化
[参考链接](http://c.biancheng.net/view/4455.html)
国际化的目的就是根据用户的语言环境的不同向用户输出与之相应的页面
Java 程序的国际化主要通过两个类来完成。
1. java.util.Locale
用于提供本地信息，通常称它为语言环境。不同的语言、不同的国家和地区采用不同的 Locale 对象来表示。
2. java.util.ResourceBundle
该类称为资源包，包含了特定于语言环境的资源对象。当程序需要一个特定于语言环境的资源时（例如字符串资源），程序可以从适合当前用户语言环境的资源包中加载它。采用这种方式可以编写独立于用户语言环境的程序代码，而与特定语言环境相关的信息则通过资源包来提供。

为了实现 Java 程序的国际化，必须事先提供程序所需要的资源文件。资源文件的内容由很多 key-value 对组成，其中 key 是程序使用的部分，而 value 是程序界面的显示。

资源文件的命名可以有如下 3 种形式：

    baseName.properties。
    baseName_language.properties。
    baseName_language_country.properties。

baseName 是资源文件的基本名称，由用户自由定义，但是 language 和 country 必须为 Java 所支持的语言和国家/地区代码。例如：
    
    中国大陆：baseName_zh_CN.properties。
    美国：baseName_en_US.properties。

Java 中的资源文件只支持 ISO-8859-1 编码格式字符，直接编写中文会出现乱码。用户可以使用 Java 命令 native2ascii.exe 解决资源文件的中文乱码问题，使用 MyEclipse 编写资源属性文件，在保存资源文件时 MyEclipse 自动执行 native2ascii.exe 命令


在resource下面新建 `messageResource_en_US.properties` 和 `messageResource_zh_CN.properties`文件
`messageResource_en_US.properties`文件内容 
```properties
welcome=how are you;
welcome2={0},how are you;
```
`messageResource_zh_CN.properties`文件内容：
```properties
welcome=你好呀
welcome2={0},你好呀
```
测试类:
```java
/**
* 测试java输出信息国际化
*/
public class Test {
    public static void main(String[] args) throws UnsupportedEncodingException {
        //取得系统默认的国家语言环境
        Locale locale = Locale.getDefault();
        // 根据国家语言环境加载资源文件
        ResourceBundle rb = ResourceBundle.getBundle("messageResource", locale);
        //配置文件默认gbk，而idea中的其他都是utf-8,所以需要转码
        String msg = new String(rb.getString("welcome").getBytes("ISO-8859-1"), "gbk");
        // 打印出从资源文件中取得的信息
        System.out.println(msg);

        //带占位符的国际化信息
        String msg2 = new String(rb.getString("welcome2").getBytes("ISO-8859-1"), "gbk");
        // 打印出从资源文件中取得的信息
        String format = MessageFormat.format(msg2, "李白");
        System.out.println(format);
    }

}
```
输出结果，（如果你的电脑语言是english就会输出 how are you,是中文则输出你好呀）
```
你好呀
李白,你好呀
```
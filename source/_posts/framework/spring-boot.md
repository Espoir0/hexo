---
title: spring boot入门
date: 2019-07-26
tags:
- spring-boot
categories:
- spring-boot
---
*** 
<!-- more -->
#### 1.SpringBoot简介

    1.约定优于配置的思想
    2.spring-boot简化了第三方包的引用，通过提供的starter，简化了依赖包的配置
    3.内嵌tomcat、jetty等web容器，不需要部署war文件。jar包启动即可
    4.SpringBoot不是对Spring功能上的增强，而是提供了一种快速使用Spring的方式，是spring技术栈的整合

    SpringBoot核心:    
        1.起步依赖
            起步依赖本质上是一个Maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖    
        2.自动配置
            Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程，才决定Spring配置应该用哪个，不该用哪个。该过程是Spring自动完成的。
#### 2.spring boot项目创建
##### 2.1 创建maven项目
0.项目结构
```
├─main
│  ├─java
│  │  └─com
│  │      └─espoir
│  │          │  HelloApplication.java
│  │          │
│  │          └─controller
│  │                  HelloController.java
│  │
│  └─resources
└─test
    └─java
```
1.创建maven项目->next
2.配置依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>alone.aa</groupId>
        <artifactId>spring_boot_start</artifactId>
        <version>1.0-SNAPSHOT</version>
        
        <!--SpringBoot要求，项目要继承SpringBoot的起步依赖spring-boot-starter-parent-->
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.0.1.RELEASE</version>
        </parent>
        
        <!--SpringBoot要集成SpringMVC进行Controller的开发，所以项目要导入web的启动依赖-->
        <dependencies>
            <dependency>   <!--spring-boot web依赖-->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
        </dependencies>
        
        <!--该插件可以键应用打包过程一个可执行的jar包，简化部署-->
        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>               
                </plugin>
            </plugins>
        </build>
</project>
```
3.编写SpringBoot启动类，要通过SpringBoot提供的引导类起步SpringBoot才可以进行访问(注意，启动类不能直接在java目录下面创建，必须自己创建包，然后在包下面创建)
```java
@SpringBootApplication //@SpringBootApplication：标注SpringBoot的启动类，该注解具备多种功能
public class MySpringBootApplication {

    public static void main(String[] args) {
        //代表运行SpringBoot的启动类，参数为SpringBoot启动类的字节码对象
        SpringApplication.run(MySpringBootApplication.class);
    }
}
```
4.Controller在引导类MySpringBootApplication同级包或者子级包中创建QuickStartController
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
@SpringBootApplication //声明这是一个spring-boot应用
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {}
```
    @SpringBootApplication
        @SpringBootConfiguration //该注解标记的类，是spring-boot的配置类
            @Configuration       //spring中的配置注解，表明这是一个配置类，配置文件，配置类也是容器中的一个组件
                @Component       //声明组件
        @EnableAutoConfiguration //开启spring-boot自动配置功能，
            @AutoConfigurationPackage //自动配置包，将主配置类（@SpringBootApplication标注的类）的所在包及子包的所有组件注册到容器中
                @Import({Registrar.class})  //spring的底层注解，给容器中导入一个组件
            @Import({AutoConfigurationImportSelector.class}) 
                AutoConfigurationImportSelector要导入组件的选择器，将所有要导入的组件，以全类名的方式返回，这些组件就会被添加到容器中。 
                
    @RestController  //该注解底层是这两个注解组成的
        @Controller
        @ResponseBody 
##### 2.2使用spring-boot initializr快速创建项目
(创建的时候可能需要安装插件spring assistant)
1.新建项目选择`spring-boot initializr`,创建的时候选择依赖（web）后，pom.xml会自动添加这些依赖，不用再去手动配置
2.项目结构
```
├─main
│  ├─java
│  │  └─com
│  │      └─example
│  │          └─demo
│  │              │  DemoApplication.java
│  │              │
│  │              └─com
│  │                  └─example
│  │                      └─controller
│  │                              HelloController.java
│  │
│  └─resources
│      │  application.properties  //springboot应用配置文件，配置端口等
│      │
│      ├─static      //静态资源 css ,js ,img
│      └─templates   //保存所有模板页面（springboot默认jar包使用嵌入式的tomcat,默认不支持jsp;可以使用模板引擎freemarker,thymeleaf）
└─test
    └─java
        └─com
            └─example
                └─demo
                        DemoApplicationTests.java
```
##### 2.3通过springboto官网创建项目
[springboot官网](https://start.spring.io/)
1.选择springboot版本
2.group,artifact.
3.generate the project
4.下载项目压缩包，解压后用IDEA打开
5.pom.xml文件中添加依赖
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
#### 3.springboot配置文件
1.(properties文件默认jbk编码，读取会导致乱码情况，需要设置为utf-8,setting->file encoding->properties file,设置utf-8 ，勾选自动转换)
配置文件一般在resource目录下
有application.yml和application.properties
YAML（YAML Ain't Markup Language

​	YAML   A Markup Language：是一个标记语言
​	YAML   isn't Markup Language：不是一个标记语言；
标记语言：
YAML：以数据为中心，比json、xml等更适合做配置文件；
k:(空格)v：表示一对键值对（空格必须有）；

以**空格**的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

```yaml
server:
    port: 8081
    path: /hello
```

属性和值也是大小写敏感
```xml
<server>
	<port>8081</port>
</server>

```
#### 2、值的写法

#### 字面量：普通的值（数字，字符串，布尔）

​	k: v：字面直接来写；

​		字符串默认不用加上单引号或者双引号；

​		""：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

​				name:   "zhangsan \n lisi"：输出；zhangsan 换行  lisi

​		''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

​				name:   ‘zhangsan \n lisi’：输出；zhangsan \n  lisi



#### 对象、Map（属性和值）（键值对）：

​	k: v：在下一行来写对象的属性和值的关系；注意缩进

​		对象还是k: v的方式

```yaml
friends:
		lastName: zhangsan
		age: 20
```

行内写法：

```yaml
friends: {lastName: zhangsan,age: 18}
```

#### 数组（List、Set）：

用- 值表示数组中的一个元素

```yaml
pets:
 - cat
 - dog
 - pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```



## 3、配置文件值注入

配置文件

```yaml
person:
    lastName: hello
    age: 18
    boss: false
    birth: 2017/12/12
    maps: {k1: v1,k2: 12}
    lists:
      - lisi
      - zhaoliu
    dog:
      name: 小狗
      age: 12
```
javaBean：

```java
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

```



我们可以导入配置文件处理器，以后编写配置就有提示了

```xml
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
		</dependency>
```



#### SpringBoot工程热部署
    
    经常在开发中反复修改类、页面等资源，每次修改后都需要重新启动才生效，这样每次启动都很麻烦，浪费了大量的时
    间，我们可以在修改代码后不重启就能生效，在 pom.xml 中添加如下配置就可以实现这样的功能，我们称之为热部署
```xml
<!--热部署配置-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
</dependency>
```
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
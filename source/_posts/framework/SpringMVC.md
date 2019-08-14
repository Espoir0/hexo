---
title: springMVC 第一篇
date: 2019-08-02
tags:
- springMVC
categories:
- 框架
---
<!-- more -->
### 一、SpringMVC介绍
SpringMVC三层架构包括：表现层、业务层、持久层。
* 表现层：也就是web层。它负责接收客户端请求，向客户端响应结果，通常客户端使用http协议请求，web 层，web 需要接收 http 请求，完成 http 响应。表现层包括展示层和控制层：控制层负责接收请求，展示层负责结果的展示。
表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。
表现层的设计一般都使用 MVC 模型。（MVC 是表现层的设计模型，和其他层没有关系）
* 业务层：也就是 service 层。它负责业务逻辑处理，和我们开发项目的需求息息相关。web 层依赖业务层，但是业务层不依赖 web 层。业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性。（也就是我们说的，事务应该放到业务层来控制）
*持久层：也就是 dao 层。负责数据持久化，包括数据层即数据库和数据访问层，数据库是对数据进行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过数据访问层将数据持久化到数据库
中。通俗的讲，持久层就是和数据库交互，对数据库表进行曾删改查的


MVC 全名是 Model View Controller，是模型(model)－视图(view)－控制器(controller)是一种用于设计创建 Web 应用程序表现层的模式。MVC 中每个部分各司其职：
* Model（模型）：通常指的就是我们的数据模型。作用一般情况下用于封装数据。
* View（视图）：通常指jsp 或者 html。作用就是展示数据的。通常视图是依据模型数据创建的。
* Controller（控制器）：是应用程序中处理用户交互的部分。作用一般就是处理程序逻辑的

SpringMVC有着清晰的角色划分：
* 前端控制器（DispatcherServlet）
* 请求处理器映射（HandlerMapping）
* 处理器适配器（HandlerAdapter）
* 视图解析器（ViewResolver）
* 处理器或页面控制器（Controller）
* 验证器（ Validator）
* 命令对象（Command 请求参数绑定到的对象就叫命令对象）
* 表单对象（Form Object 提供给表单展示和提交到的对象就叫表单对象）。

SpringMVC 和 Struts2 的优略分析
共同点：
* 它们都是表现层框架，都是基于 MVC 模型编写的。
* 它们的底层都离不开原始 ServletAPI。
* 它们处理请求的机制都是一个核心控制器。
区别：
* Spring MVC 的入口是 Servlet, 而 Struts2 是 Filter 
* Spring MVC 是基于方法设计的，而 Struts2 是基于类，Struts2 每次执行都会创建一个动作类。所以 Spring MVC 会稍微比 Struts2 快些。
* Spring MVC 使用更加简洁,同时还支持 JSR303, 处理 ajax 的请求更方便
(JSR303 是一套 JavaBean 参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注
解加在我们 JavaBean 的属性上面，就可以在需要校验的时候进行校验了。)
Struts2 的 OGNL 表达式使页面的开发效率相比 Spring MVC 更高些，但执行效率并没有比 JSTL 提升，尤其是 struts2 的表单标签，远没有 html 执行效率高。

### 二、入门案例
#### 1.环境搭建
##### 1.创建maven项目不选择模板，直接next

##### 2.在main下创建 webapp->WEB-INF->web.xml文件
```xml
<?xml version="1.0"?>
<!DOCTYPE web-app PUBLIC
       "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
       "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <!--配置前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        
        <init-param><!--配置全局初始化参数 ，让spring去加载类路径下的 springMVC.xml配置文件-->
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>

        <!--配置 servlet 的对象的创建时间点：应用加载时创建。取值只能是非 0 正整数，表示启动顺序 -->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern><!-- / 表示任何请求都会经过这个servlet-->
    </servlet-mapping>

</web-app>
```
<font color="red">注意:web.xml中配置的 servlet-mapping无效时，打开Project Structure界面，Modules>Web>Deployment descriptor，中点击加号添加你的web.xml文件即可</font>


##### 3.webapp->创建 index.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<a href="/hello">点我</a>

</body>
</html>
```

##### 4.创建controller
```java
//控制器
@Controller
public class HelloController {

    @RequestMapping(path = "/hello")
    public String hello(){
        return "success";
    }
}

```
##### 5.resource->创建 xml Configuration file->spring config->文件名springMVC.xml
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

    <!-- 配置创建 spring 容器要扫描的包,开启注解扫描 -->
    <context:component-scan base-package="com.espoir.controller"></context:component-scan>

    <!--视图解析器对象-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--  将后端返回的字符串，对应到该目录下面的文件 /WEB-INF/pages/-->
        <property name="prefix" value="/WEB-INF/pages/"/><!--prefix 前缀-->
        <property name="suffix" value=".jsp"/><!--suffix后缀-->
    </bean>

    <!--开启SpringMvc注解的支持-->
    <mvc:annotation-driven/>
</beans>
```

##### 6.webapp->WEB-INF->创建pages目录->创建success.jsp文件（和controller中返回的字符串对应）
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
入门成功

</body>
</html>

```
##### 7.最后在Edit Configuration中添加点击+添加tomcat服务器，选择tomcat server->local->配置端口和deployment信息选择要部署的项目，配置访问路径即可
##### 8.最后就可以启动你的项目了

#### 启动过程分析：
1. 启动服务加载web.xml配置文件，创建 spring 容器并且初始化容器中的对象。
    DispatcherServlet对象被创建
    web.xml文件中声明了springMVC.xml，然后加载springMVC.xml
    springMVC.xml中配置了spring要扫描的包，根据@controller注解，将HelloController类创建对象
2. 发送请求后台处理请求
    页面发送请求，路径为：项目名称/hello
    前端控制器，dispatcherServlet配置了拦截路径，拦截所有请求，然后根据请求路径找到controller中的方法中转发的路径是根据请求 URL，匹配@RequestMapping 中的路径，
    
3. 匹配到了后，执行对应方法。调用相应的方法返回字符串，前端控制器接受字符串，找到前端解析器对象，解析器收到字符串然后解析成对应的页面，将页面返回给控制器

4. 控制器将页面返回给浏览器，渲染结果视图，响应浏览器。

**DispatcherServlet**：前端控制器：用户请求到达前端控制器，它就是mvc 模式中的 c，dispatcherServlet 是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet 的存在降低了组件之间的耦合
**HandlerMapping**：处理器映射器：HandlerMapping 负责根据用户请求找到 Handler（即处理器）
**Handler**：处理器 它就是具体业务控制器（controller）。由 DispatcherServlet 把用户请求转发到 Handler。由Handler 对具体的用户请求进行处理。
**HandlAdapter**：处理器适配器 通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。
**View Resolver**：视图解析器View Resolver 负责将处理结果生成 View 视图，View Resolver 首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。
**View**：视图SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是 jsp。

<mvc:annotation-driven >说明//注意：一般开发中，我们都需要写上此标签
在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 SpringMVC 的三大组件。
使 用<mvc:annotation-driven > 自动加载 RequestMappingHandlerMapping （处理映射器） 和RequestMappingHandlerAdapter （ 处 理 适 配 器 ）
可 用 在 SpringMVC.xml 配 置 文 件 中 使 用<mvc:annotation-driven >替代注解处理器和适配器的配置。
它就相当于在 xml 中配置了：
```xml
<!-- 上面的标签相当于 如下配置-->
<!-- Begin -->
<!-- HandlerMapping --> 
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"></bean> 
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
<!-- HandlerAdapter --> 
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"></bean> 
<beanclass="org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter"></bean> 
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>
<!-- HadnlerExceptionResolvers --> 
<bean class="org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver"></bean> 
<bean class="org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver"></bean>
<bean class="org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver"></bean>
<!-- End -->
```
@RequestMapping:用于建立请求 URL 和处理请求方法之间的对应关系。
```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping { }
```
类上：请求 URL 的第一级访问目录。如果不写，就相当于应用的根目录。写的话需要以/开头。它出现的目的是为了使我们的 URL 可以按照模块化管理:
方法上：请求 URL 的第二级访问目录

    参数：
    value： //用于指定请求的 URL。它和 path 属性的作用是一样的。
    method：//用于指定请求的方式。
    params：//用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的 key 和 value 必须和配置的一模一样。

    params = {"accountName"}，表示请求参数必须有 accountName
    params = {"moeny!100"}，表示请求参数中 money 不能是 100。
    headers：用于指定限制请求消息头的条件
    
jsp 中的代码：
```
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd"> <html><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> <title>requestmapping 的使用</title>
</head> 
<body>
    <!-- 第一种访问方式 --> <a href="${pageContext.request.contextPath}/account/findAccount">
    查询账户</a> <br/>
    <!-- 第二种访问方式 --> <a href="account/findAccount">查询账户</a>
</body>
</html>
```
注意：   当我们使用此种方式配置时，在 jsp 中第二种写法时，不要在访问 URL 前面加/，否则无法找到资源。
```java
@RequestMapping(value="/saveAccount",method=RequestMethod.POST,params= {"username=aa"})
public String saveAccount() {
    System.out.println("保存了账户");
    return "success"; //这里返回的是一个字符串
}
```

请求参数的绑定
1. 请求参数的绑定说明
    1. 表单提交的数据都是k=v格式的 username=haha&password=123
    2. SpringMVC的参数绑定过程是把表单提交的请求参数，作为控制器中方法的参数进行绑定的
    3. 要求：提交表单的name和参数的名称是相同的
2. 支持的数据类型
    1. 基本数据类型和字符串类型
    2. 实体类型（JavaBean） 
    3. 集合数据类型（List、map集合等）
2. 基本数据类型和字符串类型
    1. 提交表单的name和参数的名称是相同的
    2. 区分大小写
3. 实体类型（JavaBean） 
    1. 提交表单的name和JavaBean中的属性名称需要一致（set方法）
    2. 如果一个JavaBean类中包含其他的引用类型，那么表单的name属性需要编写成：对象.属性 例如：
    address.name
4. 给集合属性数据封装
    list[0].属性
5. 在控制器中使用原生的ServletAPI对象只需要在控制器的方法参数定义HttpServletRequest和HttpServletResponse对象
```java
@RequestMapping("/test")
void test(HttpServletRequest req,HttpServleetResponse rep){
    req.getSession();
}
```
6. 请求参数中文乱码的解决1. 在web.xml中配置Spring提供的过滤器类
```xml
<!-- 配置过滤器，解决中文乱码的问题 --> 
<filter>
    <filter-name>characterEncodingFilter</filter-name> 
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter- class> 
    <!-- 指定字符集 --> 
    <init-param> 
        <param-name>encoding</param-name>
         <param-value>UTF-8</param-value> 
     </init-param> 
</filter>
<filter-mapping> 
      <filter-name>characterEncodingFilter</filter-name> 
     <url-pattern>/*</url-pattern>
</filter-mapping>
```
7.自定义类型转换器
1. 表单提交的任何数据类型全部都是字符串类型，但是后台定义Integer类型，数据也可以封装上，说明
Spring框架内部会默认进行数据类型转换。
2. 如果想自定义数据类型转换，可以实现Converter的接口

```java
/*** 把字符串转换成日期的转换器  */ 
public class StringToDateConverter implements Converter<String, Date>{ 
    /*进行类型转换的方法 */ 
    public Date convert(String source) { 
    // 判断 
        if(source == null) { 
            throw new RuntimeException("参数不能为空"); 
        }
        try {
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        // 解析字符串 Date date = df.parse(source); return date; 
        } catch (Exception e) { 
            throw new RuntimeException("类型转换错误"); 
        } 
    } 
}
```
 2. 注册自定义类型转换器，在springmvc.xml配置文件中编写配置
<!-- 注册自定义类型转换器 --> 
```xml
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
     <property name="converters">
         <set>
            <bean class="cn.espoir.utils.StringToDateConverter"/>
         </set> 
     </property> 
 </bean>
 <!-- 开启Spring对MVC注解的支持 --> 
<mvc:annotation-driven conversion-service="conversionService"/>
```
   
### 三、常见注解
```java
/*    @RequestParam :把请求中的指定名称的参数传递给控制器中的形参赋值
          value：请求参数中的名称
          required：请求参数中是否必须提供此参数，默认值是true，必须提供    */
@RequestMapping(path="/hello")
//此时前段传过来的值必须是username
public String sayHello(@RequestParam(value="username",required=false)String name) {  
    return "success"; 
}
     
 ```

 ```java
    /*   @RequestBody :用于获取请求体的内容（注意：get方法不可以）
             required：是否必须有请求体，默认值是true*/ 
    @RequestMapping(path="/hello") 
    public String sayHello(@RequestBody String body) {
        return "success"; 
    }
```

```java
/*   @PathVariable :拥有绑定url中的占位符的。例如：url中有/delete/{id}，{id}就是占位符
           value：指定url中的占位符名称
           Restful风格的URL
           1. 请求路径一样，可以根据不同的请求方式去执行后台的不同方法
           (get,post,put,delete)*/
@RequestMapping(path="/hello/{id}") 
public String sayHello(@PathVariable(value="id") String id) { 
    System.out.println(id); 
    return "success"; }
   
```

```java
/*  @RequestHeader: 获取请求头某个参数的值
       value：请求头的名称*/
@RequestMapping(path="/hello") 
public String sayHello(@RequestHeader(value="Accept") String header) { 
    System.out.println(header); 
    return "success"; 
}
```

```java
/*    @CookieValue: 用于获取cookie中某个参数的值
           value：cookie的名称*/
@RequestMapping(path="/hello") 
public String sayHello(@CookieValue(value="JSESSIONID") String cookieValue) { 
System.out.println(cookieValue); 
return "success"; 
}
```

```java
/*  @ModelAttribute: 表示当前方法会在控制器的方法执行之前，先执行。
   我们在编辑一个用户时，用户有一个创建信息字段，该字段的值是不允许被修改的。
   在提交表单数据是肯定没有此字段的内容，一旦更新会把该字段内容置为 null，此时就可以使用此注解解决问题*/
@ModelAttribute
private User findUserByName(String username) {
    //模拟去数据库查询
    User user = findUserByName(username);
    return user; }
```

没有返回值的时候。在user存入map
```java
@ModelAttribute
public void showModel(String username,Map<String,User> map) {
    //模拟去数据库查询
    User user = findUserByName(username);
    System.out.println("执行了 showModel 方法"+user);
    map.put("u",user);
}
```
在方法的参数前面添加@ModelAttribute("abc")可以取出map中存放的值
```java
@RequestMapping("/updateUser")
public String testModelAttribute(@ModelAttribute("u")User user) {
    System.out.println("控制器中处理请求的方法：修改用户："+user);
    return "success"; }
```

```java
@RequestMapping("/springmvc")
@SessionAttributes(value ={"username","password"},types={Integer.class}) 
public class SessionAttributeController {
/**
* 把数据存入 SessionAttribute
* Model 是 spring 提供的一个接口，该接口有一个实现类 ExtendedModelMap
* 该类继承了 ModelMap，而 ModelMap 就是 LinkedHashMap 子类
*/
    @RequestMapping("/testPut") 
    public String testPut(Model model){ 
        model.addAttribute("username", "aa"); //model是把数据存入request域中
        model.addAttribute("password","123456");
        //跳转之前将数据保存到 username、password 因为注解@SessionAttribute 中有这几个参数
        return "success"; 
    } 
    @RequestMapping("/testGet") 
    public String testGet(ModelMap model){ 
        System.out.println(model.get("username")+";"+model.get("password"); 
        return "success"; 
    } 
    @RequestMapping("/testClean") 
    public String complete(SessionStatus sessionStatus){ 
        sessionStatus.setComplete(); 
        return "success"; 
    } 
}
```



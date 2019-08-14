---
title: springMVC 第二篇
date: 2019-08-10
tags:
- springMVC
categories:
- springMVC
---
1. springMVC 之多种返回值类型
2. springMVC文件上传
3. 异常管理
4. SpringMVC 中的拦截器
***
<!-- more -->
#### springMVC 之多种返回值类型
pom.xml
```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
  <!--版本锁定-->
  <spring.version>5.0.2.RELEASE</spring.version>
</properties>

<dependencies>
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
  </dependency>

  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
  </dependency>

  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
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
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.8</version>
  </dependency>

  <!--jackson json格式数据转换依赖-->
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.9</version>
  </dependency>
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.9</version>
  </dependency>
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.0</version>
  </dependency>
  <!--end -->

  <!--热部署配置-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
  </dependency>

</dependencies>
```
springMVC.xml
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
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--静态资源文件不能拦截-->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/image/**" location="/image/"/>

    <!--开启SpringMvc注解的支持-->
    <mvc:annotation-driven/>

</beans>
```
User
```java
@Data
public class User {
    private String username;
    private Integer age;
}
```
web.xml
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
        <!--<servlet-name>dispatcherServlet</servlet-name>&lt;!&ndash;此处的name任意&ndash;&gt;
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
-->
        <init-param><!--配置全局初始化参数 ，让spring去加载类路径下的 springMVC.xml配置文件-->
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>

        <!--配置服务启动就加载Servlet类-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern><!-- / 表示任何请求都会经过这个servlet-->
    </servlet-mapping>

</web-app>
```
//需要在webapp目录下创建 js/css/images 等文件夹，存放需要的jar包，这里引入了jq的jar包，
index.jsp
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="js/jquery.min.js"></script>
    <script>
        $(function () {
            $("#btn").click(function () {
                $.ajax({
                    url:"user/testAjax",
                    contentType:"application/json;charset=utf-8",   //传过去的数据类型
                    data:'{"username":"李白","age":22}',
                    dataType:"json",     //返回的数据类型
                    type:"post",         //请求类型
                    success:function (data) {
                        alert(data);
                    }
                })
            })
        })
    </script>
</head>
<body>
<a href="user/testString"> testString </a>
<a href="user/testVoid">  testVoid </a>
<a href="user/testModelAndView"> testModelAndView </a>
<a href="user/testForwardOrRedirect"> testForwardOrRedirect </a>
<button id="btn" value="提交"> Ajax异步提交 </button>

</body>
</html>

```
webapp->pages下面创建success.jsp文件
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        ${user.username}
    </body>
</html>
```
controller
```java
//控制器
@Controller
@RequestMapping("/user")
public class HelloController {

    //返回值是字符串 底层也是ModelAndView
    @RequestMapping(path = "/testString")
    public String hello(Model model){
        User user = new User();
        user.setAge(18);
        user.setUsername("李白");
        //添加到request域中
        model.addAttribute("user",user);
        return "success";
        /* <!--  此处虽然返回的字符串，但在SpringMVC.xml中做了配置，对应到该 /WEB-INF/pages/下面的文件-->*/
    }

    //无返回值的时候，默认请求 解析后的testVoid.jsp
    @RequestMapping(path = "/testVoid")
    public void testVoid(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //使用request转发器,就不会去调用前端解析器，需要自己写全路径
        //req.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(req,resp);

        //使用重定向，但重定向不能直接访问，WEB-INF目录下的文件
        //resp.sendRedirect(req.getContextPath()+"/index.jsp");

        //直接响应
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        resp.getWriter().write("你好");
        return;
    }

    //返回值ModelAndView SpringMVC框架提供的对象
    @RequestMapping(path = "/testModelAndView")
    public ModelAndView hello(){
        User user = new User();
        user.setAge(18);
        user.setUsername("李白");
        ModelAndView mv = new ModelAndView();
        mv.addObject("user",user);
        mv.setViewName("success");  //设置返回的ViewName
        return mv;
    }

    //使用关键字进行转发
    @RequestMapping(path = "/testForwardOrRedirect")
    public String testForwardOrRedirect(){
        return "forward:/WEB-INF/pages/success.jsp";
        //重定向，使用要加项目名称，但是springMVC底层已经帮你加上了，不用你自己在添加
        //return "redirect:/index.jsp";
    }

    //测试ajax传递json数据
    @RequestMapping(path = "/testAjax")
    @ResponseBody //将返回的数据封装成json格式
    public User testAjax(@RequestBody User user){  //@RequestBody根据传入的数据将数据自动封装为对象
        System.out.println(user);
        return user;
    }
}
```

#### springMVC文件上传
pom.xml
```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <!--版本锁定-->
    <spring.version>5.0.2.RELEASE</spring.version>
</properties>


<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
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
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.8</version>
    </dependency>

    <!--jackson json格式数据转换依赖-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.9</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.9.9</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-annotations</artifactId>
        <version>2.9.0</version>
    </dependency>
    <!--end -->

    <!--热部署配置-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>

    <!--传统方式上传文件依赖-->
    <dependency>
        <groupId>commons-fileupload</groupId>
        <artifactId>commons-fileupload</artifactId>
        <version>1.4</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
    <!---->

    <!--跨服务器上传依赖-->
    <dependency>
        <groupId>com.sun.jersey</groupId>
        <artifactId>jersey-client</artifactId>
        <version>1.19.1</version>
    </dependency>
    <dependency>
        <groupId>com.sun.jersey</groupId>
        <artifactId>jersey-core</artifactId>
        <version>1.19.1</version>
    </dependency>
    <!---->

</dependencies>
```
web.xml
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

        <!--配置服务启动就加载Servlet类-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern><!-- / 表示任何请求都会经过这个servlet-->
    </servlet-mapping>

</web-app>
```
index.jsp
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body><%--enctype="multipart/form-data 表示将form表单的数据分成多个部分，后台方便获取数据--%>
        <form action="/upload1" method="post" enctype="multipart/form-data">
            选择文件:<input type="file" name="upload">         <br>
            <input type="submit" value="传统方式上传文件"/>
        </form>
        <form action="/upload2" method="post" enctype="multipart/form-data">
            选择文件:<input type="file" name="upload">        <br>
            <input type="submit" value="SpringMVC上传文件"/>
        </form><form action="/upload3" method="post" enctype="multipart/form-data">
            选择文件:<input type="file" name="upload">          <br>
            <input type="submit" value="跨服务器上传文件"/>
        </form>
    </body>
</html>
```
springMVC.xml
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
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--静态资源文件不能拦截-->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/image/**" location="/image/"/>

    <!--配置上传文件-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="maxUploadSize" value="1000000"/><!--配置上传文件大小限制-->

    </bean>

    <!--开启SpringMvc注解的支持-->
    <mvc:annotation-driven/>


</beans>
```

controller
```java
@Controller
public class FileUploadController {

    //跨服务器进行文件上传
    @RequestMapping("/upload3")
    public String upload(MultipartFile upload) throws IOException { //upload的名字必须和前端页面中表单中file的name相同
        //定义上传的服务器路径
        String path="http://localhost:8888/uploads/";
        //获取文件名
        String filename = upload.getOriginalFilename();
        filename=new SimpleDateFormat("yyyyMMddHHmmssSSS").format(new Date())+filename;

        //创建客户端对象
        Client client = Client.create();
        //和图片服务器进行连接
        WebResource resource = client.resource(path + filename);
        //上传文件
        resource.put(upload.getBytes());

        return "success";
    }

    //SpringMVC文件上传，将文件存储在request域中，前端控制器将request发送给配置文件解析器，解析出upload的文件
    //然后发送给controller
    @RequestMapping("/upload2")
    public String upload(HttpServletRequest req,MultipartFile upload) throws IOException { //upload的名字必须和前端页面中表单中file的name相同
        //使用fileupload组件完成文件上传
        //上传的位置,realPath就是项目部署的地方，也就是tomcat的webapp下面
        //apache-tomcat-8.5.31/webapps/ROOT/uploads
        String realPath = req.getSession().getServletContext().getRealPath("/uploads/");
        System.out.println(realPath+req.getSession().getServletContext());
        File file = new File(realPath);
        if (!file.exists()){
            //创建该文件夹
            file.mkdirs();
        }

        //获取文件名
        String filename = upload.getOriginalFilename();
        filename=new SimpleDateFormat("yyyyMMddHHmmssSSS").format(new Date())+filename;
        upload.transferTo(new File(realPath,filename));
        return "success";
    }

    //传统方式上传文件
    @RequestMapping("/upload1")
    public String upLoad(HttpServletRequest req) throws Exception {
        //使用fileupload组件完成文件上传
        //上传的位置,realPath就是项目部署的地方，也就是tomcat的webapp下面
        //apache-tomcat-8.5.31/webapps/ROOT/uploads
        String realPath = req.getSession().getServletContext().getRealPath("/uploads/");
        System.out.println(realPath+req.getSession().getServletContext());
        File file = new File(realPath);
        if (!file.exists()){
            //创建该文件夹
            file.mkdirs();
        }

        //解析request，获取上传的文件项
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        //解析request
        List<FileItem> fileItems = upload.parseRequest(req);
        for (FileItem item : fileItems) {
            //判断当前item对象是否是文件对象
            if (item.isFormField()){
                //说明是普通表单
            }else {
                //说明是文件项
                //获取上传文件的名称
                String fileName = item.getName();
                //String uuid = UUID.randomUUID().toString().replace("-", "");
                fileName=fileName+new SimpleDateFormat("yyyyMMddHHmmssSSS").format(new Date())+fileName;
                System.out.println(realPath+fileName);
                //完成文件的上传
                item.write(new File(realPath,fileName));
                //删除临时文件
                item.delete();
            }
        }
        return "success";
    }
}
```

#### 异常管理
controller
```java
//controller中出现异常后，抛出自定义异常
@Controller
public class TestExceptionController {
    @RequestMapping("/testE")
    public String testException() throws Exception {
        try {
            int i=1/0;
        } catch (Exception e) {
            e.printStackTrace();
            throw new MyException("查询过程出现错误");
        }
        return "success";
    }
}

```
异常实现类:
```java
//在这里实现了异常捕获接口，当抛出自定义异常的时候，就会被该类方法捕获，执行该类方法
public class ExceptionHandler implements HandlerExceptionResolver{
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception ex) {
        //获取异常对象
        MyException e=null;
        if (ex instanceof MyException){
            e=(MyException)ex;
        }else{
            e=new MyException("系统正在维护");
        }

        //创建ModelAndView对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg",e.getMessage());
        mv.setViewName("error");

        return mv;
    }
}
```
自定义异常信息类：
```java
@Data
public class MyException extends Exception {
    private String message;

    public MyException(String message){
        this.message = message;
    }
}
```
在SpringMVC.xml中配置处理器
```xml
<!--配置异常处理器-->
    <bean id="exceptionHandler" class="com.espoir.exception.ExceptionHandler"/>
```
前端页面:
```
<a href="testE">测试异常</a>
```
错误页面:
```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        ${errorMsg}
    </body>
</html>
```

#### SpringMVC 中的拦截器
* Spring MVC 的处理器拦截器类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行预处理和后处理。
* 过滤器是 servlet 规范中的一部分，任何 java web 工程都可以使用。
* 过滤器在 url-pattern 中配置了/*之后，可以对所有要访问的资源拦截。
* 拦截器是 SpringMVC 框架自己的，只有使用了 SpringMVC 框架的工程才能用。
* 拦截器它是只会拦截访问的控制器方法，如果访问的是 jsp，html,css,image 或者 js 是不会进行拦截的。
* 它也是 AOP 思想的具体应用。我们要想自定义拦截器， 要求必须实现：HandlerInterceptor 接口。

实现类：现在访问所有方法都会先执行拦截器
```java
public class MyIntercepter implements HandlerInterceptor {

    //预处理，controller中的方法执行前执行，判断用户是否登录
    //return true 执行下一个拦截器，如果没有则执行controller中的方法
    //return false 不放行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器");
        //可以直接跳转错误页面
        //request.getRequestDispatcher("/WEB-INF/pages/error").forward(request,response);
        return true;
    }

    //controller执行后，success.jsp执行之前执行的方法
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("后处理方法执行");
        //request.getRequestDispatcher("/WEB-INF/pages/error").forward(request,response);

    }

    //success.jsp执行后，才会执行的方法，用来释放资源
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("最后");
    }
}

```
springmvc.xml
```xml
<!--配置拦截器-->
    <!--配置拦截器 1-->
    <mvc:interceptors>
        <!--配置拦截器 -->
        <mvc:interceptor>
            <!--配置要拦截的方法 /** 表示所有请求方法都拦截-->
            <mvc:mapping path="/**" />
            <!--配置不要拦截的方法-->
            <!--<mvc:exclude-mapping path=""/>-->

            <!--配置自定义的拦截器-->
            <bean class="com.espoir.intercepter.MyIntercepter"/>
        </mvc:interceptor>
    </mvc:interceptors>
    <!--配置拦截器 2-->
    <mvc:interceptors>
        <!--配置拦截器-->
        <mvc:interceptor>
            <!--配置要拦截的方法 /** 表示所有请求方法都拦截-->
            <mvc:mapping path="/**" />
            <!--配置不要拦截的方法-->
            <!--<mvc:exclude-mapping path=""/>-->

            <!--配置自定义的拦截器-->
            <bean class="com.espoir.intercepter.MyIntercepter"/>
        </mvc:interceptor>
    </mvc:interceptors>

```

执行顺序 ：拦截器1执行->2 执行->controller执行->后2执行-》后1执行-> success.jsp执行->最后2执行，-》最后1执行
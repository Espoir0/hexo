---
title: travel_project
date: 2019-07-21
tags:
- project servlet
categories:
- project
---
***
<!-- more -->
#### 环境搭建
1.创建空项目
![mark](http://simon.bzk.ink/blog/20190815/hvjlF0EFUP4m.png?imageslim)
2.导入maven项目，(找到项目，选中pom文件即可)
![mark](http://simon.bzk.ink/blog/20190815/Cl83CusBNdmt.png?imageslim)
3.启动项目
方式一:
![mark](http://simon.bzk.ink/blog/20190815/Vg4xSDI7oz5r.png?imageslim)
方式二:
![mark](http://simon.bzk.ink/blog/20190815/hBKrgbKhHncL.png?imageslim)
![mark](http://simon.bzk.ink/blog/20190815/Ig17F2PoKbPx.png?imageslim)
![mark](http://simon.bzk.ink/blog/20190815/rjr5v5abqDfO.png?imageslim)
#### 技术栈
1.Web层

    Servlet：前端控制器
    html：视图    //普通项目都是html  访问速度较快		
    Filter：过滤器
    BeanUtils：数据封装
    Jackson：json序列化工具
2.Service层

    Javamail：java发送邮件工具
    Redis：nosql内存数据库
    Jedis：java的redis客户端
3.Dao层

    Mysql：数据库
    Druid：数据库连接池
    JdbcTemplate：jdbc的工具
#### 部分代码
pom.xml文件
```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  
  <groupId>cn.itcast</groupId>
  <artifactId>travel</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
    <dependencies>
       //junit测试
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!--servlet-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
            <scope>compile</scope>
        </dependency>
        <!--druid连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.9</version>
        </dependency>
        
        <!--jdbcTemplate-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.1.2.RELEASE</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.1.2.RELEASE</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.1.2.RELEASE</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.1.2.RELEASE</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.1.1</version>
            <scope>compile</scope>
        </dependency>
        
        <!--beanUtils-->
        <dependency>
            <groupId>commons-beanutils</groupId>
            <artifactId>commons-beanutils</artifactId>
            <version>1.9.2</version>
            <scope>compile</scope>
        </dependency>
        
        <!--jackson-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.3.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.3.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.3.3</version>
        </dependency>


        <!--javaMail-->
        <dependency>
            <groupId>javax.mail</groupId>
            <artifactId>javax.mail-api</artifactId>
            <version>1.5.6</version>
        </dependency>
        <dependency>
            <groupId>com.sun.mail</groupId>
            <artifactId>javax.mail</artifactId>
            <version>1.5.3</version>
        </dependency>
        <!--jedis-->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.7.0</version>
        </dependency>

    </dependencies>


    <build>
        <!--maven插件-->
        <plugins>
            <!--jdk编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>utf-8</encoding>
                </configuration>
            </plugin>
            <!--tomcat插件-->
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <!-- tomcat7的插件， 不同tomcat版本这个也不一样 -->
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.1</version>
                <configuration>
                    <!-- 通过maven tomcat7:run运行项目时，访问项目的端口号 -->
                    <port>80</port>
                    <!-- 项目访问路径  本例：localhost:9090,  如果配置的aa， 则访问路径为localhost:9090/aa-->
                    <path>/travel</path>
                </configuration>
            </plugin>

        </plugins>
    </build>
</project>
```
sql
```sql
create travel;
use travel;

/*==============================================================*/
/* DBMS name:      MySQL 5.0                                    */
/* Created on:     2018/2/14 23:10:29                           */
/*==============================================================*/
SET NAMES utf8;

DROP TABLE IF EXISTS tab_favorite;
DROP TABLE IF EXISTS tab_route_img;
DROP TABLE IF EXISTS tab_route;
DROP TABLE IF EXISTS tab_category;
DROP TABLE IF EXISTS tab_seller;
DROP TABLE IF EXISTS tab_user;

/*==============================================================*/
/* Table: tab_category                                          */
/*==============================================================*/
CREATE TABLE tab_category
(
   cid                  INT NOT NULL AUTO_INCREMENT,
   cname                VARCHAR(100) NOT NULL,
   PRIMARY KEY (cid),
   UNIQUE KEY AK_nq_categoryname (cname)
);

/*==============================================================*/
/* Table: tab_favorite                                          */
/*==============================================================*/
CREATE TABLE tab_favorite
(
   rid                  INT NOT NULL,
   DATE                 DATE NOT NULL,
   uid                  INT NOT NULL,
   PRIMARY KEY (rid, uid)
);

/*==============================================================*/
/* Table: tab_route                                             */
/*==============================================================*/
CREATE TABLE tab_route
(
   rid                  INT NOT NULL AUTO_INCREMENT,
   rname                VARCHAR(500) NOT NULL,
   price                DOUBLE NOT NULL,
   routeIntroduce       VARCHAR(1000),
   rflag                CHAR(1) NOT NULL,
   rdate                VARCHAR(19),
   isThemeTour          CHAR(1) NOT NULL,
   COUNT                INT DEFAULT 0,
   cid                  INT NOT NULL,
   rimage               VARCHAR(200),
   sid                  INT,
   sourceId             VARCHAR(50),
   PRIMARY KEY (rid),
   UNIQUE KEY AK_nq_sourceId (sourceId)
);

/*==============================================================*/
/* Table: tab_route_img                                         */
/*==============================================================*/
CREATE TABLE tab_route_img
(
   rgid                 INT NOT NULL AUTO_INCREMENT,
   rid                  INT NOT NULL,
   bigPic               VARCHAR(200) NOT NULL,
   smallPic             VARCHAR(200),
   PRIMARY KEY (rgid)
);

/*==============================================================*/
/* Table: tab_seller                                            */
/*==============================================================*/
CREATE TABLE tab_seller
(
   sid                  INT NOT NULL AUTO_INCREMENT,
   sname                VARCHAR(200) NOT NULL,
   consphone            VARCHAR(20) NOT NULL,
   address              VARCHAR(200),
   PRIMARY KEY (sid),
   UNIQUE KEY AK_Key_2 (sname)
);

/*==============================================================*/
/* Table: tab_user                                              */
/*==============================================================*/
CREATE TABLE tab_user
(
   uid                  INT NOT NULL AUTO_INCREMENT,
   username             VARCHAR(100) NOT NULL,
   PASSWORD             VARCHAR(32) NOT NULL,
   NAME                 VARCHAR(100),
   birthday             DATE,
   sex                  CHAR(1),
   telephone            VARCHAR(11),
   email                VARCHAR(100),
   STATUS               CHAR(1) ,
   CODE					VARCHAR(50),
   
   PRIMARY KEY (uid),
   UNIQUE KEY AK_nq_username (username),
   UNIQUE KEY AK_nq_code (CODE)
);

ALTER TABLE tab_favorite ADD CONSTRAINT FK_route_favorite FOREIGN KEY (rid)
      REFERENCES tab_route (rid) ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE tab_favorite ADD CONSTRAINT FK_user_favorite FOREIGN KEY (uid)
      REFERENCES tab_user (uid) ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE tab_route ADD CONSTRAINT FK_category_route FOREIGN KEY (cid)
      REFERENCES tab_category (cid) ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE tab_route ADD CONSTRAINT FK_seller_route FOREIGN KEY (sid)
      REFERENCES tab_seller (sid) ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE tab_route_img ADD CONSTRAINT FK_route_routeimg FOREIGN KEY (rid)
      REFERENCES tab_route (rid) ON DELETE RESTRICT ON UPDATE RESTRICT;
INSERT  INTO `tab_category`(`cid`,`cname`) VALUES (8,'全球自由行')....
INSERT  INTO `tab_seller`(`sid`,`sname`,`consphone`,`address`) VALUES (1,'黑马程序员'...
/*Data for the table `tab_route` */
INSERT  INTO `tab_route`(`rid`,`rname`,`price`,...
/*Data for the table `tab_route_img` */
INSERT  INTO `tab_route_img`(`rgid`,`rid`,`bigPic`,`smallPic`) VALUES...

```
项目分析:
![mark](http://simon.bzk.ink/blog/20190815/n06kSxQQdJpE.png?imageslim)
1 表单校验：部分代码
```html
//提升用户体验，并减轻服务器压力。使用正则校验输入的数据
//检验email alone@qq.com
function checkEmail() {
   var email=$("#email").val();
   var reg_email=/^\w+@\w+\.\w+$/;
   var flag = reg_email.test(email);
   if(flag){
       $("#email").css("border","");
   }else {
              $("#email").css("border","red 1px solid");
   }
   return flag;
}
//入口函数
$(function () {
     //给输入框添加离焦事件
     $("#email").blur(checkEmail);
     //检测利用function检测用户信息合法后才会执行表单提交,即submit的参数为true时提交
     $("#registerForm").submit(function () {
           return checkEmail();   //表单提交的时候调用所有校验方法
         });
});
```
2 异步(ajax)提交表单
在此使用异步提交表单是为了获取服务器响应的数据。因为我们前台使用的是html作为视图层，不能够直接从servlet相关的域对象获取值，只能通过ajax获取响应数据
```html
//检测利用function检测用户信息合法后才会执行表单提交,即submit的参数为true时提交
$("#registerForm").submit(function () {
    //如果检校通过，利用ajax方式发送数据到服务器
   if(checkUsername() && checkPassword() && checkEmail() && checkName() &&checkTelephone()&&checkBirthday() && checkCheck()){
       //$(this).serialize() 作用： 将表单数据序列化为字符串
       $.post("user/regist",$(this).serialize(),function (data) {
           if(data.flag){
               //跳转成功页面
            location.href="register_ok.html";
         }else {
               //给出错误信息
            $("#err_msg").html(data.errorMsg);
         }
                 });
   }
   //d2.跳转页面

   return false;
   //return checkUsername() && checkPassword() && checkEmail() && checkName() &&checkTelephone()&&checkBirthday() && checkCheck();
         });
```
3.后台代码,三层架构
UserDao
```java
public class UserDaoImpl implements UserDao {
    private JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils.getDataSource());
    //注册用户
    @Override
    public void addUser(User user) {
        String sql="insert into tab_user (username,password,name,birthday,sex,telephone,email,status,code)values(?,?,?,?,?,?,?,?,?)";

        jdbcTemplate.update(sql,user.getUsername(),
                user.getPassword(),
                user.getName(),
                user.getBirthday(),
                user.getSex(),
                user.getTelephone(),
                user.getEmail(),
                user.getStatus(),
                user.getCode());
    }

    //注册的时候判断用户是否已经存在
    @Override
    public User findByUsername(String username) {
        //jdbcTemplate如果没转化成功会发生异常，所以要try catch
        User user =null;
        try {
            String sql="select * from tab_user where username=?";
            user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), username);

        } catch (DataAccessException e) {
        }
        return user;
    }

    //用户激活后设置用户的激活状态
    @Override
    public void updateStatus(User user) {
        String sql="update tab_user set status='Y' where uid=?";
        jdbcTemplate.update(sql,user.getUid());
    }

    //用户激活的时候，根据用户的激活码查询用户是否存在
    @Override
    public User findByCode(String code) {
        User user = null;
        try {
            String sql="select * from tab_user where code=?";
            user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), code);
        } catch (DataAccessException e) {
            e.printStackTrace();
        }
        return user;
    }

    //登录的时候根据用户名和密码查看用户是否存在
    @Override
    public User findByNameAndPassword(String username, String password) {
        System.out.println(username+password);
        User user = null;
        try {
            String sql="select * from tab_user where username=? and password=?";
            user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), username, password);
        } catch (DataAccessException e) {
            e.printStackTrace();
        }
        return user;
    }
}
```
Service
```java
public class UserServiceImpl implements UserService {
    private UserDao userDao=new UserDaoImpl();

    //注册用户
    @Override
    public boolean add(User user) {
        User u = userDao.findByUsername(user.getUsername());
        if (u==null){

            //保存用户信息
            //1.设置激活码，唯一字符串
            user.setCode(UuidUtil.getUuid());
            //2.设置激活码状态
            user.setStatus("N");
            userDao.addUser(user);

            //3.激活邮件发送，邮件正文
            String content="<a href='http://localhost/travel/user/active?code="+user.getCode()+"'>点击激活</a>";
            MailUtils.sendMail(user.getEmail(),content,"激活");

            return true;
        }else {
            return false;
        }
    }

    //用户登录邮箱激活
    @Override
    public boolean active(String code) {
        //根据激活码查询用户对象
        User user=userDao.findByCode(code);
        if(user!=null){
            //2.调用userDao 修改激活状态
            userDao.updateStatus(user);
            return true;
        }
        return false;
    }

    //登录时通过username,password判断用户是否存在
    @Override
    public User findByNameAndPassword(User user) {
        User u=userDao.findByNameAndPassword(user.getUsername(),user.getPassword());
        return u;
    }
}
```
/registUserServlet
```java
@WebServlet("/registUserServlet")
public class RegistUserServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //先判断验证码
        String check = request.getParameter("check");
        HttpSession session = request.getSession();
        String checkcode_server = (String)session.getAttribute("CHECKCODE_SERVER");
        //取出验证码之后，在session中清除验证码
        session.removeAttribute("CHECKCODE_SERVER");

        //验证码错误直接返回错误信息
        if (check==null || !check.equalsIgnoreCase(checkcode_server)){
            ResultInfo resultInfo = new ResultInfo();
            resultInfo.setFlag(false);
            resultInfo.setErrorMsg("验证码错误");
            ObjectMapper mapper = new ObjectMapper();
            String json = mapper.writeValueAsString(resultInfo);
            response.setContentType("application/json;charset=utf-8");
            response.getWriter().write(json);
            return;
        }

        //获取数据
        Map<String, String[]> map = request.getParameterMap();
        //封装对象
        User user = new User();
        try {
            //将map中的数据封装为对象
            BeanUtils.populate(user,map);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }

        //调用Service处理，并返回注册是否成功
        UserService userService=new UserServiceImpl();
        boolean flag=userService.add(user);

        //响应结果
        ResultInfo resultInfo = new ResultInfo();
        if (flag){
            //注册成功
            resultInfo.setFlag(true);
        }else {
            //注册失败
            resultInfo.setFlag(false);
            resultInfo.setErrorMsg("注册失败");
        }
        //利用jackson将info对象转化为json数组
        ObjectMapper mapper = new ObjectMapper();
        String json=mapper.writeValueAsString(resultInfo);

        //设置响应类型，并且返回json数据
        response.setContentType("application/json;charset=utf-8");
        response.getWriter().write(json);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```
#### 登录功能
![mark](http://simon.bzk.ink/blog/20190815/GPFz5eB93iFi.png?imageslim)
2.邮箱激活
![mark](http://simon.bzk.ink/blog/20190815/eVvThhkaQlFF.png?imageslim)
![mark](http://simon.bzk.ink/blog/20190815/eeRiONhVOxdP.png?imageslim)
代码:
```java
public final class MailUtils {
    private static final String USER = "450646331@qq.com"; // 发件人称号，同邮箱地址
    private static final String PASSWORD = "ogpnqjdqxprdbgdj"; // 如果是qq邮箱可以使户端授权码，或者登录密码

    /**
     *
     * @param to 收件人邮箱
     * @param text 邮件正文
     * @param title 标题
     */
    /* 发送验证信息的邮件 */
    public static boolean sendMail(String to, String text, String title){
        try {
            final Properties props = new Properties();
            props.put("mail.smtp.auth", "true");
            props.put("mail.smtp.host", "smtp.qq.com");

            // 发件人的账号
            props.put("mail.user", USER);
            //发件人的密码
            props.put("mail.password", PASSWORD);

            // 构建授权信息，用于进行SMTP进行身份验证
            Authenticator authenticator = new Authenticator() {
                @Override
                protected PasswordAuthentication getPasswordAuthentication() {
                    // 用户名、密码
                    String userName = props.getProperty("mail.user");
                    String password = props.getProperty("mail.password");
                    return new PasswordAuthentication(userName, password);
                }
            };
            // 使用环境属性和授权信息，创建邮件会话
            Session mailSession = Session.getInstance(props, authenticator);
            // 创建邮件消息
            MimeMessage message = new MimeMessage(mailSession);
            // 设置发件人
            String username = props.getProperty("mail.user");
            InternetAddress form = new InternetAddress(username);
            message.setFrom(form);

            // 设置收件人
            InternetAddress toAddress = new InternetAddress(to);
            message.setRecipient(Message.RecipientType.TO, toAddress);

            // 设置邮件标题
            message.setSubject(title);

            // 设置邮件的内容体
            message.setContent(text, "text/html;charset=UTF-8");
            // 发送邮件
            Transport.send(message);
            return true;
        }catch (Exception e){
            e.printStackTrace();
        }
        return false;
    }

    public static void main(String[] args) throws Exception { // 做测试用
        MailUtils.sendMail("877873768@qq.com","你好，这是一封测试邮件，无需回复。","测试邮件");
        System.out.println("发送成功");
    }
}
```
1 优化Servlet
1.1 目的
减少Servlet的数量，现在是一个功能一个Servlet，将其优化为一个模块一个Servlet，相当于在数据库中一张表对应一个Servlet，在Servlet中提供不同的方法，完成用户的请求。
![mark](http://simon.bzk.ink/blog/20190815/SDOmbpPQxi4n.png?imageslim)
```java
BaseServlet
public class BaseServlet extends HttpServlet {
    @Override
    public void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //System.out.println("baseServlet方法执行");
        //完成方法的分发
        //1.获取请求路径
        String uri = req.getRequestURI();//travel/user/add
        //2.获取方法的名称
        String methodName = uri.substring(uri.lastIndexOf('/') + 1);
        //3.获取方法对象,service是UserServlet中调用的，谁调用this代表谁
        try {

            Method method = this.getClass().getMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
            System.out.println(methodName);
            //4.执行方法
            //暴力反射//忽略访问权限的修饰符，因为获取的该方法的权限是protect的
            //method.setAccessible(true);
            method.invoke(this,req,resp);
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
    }

    //序列化为json，然后返回给客户端
    public void writeValue(Object obj,HttpServletResponse rep){

        ObjectMapper mapper = new ObjectMapper();
        rep.setContentType("application/json;charset=utf-8");
        try {
            mapper.writeValue(rep.getOutputStream(),obj);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //调用父类方法//将user转化为json字符串
    public String writeValueAsString(Object obj){
        ObjectMapper mapper = new ObjectMapper();
        String json=null;
        try {
            json = mapper.writeValueAsString(obj);
        } catch (JsonProcessingException e) {
        }
        return json;
    }
}


```
一般写代码从后台开始
dao->service:
缓存优化显示页面：Redis
```java
//开启本地Redis服务，然后查询本地redis中是否存在，如果不存在，则查询mysql
public class CategoryServiceImpl implements CategoryService{
    private CategoryDao categoryDao=new CategoryDaoImpl();
    
    @Override
    public List<Category> findAll() {
        //缓存优化显示界面
        //1.1获取jedis客户端
        Jedis jedis = JedisUtil.getJedis();
        //1.2使用sortset排序查询
        List<Category> list = new ArrayList<>();

        Set<String> categorys = jedis.zrange("category", 1, -1);
        //1.3判断查询的集合是否为空
        if(categorys==null || categorys.size()==0){
            System.out.println("mysql");
            //如果为Null,就从数据库里面查询，然后将数据存入redis
            //查询数据库
            list = categoryDao.findAll();
            //将集合数据存入到redis中的，并且按照id排序
            for (Category temp:list){
                jedis.zadd("category",temp.getCid(),temp.getCname());
            }
        }else {
            System.out.println("jedis");
            //如果不为null，把从redis中取出的set集合转化成list,set里面是分类名
            for (String temp:categorys){
                //list中存储Category对象
                Category category = new Category();
                category.setCname(temp);
                list.add(category);
            }
        }
        return list;
    }
}
```
![mark](http://simon.bzk.ink/blog/20190815/Dgm0rCIQMw0Q.png?imageslim)


header.html
```html
//给搜索按钮绑定事件，获取搜索框中的值
$("#search-button").click(function () {
    var rname=$("#search_input").val();
    //使用自定义函数获取参数值
    var cid=getParameter("cid");
    //拼接参数然后跳转到路线页面
    location.href="http://localhost/travel/route_list.html?cid="+cid+"&rname="+rname;
})
```
route_list.html
```html
//利用自己封装的方法获取cid 和rname
var cid=getParameter("cid");  //获取cid
var rname=getParameter("rname");//获取rname的值

if(rname){
    //此时获取的rname是url编码，需要转码
    rname=window.decodeURIComponent(rname);
}
```
修复后台代码 Servlet Service Dao
```java
@Override
public int getTotalCount(int cid,String rname) {
    //String sql="select count(*) from tab_route where cid=?";
    //定义sql模板
    String sql="select count(*) from tab_route where 1 = 1 ";
    StringBuilder sb=new StringBuilder(sql);

    //定义存储参数得list
    List param=new ArrayList();
    if(cid!=0){
        sb.append(" and cid = ? ");
        param.add(cid);
    }
    if(rname!=null && rname.length()>0){
        sb.append(" and rname like ? ");
        param.add("%"+rname+"%"); //模糊查询
    }
    sql=sb.toString();
    System.out.println(sql);
    return jdbcTemplate.queryForObject(sql,Integer.class,param.toArray());   //将查到的结果用的Integer表示，
}
```

```java
public List<Route> queryPage(int cid, int start, int pageSize, String rname) {
    //分页查询，将结果放入List中
    //String sql="select * from tab_route where cid=? limit ?,?";
    //定义sql模板
    String sql="select * from tab_route where 1 = 1 ";
    StringBuilder sb=new StringBuilder(sql);

    //定义存储参数得list
    List param=new ArrayList();
    if(cid!=0){
        sb.append(" and cid= ? ");
        param.add(cid);
    }
    if(rname!=null && rname.length()>0){
        sb.append(" and rname like ? ");
        param.add("%"+rname+"%");//模糊查询
    }
    sb.append(" limit ?,? ");  //分页条件
    sql=sb.toString();

    param.add(start);
    param.add(pageSize);
    System.out.println(param);

    return jdbcTemplate.query(sql,new BeanPropertyRowMapper<>(Route.class),param.toArray());

}
```
前端代码
分页展示
旅游线路的分页展示
查询不同分类的旅游线路sql,一个分类里面有对应多种线路
Select * from tab_route where cid = ?;
//取出category 分类对应的id,
```html
$(function () {
    var search = location.search;
    //alert(search);//?id=5
    // 切割字符串，拿到第二个值
    var cid = search.split("=")[1];
});
```
1，创建PageBean;
2.pageServlet 
   pageService
   pageDao

线路详情页面
![mark](http://simon.bzk.ink/blog/20190815/PYQqGB4zVh9H.png?imageslim)
```html
@Override
public List<RouteImg> findRouteImgByRid(int rid) {
    String sql="select * from tab_route_img where rid=?";
    return jdbcTemplate.query(sql,new BeanPropertyRowMapper<>(RouteImg.class),rid);
}

@Override
public Seller findSellerByRid(int sid) {
    String sql="select * from tab_seller where sid=?";
    return jdbcTemplate.queryForObject(sql,new BeanPropertyRowMapper<>(Seller.class),sid);
}

//根据rid查询对应的路线
@Override
public Route queryDetail(String ridStr) {
    int rid=Integer.parseInt(ridStr);
    //根据rid查询route对象
    Route route = routeDao.queryRoute(rid);

    //根据rid查询图片集合的信息
    List<RouteImg> routeImgList = routeDao.findRouteImgByRid(rid);
    //将集合设置到route对象
    route.setRouteImgList(routeImgList);
    //根据route的sid(商家id)查询商家对象
    Seller seller = routeDao.findSellerByRid(route.getSid());
    //将seller设置到route对象
    route.setSeller(seller);
    return  route;
}
```
```html
var ddStr='<a class="up_img up_img_disable"></a>';
for(var i=0;i<route.routeImgList.length;i++){
    var a;
    //大于四的时候隐藏
    if(i>=4){
        a='<a title="" class="little_img" data-bigpic="'+route.routeImgList[i].bigPic+'" style="display:none;">\n' +
            '                        <img src="'+route.routeImgList[i].smallPic+'">\n' +
            '                    </a>';
        ddStr += a;
    }else {
        a='<a title="" class="little_img" data-bigpic="'+route.routeImgList[i].bigPic+'">\n' +
            '                        <img src="'+route.routeImgList[i].smallPic+'">\n' +
            '                    </a>';
        ddStr += a;
    }


}
ddStr+= ' <a class="down_img down_img_disable" style="margin-bottom: 0;"></a>';

$("#dd").html(ddStr);
goImg();
```
旅游线路收藏功能
![mark](http://simon.bzk.ink/blog/20190815/eAylFwHhBMF9.png?imageslim)
![mark](http://simon.bzk.ink/blog/20190815/6a4z9kpoMJRc.png?imageslim)
```java
$(function () {
    var rid=getParameter("rid");
    $.get("route/isFavorite",{rid:rid},function (flag) {
        if(flag){
            //如果收藏过
            $("#favorite").addClass("already");
            $("#favorite").attr("disabled","disabled");
            //删除按钮的点击事件
            $("#favorite").removeAttr("onclick");
        }else {
            //没收藏
        }
    });
});

function addFavorite() {
    var rid=getParameter("rid");
    $.get("user/findOne",{},function (user) {
        if(user){
            //已登录
            $.get("route/addFavorite",{rid:rid},function () {
                //代码刷新页面，也可以发送ajax去查数据库
                location.reload();
            })
        }else {
            alert("您尚未登录，请登录");
            location.href="http://localhost/travel/login.html";
        }
    });
}
```
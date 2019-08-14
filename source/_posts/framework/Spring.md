---
title: Spring
date: 2019-08-02
tags:
- Spring
categories:
- 框架
---
spring
<!-- more -->
IoC 的概念和作用：削减计算机程序的耦合(解除我们代码中的依赖关系)。
模块间的耦合度是指模块之间的依赖关系，包括控制关系、调用关系、数据传递关系。
比如在jdbc注册驱动的时候，我们是通过反射来注册驱动的,而不是new 对象
```
//1.注册驱动,两种方式，而我们选择通过反射来注册驱动
//DriverManager.registerDriver(new com.mysql.jdbc.Driver());
Class.forName("com.mysql.jdbc.Driver");

//2.获取连接
//3.获取预处理 sql 语句对象
//4.获取结果集
//5.遍历结果集
```
mysql 驱动的全限定类名字符串是在 java 类中写死的，一旦要改还是要修改源码。使用配置文件配置
工厂模式解耦
在实际开发中我们可以把三层的对象都使用配置文件配置起来，当启动服务器应用加载的时候，让一个类中的
方法通过读取配置文件，把这些对象创建出来并存起来。在接下来的使用的时候，直接拿过来用就好了。
那么，这个读取配置文件，创建和获取三层对象的类就是工厂。
在应用加载时，创建一个 Map，用于存放三层对象。
我们把这个 map 称之为容器。

工厂就是负责给我们从容器中获取指定对象的类。这时候我们获取对象的方式发生了改变。
原来：我们在获取对象时，都是采用 new 的方式。是主动的。我们获取对象时，同时跟工厂要，有工厂为我们查找或者创建对象。是被动的。

IOC就是吧创建对象的权利交给框架，包括依赖注入和依赖查找

```xml
<!-- bean 标签：用于配置让 spring 创建对象，并且存入 ioc 容器之中
     id 属性：对象的唯一标识。
     class 属性：指定要创建对象的全限定类名
-->

<beans>
    <!-- 配置 service --> 
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
    </bean>
    <!-- 配置 dao --> 
    <bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl">
    </bean>
</beans>
```

```
public static void main(String[] args) {

    //1.使用 ApplicationContext 接口，就是在获取 spring 容器
    ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
    
    //2.根据 bean 的 id 获取对象
    IAccountService aService = (IAccountService) ac.getBean("accountService");

    IAccountDao aDao = (IAccountDao) ac.getBean("accountDao");
}
```

BeanFactory 才是 Spring 容器中的顶层接口。
ApplicationContext 是它的子接口。

BeanFactory 和 ApplicationContext 的区别：
创建对象的时间点不一样。
ApplicationContext：只要一读取配置文件，默认情况下就会创建对象。
BeanFactory：什么使用什么时候创建对象。

bean 标签
作用：
用于配置对象让 spring 来创建的。
默认情况下它调用的是类中的无参构造函数。如果没有无参构造函数则不能创建成功。
属性：
id：给对象在容器中提供一个唯一标识。用于获取对象。
class：指定类的全限定类名。用于反射创建对象。默认情况下调用无参构造函数。
scope：指定对象的作用范围。
* singleton :默认值，单例的.
* prototype :多例的.
* request :WEB 项目中,Spring 创建一个 Bean 的对象,将对象存入到 request 域中.
* session :WEB 项目中,Spring 创建一个 Bean 的对象,将对象存入到 session 域中.
* global session :WEB 项目中,应用在 Portlet 环境.如果没有 Portlet 环境那么
globalSession 相当于 session.
init-method：指定类中的初始化方法名称。
destroy-method：指定类中销毁方法名称。
3.3.2.2 bean 的作用范围和生命周期
单例对象：scope="singleton"
一个应用只有一个对象的实例。它的作用范围就是整个引用。
生命周期：
对象出生：当应用加载，创建容器时，对象就被创建了。
对象活着：只要容器在，对象一直活着。
对象死亡：当应用卸载，销毁容器时，对象就被销毁了。
多例对象：scope="prototype"
每次访问对象时，都会重新创建对象实例。
生命周期：
对象出生：当使用对象时，创建新的对象实例。
对象活着：只要对象在使用中，就一直活着。
对象死亡：当对象长时间不用时，被 java 的垃圾回收器回收了。
3.3.2.3 实例化 Bean 的三种方式
第一种方式：使用默认无参构造函数
<!--在默认情况下：
它会根据默认无参构造函数来创建类对象。如果 bean 中没有默认无参构造函数，将会创建失败。
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"/>

第二种方式：spring 管理静态工厂-使用静态工厂的方法创建对象
/**
* 模拟一个静态工厂，创建业务层实现类
*/
public class StaticFactory {
public static IAccountService createAccountService(){
return new AccountServiceImpl();
} }
<!-- 此种方式是:
使用 StaticFactory 类中的静态方法 createAccountService 创建对象，并存入 spring 容器
id 属性：指定 bean 的 id，用于从容器中获取
class 属性：指定静态工厂的全限定类名
factory-method 属性：指定生产对象的静态方法
--> <bean id="accountService"
class="com.itheima.factory.StaticFactory"
factory-method="createAccountService"></bean>

第三种方式：spring 管理实例工厂-使用实例工厂的方法创建对象
/**
* 模拟一个实例工厂，创建业务层实现类
* 此工厂创建对象，必须现有工厂实例对象，再调用方法
*/
public class InstanceFactory {
    public IAccountService createAccountService(){
        return new AccountServiceImpl();
        } 
}


<!-- 此种方式是：
先把工厂的创建交给 spring 来管理。然后在使用工厂的 bean 来调用里面的方法
factory-bean 属性：用于指定实例工厂 bean 的 id。
factory-method 属性：用于指定实例工厂中创建对象的方法。
--> 
<bean id="instancFactory" class="com.itheima.factory.InstanceFactory"></bean> <bean id="accountService"
factory-bean="instancFactory"
factory-method="createAccountService"></bean>


spring 的依赖注入

```xml
<!-- 使用构造函数的方式，给 service 中的属性传值
要求：类中需要提供一个对应参数列表的构造函数。
涉及的标签： <constructor-arg>
属性：
    index:指定参数在构造函数参数列表的索引位置
    type:指定参数在构造函数中的数据类型
    name:指定参数在构造函数中的名称 用这个找给谁赋值
    =======上面三个都是找给谁赋值，下面两个指的是赋什么值的==============
    value:它能赋的值是基本数据类型和 String 类型
    ref:它能赋的值是其他 bean 类型，也就是说，必须得是在配置文件中配置过的 bean
--> 
<beans>
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
         <constructor-arg name="name" value="张三"></constructor-arg> 
         <constructor-arg name="age" value="18"></constructor-arg> 
         <constructor-arg name="birthday" ref="now"></constructor-arg>
    </bean> 
    <bean id="now" class="java.util.Date"></bean>
</beans>
```
set 方法注入
顾名思义，就是在类中提供需要注入成员的 set 方法。具体代码如下
```java
public class AccountServiceImpl implements IAccountService {
    private String name;
    private Integer age;
    private Date birthday;
    
    public void setName(String name) {
        this.name = name; }
    public void setAge(Integer age) {
        this.age = age; }
    public void setBirthday(Date birthday) {
        this.birthday = birthday; 
    }
}
        
```
```xml
<!-- 通过配置文件给 bean 中的属性传值：使用 set 方法的方式,这种方式用的较多
涉及的标签：<property>
属性：
    name：找的是类中 set 方法后面的部分,属性（属性不一定是成员变量）
    ref：给属性赋值是其他 bean 类型的
    value：给属性赋值是基本数据类型和 string 类型的
-->
<beans>
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
         <property name="name" value="test"></property> 
         <property name="age" value="21"></property> 
         <property name="birthday" ref="now"></property>
    </bean> 
    <bean id="now" class="java.util.Date"></bean>
</beans> 
```

注入集合属性
顾名思义，就是给类中的集合成员传值，它用的也是set方法注入的方式，只不过变量的数据类型都是集合。
我们这里介绍注入数组，List,Set,Map,Properties。具体代码如下：
/***/
```java
public class AccountServiceImpl implements IAccountService {
    private String[] myStrs;
    private List<String> myList;
    private Set<String> mySet;
    private Map<String,String> myMap;
    private Properties myProps;
    
    public void setMyStrs(String[] myStrs) {
        this.myStrs = myStrs; }
    public void setMyList(List<String> myList) {
        this.myList = myList; }
    public void setMySet(Set<String> mySet) {
        this.mySet = mySet; }
    public void setMyMap(Map<String, String> myMap) {
        this.myMap = myMap; }
    public void setMyProps(Properties myProps) {
        this.myProps = myProps; }
}
```
<!-- 注入集合数据
List 结构的：
    array,list,set
Map 结构的
    map,entry,props,prop
--> 
```xml
<beans>
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
    <!-- 在注入集合数据时，只要结构相同，标签可以互换 -->
    <!-- 给数组注入数据 --> 
    <property name="myStrs"> 
        <set>
            <value>AAA</value> 
            <value>BBB</value> 
            <value>CCC</value>
        </set>
    </property>
    <!-- 注入 list 集合数据 --> 
    <property name="myList">
         <array> 
             <value>AAA</value> 
             <value>BBB</value> 
             <value>CCC</value>
         </array>
    </property>
    <!-- 注入 set 集合数据 --> 
    <property name="mySet"> 
        <list>
            <value>AAA</value>
             <value>BBB</value> 
             <value>CCC</value>
        </list>
    </property>
    <!-- 注入 Map 数据 --> 
    <property name="myMap"> 
        <props> 
            <prop key="testA">aaa</prop> 
            <prop key="testB">bbb</prop>
        </props>
    </property>
    <!-- 注入 properties 数据 -->
    <property name="myProps">
         <map>
             <entry key="testA" value="aaa"></entry> 
             <entry key="testB"> 
                <value>bbb</value>
             </entry>
         </map>
    </property>
    </bean>
</beans> 
```

注解配置
注意：
基于注解整合时，导入约束时需要多导入一个 context 名称空间下的约束。
由于我们使用了注解配置，此时不能在继承 JdbcDaoSupport，需要自己配置一个 JdbcTemplate
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 告知 spring 创建容器时要扫描的包 --> 
    <context:component-scan base-package="com.espoir"></context:component-scan>

<!-- 配置 类 --> 
    <bean id="dbAssit" class="com.espoir.Demo"> 
        <property name="dataSource" ref="dataSource"></property>
    </bean>
<!-- 配置数据源 --> 
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"> 
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property> 
        <property name="jdbcUrl" value="jdbc:mysql:///spring_day02"></property>
         <property name="user" value="root"></property> 
         <property name="password" value="1234"></property>
    </bean>
</beans>
```

常用注解
用于创建对象的 相当于：<bean id="" class="">
@Component  //组件，Bean也有组件的意思
作用：把资源让 spring 来管理。相当于在 xml 中配置一个 bean。
属性：
value：指定 bean 的 id。如果不指定 value 属性，默认 bean 的 id 是当前类的类名。首字母小写。

@Controller @Service @Repository //仓库
他们三个注解都是针对一个的衍生注解，他们的作用及属性都是一模一样的。提供了更加明确的语义化。
@Controller：一般用于表现层的注解。
@Service：一般用于业务层的注解。
@Repository：一般用于持久层的注解。
细节：如果注解中有且只有一个属性要赋值时，且名称是 value，value 在赋值是可以不写。

用于注入数据的
相当于：<property name="" ref=""> <property name="" value="">

 @Autowired
作用：自动按照类型注入。当使用注解注入属性时，set 方法可以省略。它只能注入其他 bean 类型。当有多个
类型匹配时，使用要注入的对象变量名称作为 bean 的 id，在 spring 容器查找，找到了也可以注入成功。找不到
就报错。
 @Qualifier
作用：
在自动按照类型注入的基础之上，再按照 Bean 的 id 注入。它在给字段注入时不能独立使用，必须和
@Autowire 一起使用；但是给方法参数注入时，可以独立使用。
属性： value：指定 bean 的 id。
@Resource
作用：
直接按照 Bean 的 id 注入。它也只能注入其他 bean 类型。
属性：
name：指定 bean 的 id。

@Value
作用 注入基本数据类型和 String 类型数据的
属性：value：用于指定值
2.3.3 用于改变作用范围的：
相当于：<bean id="" class="" scope="">
2.3.3.1 @Scope
作用：
指定 bean 的作用范围。
属性：
value：指定范围的值。
取值：singleton prototype request session globalsession
2.3.4 和生命周期相关的：(了解)
相当于：<bean id="" class="" init-method="" destroy-method="" />

2.3.4.1 @PostConstruct
作用：
用于指定初始化方法。
2.3.4.2 @PreDestroy
作用：
用于指定销毁方法。

注解的优势：
配置简单，维护方便（我们找到类，就相当于找到了对应的配置）。
XML 的优势：
修改时，不用改源码。不涉及重新编译和部署。
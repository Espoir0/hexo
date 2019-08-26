---
title: spring-cloud
date: 2019-08-02
tags:
- spring cloud
categories:
- spring cloud
---
spring cloud入门
<!-- more -->
[点我查看，博主学习时的参考文章](https://blog.csdn.net/forezp/article/details/81040925)
### 微服务介绍：微服务是一种架构风格，定义:
    
    1. 一个应用由一系列微小服务共同组成，通过http的方式进行互通
    2. 每个业务独立开发
    3. 独立部署
    4. 分布式的管理
    5. 跑在自己的进程里面

### spring cloud简介:
spring cloud 为开发人员提供了快速构建分布式系统的一些工具，包括配置管理、服务发现、断路器、路由
 、微代理、事件总线、全局锁、决策竞选、分布式会话等等。它运行环境简单，可以在开发人员的电脑上跑。spring cloud是基于springboot的，

### springcloud之服务的注册与发现Eureka
(Finchley版本,spring cloud版本是以伦敦地铁站命名的，并按字母顺序递增), 用Eureka创建Server和Client，
eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下erureka server也是一个eureka client ,必须要指定一个 server
#### 2.首先创建一个父级maven工程

首先创建一个主Maven工程,在其pom文件引入依赖，spring Boot版本为2.0.3.RELEASE，Spring Cloud版本为Finchley.RELEASE。这个pom文件作为父pom文件，起到依赖版本控制的作用，其他module工程继承该pom

pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>alone.aa</groupId>
    <artifactId>spring_cloud_learn</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
        <relativePath/>
    </parent>



    <!--spring-cloud版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencies>

        <!--热部署配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

        <!--单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


    </dependencies>

    <!--依赖管理-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version><!--properties中的 cloud版本-->
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

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

#### 3.创建子module，使用spring initialir 创建，选择cloud discovery->eureka server
创建eureka server,声明服务器，
```xml
<project>
    <parent><!--声明继承父pom文件-->
        <groupId>alone.aa</groupId>
        <artifactId>spring_cloud_learn</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <dependencies>
        <dependency><!--引入eureka-server  -->
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>
    
    <dependencyManagement>
    		<dependencies>
    			<dependency>
    				<groupId>org.springframework.cloud</groupId>
    				<artifactId>spring-cloud-dependencies</artifactId>
    				<!--<version>${spring-cloud.version}</version>
    				这里的version应该，使用下面这种写法-->
    				<version>Finchley.RELEASE</version>
    				<type>pom</type>
    				<scope>import</scope>
    			</dependency>
    
    		</dependencies>
    	</dependencyManagement>
</project>
```

在启动类中加上:`@EnableEurekaServer`注解
```java
@SpringBootApplication
@EnableEurekaServer  // 声明服务注册中心
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}
}
```

配置文件:
```yaml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false    #通过eureka.client.registerWithEureka：false和
    fetchRegistry: false         #fetchRegistry：false来表明自己是一个eureka server.
    serviceUrl:           #配置服务的url
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      
  server:
    enable-self-preservation: false        #开发模式下可以关闭eureka的自我保护模式生产模式不要设置

spring:
  application:
    name: eurka-server          #应用名
     
```
配置成功后访问:`http://localhost:8761`

#### 4.创建客户端:eureka client,
创建子module，使用spring initialir 创建，选择cloud discovery->eureka discovery

pom.xml主要内容
```xml
<project>
<parent><!--引入父Pom-->
		<groupId>alone.aa</groupId>
		<artifactId>spring_cloud_learn</artifactId>
		<version>1.0-SNAPSHOT</version>
</parent>

<dependencies>
		<dependency><!--声明客户端-->
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<!--<version>${spring-cloud.version}</version>-->
				<version>Finchley.RELEASE</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

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

ServiceHiApplication
```java
@SpringBootApplication
@EnableEurekaClient  //声明自己是Client
@RestController
public class ServiceHiApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceHiApplication.class, args);
	}

	@Value("${server.port}")  //读取配置文件中的port
	String port;

	@RequestMapping("/hi")   //配置url和默认值
	public String home(@RequestParam(value = "name", defaultValue = "espoir") String name) {
		return "hi " + name + " ,i am from port:" + port;
	}
}
```
配置文件:
```yaml
server:
  port: 8762


eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/      #向该服务注册
  instance:
    hostname: clientName  #配置client的hostname


spring:
  application:
    name: client

```

#### 5.都创建成功后，先启动server 在启动client，访问 http://localhost:8761 ，可以看到server中存在一个Application 名字就是client配置文件里面的名字
访问:`http://localhost:8762/hi` 就会看到 `hi espoir,i am from port:8762`

### 服务消费者（rest+ribbon）
RestTemplate+Ribbon去消费服务
ribbon是一个负载均衡客户端，可以很好的控制htt和tcp的一些行为。Feign默认集成了ribbon

启动eureka-server 工程；启动service-hi工程，它的端口为8762；将client 的配置文件的端口改为8763,并启动，这时你会发现：client在eureka-server注册了2个实例，这就相当于一个小的集群。

配置idea允许启动多个实例 ：edit configuration -> single instance only 取消勾选即可
创建子module，使用spring initialir 创建，选择cloud discovery->eureka discovery，继承父pom
```xml
<project>
    <parent>
            <groupId>alone.aa</groupId>
            <artifactId>spring_cloud_learn</artifactId>
            <version>1.0-SNAPSHOT</version>
    </parent>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<!--<version>${spring-cloud.version}</version>-->
				<version>Finchley.RELEASE</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
</project>
```
启动类：
```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient   //@EnableDiscoveryClient向服务中心注册
public class RibbonApplication {

	public static void main(String[] args) {
		SpringApplication.run(RibbonApplication.class, args);
	}

	@Bean          //向程序的ioc注入一个bean: restTemplate;
	@LoadBalanced  //@LoadBalanced注解开启负载均衡的功能
	RestTemplate restTemplate() {
		return new RestTemplate();
	}
}

```
配置文件:
```yaml
server:
  port: 8763


eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/      #向该服务注册
  #instance:
   # hostname: clientName  #配置client的hostname


spring:
  application:
    name: client

```
controller
```java
@RestController
public class HelloController {
    @Autowired
    private HelloService service;

    @GetMapping(value = "/hi")
    public String hi(@RequestParam String name){
        return service.hiService(name);
    }
}
```
service
```java
@Service
public class HelloService {

    @Autowired
    private RestTemplate template;

    public String hiService(String name){ 
        //此处直接写的程序名字，就是你Client模块中的pom文件中声明的<artifactId>Client</artifactId>
        return template.getForObject("http://CLIENT/hi?name="+name,String.class);
    }
}
```

最后浏览器上多次访问http://localhost:8764/hi?name=espoir
```
hi espoir ,i am from port:8762
hi espoir ,i am from port:8763
```

1. 一个服务注册中心，eureka server,端口为8761
2. service-hi工程跑了两个实例，端口分别为8762,8763，分别向服务注册中心注册
3. sercvice-ribbon端口为8764,向服务注册中心注册
4. 当sercvice-ribbon通过restTemplate调用service-hi的hi接口时，因为用ribbon进行了负载均衡，会轮流的调用service-hi：8762和8763 两个端口的hi接口；

### 服务消费者（Feign）(Finchley版本)

一、Feign简介
Feign是一个声明式的伪Http客户端，它使得写Http客户端变得更简单。使用Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用Feign 注解和JAX-RS注解。Feign支持可插拔的编码器和解码器。Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。

简而言之:

    Feign 采用的是基于接口的注解
    Feign 整合了ribbon，具有负载均衡的能力
    整合了Hystrix，具有熔断的能力
    
准备:启动eureka-server，端口为8761; 启动service-hi 两次，端口分别为8762 、8773.
一、Feign简介
Feign是一个声明式的伪Http客户端，它使得写Http客户端变得更简单。使用Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用Feign 注解和JAX-RS注解。Feign支持可插拔的编码器和解码器。Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。

简而言之：
* Feign 采用的是基于接口的注解
* Feign 整合了ribbon

启动eureka-server，端口为8761; 启动client 两次，端口分别为8762 、8773.

新建子模块，spring-boot工程，取名为service-feign，pom.xml文件如下
```xml
<project>

	<parent>
		<groupId>alone.aa</groupId>
		<artifactId>spring_cloud_learn</artifactId>
		<version>1.0-SNAPSHOT</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-openfeign</artifactId>
		</dependency>


	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Finchley.RELEASE</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
</project>
```
配置文件
```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/         #指明服务
server:
  port: 8765
spring:
  application:
    name: service-feign          

feign:
  hystrix:
    enabled: true
```
启动类
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients  //注解开启Feign的功能
public class ServiceFeignApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceFeignApplication.class, args);
	}

}

```

接口
```java
//@ FeignClient（“服务名”），来指定调用哪个服务
@FeignClient(value = "client",fallback = SchedualHelloServiceImpl.class)  //@ FeignClient（“服务名”），来指定调用哪个服务
public interface SchedualHelloService {

    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    String hello(@RequestParam(value = "name") String name);
}

```
SchedualHelloServiceImpl
```java
@Component
public class SchedualHelloServiceImpl implements SchedualHelloService{
    @Override
    public String hello(String name) {
        return "sorry "+name;
    }
}
```
controller
```java
@RestController
public class HelloController {

    //编译器报错，无视。 因为这个Bean是在程序启动的时候注入的，编译器感知不到，所以报错。
    @Autowired
    SchedualHelloService helloService;

    @GetMapping
    public String hello(@RequestParam String name){
        return helloService.hello(name);
    }

}
```
启动程序，多次访问http://localhost:8765/hi?name=espoir,浏览器交替显示
    
    hi espoir ,i am from port:8762
    hi espoir ,i am from port:8763
    
### Hystrix熔断器
Netflix开源了Hystrix组件，实现了断路器模式，SpringCloud对这一组件进行了整合
#### serice-ribbon中使用熔断器
修改serice-ribbon 工程的代码，首先在pox.xml文件中加入spring-cloud-starter-hystrix的起步依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```
在启动类上面加@EnableHystrix，开启Hystrix：
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableEurekaClient
@EnableHystrix
public class HystrixApplication {

	public static void main(String[] args) {
		SpringApplication.run(HystrixApplication.class, args);
	}

	@Bean
	@LoadBalanced
	RestTemplate restTemplate() {
		return new RestTemplate();
	}
}
```
改造HelloService类，在hiService方法上加上@HystrixCommand注解。
该注解对该方法创建了熔断器的功能，并指定了fallbackMethod熔断方法，
熔断方法直接返回了一个字符串，字符串为"hi,"+name+",sorry,error!"，
```java
@Service
public class HelloService {

    @Autowired
    RestTemplate restTemplate;

//@HystrixCommand注解。该注解对该方法创建了熔断器的功能，并指定了fallbackMethod熔断方法
// ，熔断方法直接返回了一个字符串，字符串为"hi,"+name+",sorry,error!"
    @HystrixCommand(fallbackMethod = "hiError")
    public String hiService(String name) {
        return restTemplate.getForObject("http://SERVICE-HI/hi?name="+name,String.class);
    }

    public String hiError(String name) {
        return "hi,"+name+",sorry,error!";
    }

}
```

启动：service-ribbon 工程，当我们访问http://localhost:8764/hi?name=espoir,浏览器显示：

hi espoir,i am from port:8762

此时关闭 service-hi 工程，当我们再访问http://localhost:8764/hi?name=espoir，浏览器会显示：

hi ,espoir,sorry,error!

这就说明当 service-hi 工程不可用的时候，service-ribbon调用 service-hi的API接口时，会执行快速失败，直接返回一组字符串，而不是等待响应超时，这很好的控制了容器的线程阻塞。

Feign中使用断路器

Feign是自带断路器的，在D版本的Spring Cloud中，它没有默认打开。需要在配置文件中配置打开它，在配置文件加以下代码：

`feign.hystrix.enabled=true`

#### Feign中使用熔断器 
基于service-feign工程进行改造，只需要在FeignClient的SchedualServiceHi接口的注解中加上fallback的指定类就行了：
```java
@FeignClient(value = "service-hi",fallback = SchedualServiceHiHystric.class)
public interface SchedualServiceHi {
    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    String sayHiFromClientOne(@RequestParam(value = "name") String name);
}
```
SchedualServiceHiHystric需要实现SchedualServiceHi 接口，并注入到Ioc容器中，代码如下：
```java
@Component
public class SchedualServiceHiHystric implements SchedualServiceHi {
    @Override
    public String sayHiFromClientOne(String name) {
        return "sorry "+name;
    }
}

```

启动四servcie-feign工程，浏览器打开http://localhost:8765/hi?name=espoir,注意此时client工程没有启动，

sorry espoir

打开service-hi工程，再次访问，浏览器显示：

hi espoir,i am from port:8762

这证明断路器起到作用了。

---
title: Crawler
date: 2019-07-28
tags:
- Crawler
categories:
- Crawler
---
1. HttpClient发送Get和Post请求
2. 使用连接池技术和Jsoup解析工具
3. 爬取豆瓣书籍Top250信息，存入数据库
***
<!-- more -->
***
##### 一、基于Maven的入门案例
###### 1.引入依赖:
```xml
<dependencies> 
    <dependency>
        <groupId>org.apache.httpcomponents</groupId>
        <artifactId>httpclient</artifactId>
        <version>4.5.6</version>
    </dependency>      
        <!--日志-->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.25</version>
       <!-- <scope>test</scope>-->
    </dependency>
</dependencies>        
```
###### 2.log4j配置文件
```
        log4j.rootLogger=DEBUG,Al
        log4j.logger.alone.aa = DEBUG
        
        log4j.appender.Al=org.apache.log4j.ConsoleAppender
        log4j.appender.Al.layout=org.apache.log4j.PatternLayout
        log4j.appender.Al.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss.SSS} {%t} {%c}-{%p} %m%n

```
###### 3.发送Get请求:
```java
public class Crawler{
    public static void main(String[] args) throws URISyntaxException {
        //client对象
        CloseableHttpClient client = HttpClients.createDefault();
        //构造url
        URIBuilder uriBuilder = new URIBuilder("http://www.baidu.com/s");
        uriBuilder.setParameter("wd","java");

        //创建get请求的对象,设置url访问地址
        HttpGet get = new HttpGet(uriBuilder.build());
        System.out.println(get);  //GET http://www.baidu.com/s?wd=java HTTP/1.1
        //发起请求，得到响应
        CloseableHttpResponse response = null;
        try {
            response = client.execute(get);
            //解析响应，判断状态，获取数据
            if(response.getStatusLine().getStatusCode()==200){
                //获取响应实体.
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "utf-8");
                System.out.println(content);
                System.out.println(get);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```
###### 4.发送Post请求
```java
public class CrawlerPost {
    public static void main(String[] args) throws Exception{
        //client对象
        CloseableHttpClient client = HttpClients.createDefault();
        //创建post对象
        HttpPost httpPost = new HttpPost("http://www.baidu.com/s");
        //List集合封装表单中的参数
        List<NameValuePair> params=new ArrayList<NameValuePair>();
        params.add(new BasicNameValuePair("wd","java"));
        //创建表单的Entity对象
        UrlEncodedFormEntity formEntity = new UrlEncodedFormEntity(params, "utf-8");

        //设置表单的Entity对象到Post请求当中
        httpPost.setEntity(formEntity);
        System.out.println(httpPost);

        //发起请求，得到响应
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpPost);
            //解析响应，判断状态，获取数据
            if(response.getStatusLine().getStatusCode()==200){
                //获取响应实体.
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "utf-8");
                System.out.println(content);;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

***

###### 5.使用连接池ClientPool创建HttpClient:
```java
public class ClientPool {
    public static void main(String[] args) throws Exception {
        //连接池管理器
        PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager();
        //最大连接数
        cm.setMaxTotal(100);
        //主机最大连接数  //连接平衡
        cm.setDefaultMaxPerRoute(10);
        //
        doGet(cm);
    }

    private static void doGet(PoolingHttpClientConnectionManager cm) {
        //从连接池中获取Client对象
        CloseableHttpClient client = HttpClients.custom().setConnectionManager(cm).build();

        HttpGet get = new HttpGet("https://book.douban.com/top250");

        //配置请求信息
        RequestConfig config = RequestConfig.custom().setConnectTimeout(1000)
                .setSocketTimeout(10*1000)  //传输时间
                .build();
        //给请求设置信息
        get.setConfig(config);

        CloseableHttpResponse response = null;
        try {
            response = client.execute(get);
            if (response.getStatusLine().getStatusCode()==200){
                String content = EntityUtils.toString(response.getEntity());
                System.out.println(content);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(response!=null){
                try {
                    response.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
***
##### 二、使用Jsoup解析抓取的数据:
###### 1.jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据

jsoup主要功能:

    1. 从一个URL，文件或字符串中解析HTML；
    2. 使用DOM或CSS选择器来查找、取出数据；
    3. 可操作HTML元素、属性、文本
    4. 虽然Jsoup可以替代HttpClient发起请求解析数据，但Jsoup对多线程，连接池，代理等支持不友好，
       所以一般只把Jsoup当做解析工具使用
    
###### 2.引入依赖:
```xml
<dependencies>
        <!--日志依赖-->
   
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
           <!-- <scope>test</scope>-->
        </dependency>

        <!--jsoup-->
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.11.3</version>
        </dependency>

        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- 解析工具类 -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>

        <!-- 字符串处理工具 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
    </dependencies>
```
###### 3.测试:
```java
public class Test {
    @org.junit.Test
    public void testURL() throws Exception {
        //解析url,封装为Document
        Document document = Jsoup.parse(new URL("https://book.douban.com/top250"), 5000);
        //获取title标签
        Element title = document.getElementsByTag("title").first();
        System.out.println(title.text());
    }

    @org.junit.Test
    public void testString() throws Exception {
        //使用工具类读取文件，获取字符串
        String content = FileUtils.readFileToString(new File("E://test.html"),"utf-8");
        //解析
        Document document = Jsoup.parse(content);
        Element title = document.getElementsByTag("title").first();
        System.out.println(title.text());
    }

    @org.junit.Test
    public void testFile() throws Exception {
        //Jsoup解析文件
        Document document = Jsoup.parse(new File("E://test.txt"), "utf-8");
        //获取标签
        Element title = document.getElementsByTag("title").first();
        System.out.println(title.text());
    }
}
```

##### 三、综合案例:爬取豆瓣书籍Top250的内容，结合 SpringBoot JPA等,存入数据库
###### 1.创建普通maven工程，然后next:
###### 2.引入依赖:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent><!--spring boot 父依赖-->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <groupId>alone.aa</groupId>
    <artifactId>crawler-db</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>   <!--springMVC -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency><!--spring Data JPA-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency><!--mysql连接-->
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.32</version>
        </dependency>
        <dependency> <!--log4j-->
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
            <!-- <scope>test</scope>-->
        </dependency>
        <dependency><!--jsoup-->
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.11.3</version>
        </dependency>

        <dependency><!--httpclient-->
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.6</version>
        </dependency>
        <dependency><!-- lombok -->
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
        <dependency><!-- 字符串处理工具 -->
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
    </dependencies>
</project>
```

###### 3.创建引导类:
```java
@SpringBootApplication  //
@EnableScheduling   //使用定时任务
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

###### 4.配置文件:
```yaml
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

###### 5.实体类:
注意：Jpa的列名不能和数据库的关键字相同
```java
@Data
@Entity
@Table(name="top250_book")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 50,name = "book_name")
    private String bookName;

    @Column(length = 50,name = "img")
    private String imgName;

    @Column(length = 254,name = "des")
    private String desc;

    @Column(length = 50,name = "hot")
    private String hot;

    @Column(length = 50,name = "person")
    private String person;

    @Column(length = 50,name = "score")
    private String score;

    @CreatedDate
    @Column(name="created")
    private Date createTime;

    @LastModifiedDate
    @Column(name = "updated")
    private Date updateTime;
}

```

###### 6.BookDao:
```java
@Repository  //
public interface BookDao extends JpaRepository<Book,Long>{
}
```

###### 7.BookServiceImp
```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookDao bookDao;

    @Transactional  //开启事务提交事务
    @Override
    public void save(Book book) {
        bookDao.save(book);
    }

    @Override
    public List<Book> findAll(Book book) {
        //声明查询条件
        Example<Book> example = Example.of(book);
        //根据条件进行查询
        List<Book> list = bookDao.findAll(example);
        return list;
    }
}
```

###### 8.BookService
```java
public interface BookService {
    //添加
    void save(Book book);
    //根据条件查询
    List<Book> findAll(Book book);
}
```

###### 9.BookTask
```java
@Component
public class BookTask {
    @Autowired
    private BookService bookService;
    @Autowired
    private HttpUtils utils;
    //当前任务完成之后多长时间进行下一步操作
    @Scheduled(fixedDelay = 100*1000)
    public void task(){
        //声明需要解析的地址
        String url="https://book.douban.com/top250?start=";
        //按照页面对书籍遍历
        for (int i = 0; i < 1; i++) {
            String html = utils.doGetHtml(url + 25 * i);

            //解析页面，获取商品数据
            this.parse(html);
        }
        System.out.println("抓取完成");

    }

    //解析页面，获取商品数据
    private void parse(String html) {
        //解析html获取Document
        Document document = Jsoup.parse(html);

        //div.indent  表示div的class属性值是indent
        Elements trs = document.select("div.indent > table > tbody > tr");
        for (Element tr : trs) {
            Book book = new Book();
            //https://img3.doubanio.com/view/subject/m/public/s1727290.jpg
            //书对应的封面名称
            String imgUrl = tr.select("td > a > img").first().attr("src");
            String imgName = utils.doGetImage(imgUrl);
            book.setImgName(imgName);

            //获取书名
            String bookName = tr.select("td > div > a").first().attr("title");
            book.setBookName(bookName);

            //获取价格等描述信息
            String desc = tr.select("td > p.pl").first().html();
            book.setDesc(desc);

            //评分
            String score = tr.select("span.rating_nums").html();
            book.setScore(score);

            //评价人数
            String person = tr.select("span.pl").html();
            book.setPerson(person);

            //
            String hot = tr.select("span.inq").html();
            book.setHot(hot);

            bookService.save(book);

            //记得进行部分测试
        }
    }
}
```

###### 10.HttpUtils:
```java
@Component
public class HttpUtils {
    private static int i=0;
    private PoolingHttpClientConnectionManager cm;
    public HttpUtils(){
        //连接池管理器，设置最大连接数
        cm=new PoolingHttpClientConnectionManager();
        cm.setMaxTotal(100);
        cm.setDefaultMaxPerRoute(10);
    }

    //获取页面数据
    public String doGetHtml(String url){
        //获取client对象
        CloseableHttpClient client = HttpClients.custom().setConnectionManager(cm).build();
        //get请求
        HttpGet get = new HttpGet(url);
        get.setConfig(getConfig());
        CloseableHttpResponse response = null;
        try {
            //发起请求，得到响应
            response = client.execute(get);
            if(response.getStatusLine().getStatusCode()==200){
                if(response!=null){
                    //获取响应内容
                    String content = EntityUtils.toString(response.getEntity());
                    return content;
                }            
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (response!=null){
                try {
                    response.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
       return "";
    }

    //获取图片
    public String doGetImage(String url){
        i++;
        //获取client对象
        CloseableHttpClient client = HttpClients.custom().setConnectionManager(cm).build();
        //get请求
        HttpGet get = new HttpGet(url);

        get.setConfig(getConfig());

        CloseableHttpResponse response = null;
        try {
            //发起请求，得到响应
            response = client.execute(get);
            if(response.getStatusLine().getStatusCode()==200){
                if(response!=null){
                    //获取后缀
                    String extName = url.substring(url.lastIndexOf("."));
                    //图片名
                    String imgName=new SimpleDateFormat("yyyyMMddHHmmssSSS").format(new Date())+"_"+i+extName;
                    //String imgName = UUID.randomUUID().toString() + extName;可以使用UUID作为名字，不重复
                    //下载
                    FileOutputStream fos = new FileOutputStream(new File("E://img/" + imgName));
                    response.getEntity().writeTo(fos);
                    //返回图片名
                    return imgName;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (response!=null){
                try {
                    response.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return "";
    }

    //获取get请求的配置信息
    private RequestConfig getConfig(){
        RequestConfig config=RequestConfig.custom()
                .setConnectTimeout(5000)  //建立连接的最大时间
                .setConnectionRequestTimeout(5000) //请求时间
                .setSocketTimeout(50000)  //数据传输的时间
                .build();
        return config;
    }
}
```

###### 项目结构:
 ![img](http://pvgivgzah.bkt.clouddn.com/crawler1_1/crawler2.png)
###### 案例分析:
 ![img](http://pvgivgzah.bkt.clouddn.com/crawler1_1crawler1.png)
###### 结果:
![mark](http://pvgivgzah.bkt.clouddn.com/blog/20190730/Jm7MDeCTh43P.png?imageslim)

github地址 [点我]

***



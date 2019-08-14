---
title: WebMagic应用
date: 2019-07-29
tags:
- Crawler WebMagic
categories:
- Crawler
---
#### webMagic爬取51job某个行业招聘信息，结合Springboot存入数据库
<!-- more -->
```xml
<dependencies>
        <dependency>
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-core</artifactId>
            <version>0.7.3</version>
        </dependency>

        <dependency><!--Bloom依赖，去重-->
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>27.0.1-jre</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/us.codecraft/webmagic-extension -->
        <dependency>
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-extension</artifactId>
            <version>0.7.3</version>
        </dependency>

    </dependencies>
```
log4j.properties
```yaml
log4j.rootLogger=DEBUG,Al
log4j.logger.alone.aa = DEBUG

log4j.appender.Al=org.apache.log4j.ConsoleAppender
log4j.appender.Al.layout=org.apache.log4j.PatternLayout
log4j.appender.Al.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss.SSS} {%t} {%c}-{%p} %m%n
```

```java
public class Demo1 implements PageProcessor {
    //解析页面x
    public void process(Page page) {
/*
        //解析返回的数据page，并把解析到的结果放到ResultItems中
        //使用css获取，参数可以设置属性
        page.putField("div",page.getHtml().css("div.screening-bd ul li","data-title").all());

        //使用xpath获取 w3s使用教程
        page.putField("div2",page.getHtml().xpath("//div[@class=screening-bd]/ul/li/ul/li/a"));

        //使用正则表达式
        page.putField("div3",page.getHtml().css("ul.ui-slide-content li").regex(".*歌唱.*").all());
        //get和toString()方法相同，获取单个结果
        //处理结果API
        page.putField("div3",page.getHtml().css("ul.ui-slide-content li").regex(".*歌唱.*").get());
        page.putField("div3",page.getHtml().css("ul.ui-slide-content li").regex(".*歌唱.*").toString());

        */

        //获取链接
        System.out.println(".....................");
        page.addTargetRequests(page.getHtml().css("div.indent").links().regex(".*1/$").all());
        //#content > div > div.article > div > div > table:nth-child(2) > tbody > tr > td:nth-child(2) > div > a
        page.putField("div6",page.getHtml().css("div#content h1").all());
    }
    private Site site=Site.me()
            .setCharset("utf-8")
            .setTimeOut(10000)//超时
            .setRetryTimes(3000)//重新请求的时间
            .setSleepTime(3) //重试次数
            ;

    public Site getSite() {
        return site;
    }

    public static void main(String[] args) {
        Spider spider = Spider.create(new Demo1())
                .addUrl("https://movie.douban.com/chart")//设置爬取数据的页面
                .addPipeline(new FilePipeline("E:/crawler")) //将爬取到的结果存入文件，默认是输出到控制台
                .thread(5)//设置多线程
                //设置存储方式和去重方式内存存储，使用Bloom去重
                .setScheduler(new QueueScheduler().setDuplicateRemover(new BloomFilterDuplicateRemover(10000000)));
        //查看去重方式
        Scheduler scheduler = spider.getScheduler();
        spider.run();//执行
    }
}

```

#### webMagic爬取51job,与java相关的招聘信息，结合spring boot将数据存入数据库
pom.xml
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
    <artifactId>crawler_demo</artifactId>
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

        <dependency><!-- lombok -->
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <!-- <scope>provided</scope>-->
        </dependency>

        <dependency><!--核心包-->
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-core</artifactId>
            <version>0.7.3</version>
            <exclusions><!--webMagic自带log4j需要去除出一个，避免包发生冲突-->
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>


        <dependency><!--Bloom依赖，去重-->
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>27.0.1-jre</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/us.codecraft/webmagic-extension -->
        <dependency><!--WebMagic扩展包-->
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-extension</artifactId>
            <version>0.7.3</version>
        </dependency>


        <dependency><!-- 字符串处理工具 -->
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>

        <!-- .xml.bind.JAXBException- jdk9报错，jdk9中去掉了这部分jar，需要手动引入  -->
       <!-- <dependency>
            <groupId>javax.xml.bind</groupId>
            <artifactId>jaxb-api</artifactId>
            <version>2.3.0</version>
        </dependency>
        <dependency>
            <groupId>com.sun.xml.bind</groupId>
            <artifactId>jaxb-impl</artifactId>
            <version>2.3.0</version>
        </dependency>
        <dependency>
            <groupId>com.sun.xml.bind</groupId>
            <artifactId>jaxb-core</artifactId>
            <version>2.3.0</version>
        </dependency>
        <dependency>
            <groupId>javax.activation</groupId>
            <artifactId>activation</artifactId>
            <version>1.1.1</version>
        </dependency>-->
    </dependencies>
</project>
```
配置文件application.yml
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
log4j.yml
```yaml
log4j.rootLogger=DEBUG,Al
log4j.logger.alone.aa = DEBUG

log4j.appender.Al=org.apache.log4j.ConsoleAppender
log4j.appender.Al.layout=org.apache.log4j.PatternLayout
log4j.appender.Al.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss.SSS} {%t} {%c}-{%p} %m%n

```
启动类:
```java
@SpringBootApplication
@EnableScheduling //设置定时任务
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```
pojo
```java
@Data
@Entity
@Table(name = "job")
public class Job {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String companyName;
    private String companyAddr;

    @Column(length = 5000)
    private String companyInfo;
    private String jobName;
    private String jobAddr;

    @Column(length = 5000)
    private String jobInfo;
    private String salary;
    //private Integer salaryMin;
    //private Integer salaryMax;
    private String url;
    private String time;
}

```
jobDao
```java
@Repository
public interface JobDao extends JpaRepository<Job,Long>{
}
```
jobService
```java
public interface JobService {
    void save(Job job);

    //根据条件查询
    List<Job>findJob(Job job);
}

```
jobServiceImpl
```java
 @Service
 public class JobServiceImpl implements JobService {
     @Autowired
     private JobDao jobDao;
 
     @Override
     @Transactional //管理事务
     public void save(Job job) {
         //根据url和发布时间查询数据
         Job param = new Job();
         param.setUrl(job.getUrl());
         param.setTime(job.getTime());
 
         List<Job> list = this.findJob(param);
         if(list.size()==0){
             //如果结果为空，表示招聘信息不存在，或者已经跟新了，则需要更新数据库
             jobDao.save(job);
         }
     }
 
     @Override
     public List<Job> findJob(Job job) {
 
         //设置查询条件
         Example example = Example.of(job);
         List list = jobDao.findAll(example);
         return list;
     }
 }

```

任务类
```java
@Component  //声明Spring管理类的创建和销毁
public class JobProcess implements PageProcessor{

    private String url="https://search.51job.com/list/010000%252C050000%252C160300,000000,0000,00,9,99,java,2,2.html?lang=c&stype=1&postchannel=0000&workyear=99&cotype=99&degreefrom=99&jobterm=99&companysize=99&lonlat=0%2C0&radius=-1&ord_field=0&confirmdate=9&fromType=&dibiaoid=0&address=&line=&specialarea=00&from=&welfare=";

    @Override
    public void process(Page page) {
        //String html = page.getHtml().toString();
        //解析页面，获取招聘信息详情的url地址
        List<Selectable> list = page.getHtml().css("div#resultList div.el").nodes();

        //判断获取到的List的集合是否为空，为空则为详情页
        if(list.size()==0){
            //如果为空，表示这是招聘的详情页，解析页面，获取详细信息，保存数据
            this.saveJobInfo(page);
        }else {
            //如果不为空，表示这是列表页,解析出详情页面的url地址，放到任务列表中
            for (Selectable selectable : list) {
                String jobInfoUrl = selectable.links().toString();
                //System.out.println(jobInfoUrl);
                //把url地址添加到任务队列中
                page.addTargetRequest(jobInfoUrl);
            }

            //获取下一页的URL
            String bkUrl = page.getHtml().css("div.p_in li.bk").nodes().get(1).links().toString();
            //System.out.println(bkUrl);
            page.addTargetRequest(bkUrl);
        }
    }

    private void saveJobInfo(Page page) {
        //创建招聘详情对象
        Job job=new Job();
        //解析页面
        Html html = page.getHtml();
        //System.out.println(html);
        job.setCompanyName(html.css("div.cn p.cname a","text").toString());
        job.setCompanyAddr(Jsoup.parse(html.css("div.bmsg").nodes().get(1).toString()).text());
        job.setCompanyInfo(Jsoup.parse(html.css("div.tmsg").toString()).text());
        job.setJobAddr(html.css("div.inbox").nodes().get(1).css("p","text").toString());
        job.setJobInfo(Jsoup.parse(html.css("div.job_msg").toString()).text());
        job.setJobName(html.css("div.cn h1","text").toString());

        String time=Jsoup.parse(html.css("div.cn p.ltype").regex(".*发布").toString()).text();
        job.setTime(time.substring(time.length()-7,time.length()-2));
        job.setUrl(page.getUrl().toString());

        //获取工资
        //Integer[] salary = Salary.getSalary(html.css("div.cn strong", "text").toString());
        //job.setSalaryMax(salary[0]);
        //job.setSalaryMin(salary[1]);
        job.setSalary(html.css("div.cn strong", "text").toString());

        System.out.println(job);
        //保存数据
        page.putField("job",job);
    }

    private Site site=Site.me()
            .setCharset("gbk")
            .setTimeOut(10*1000)
            .setRetrySleepTime(3000) //重试时间
            .setRetryTimes(3); //重试次数
    @Override
    public Site getSite() {
        return site;
    }

    @Autowired
    private JobPipeline jobPipeline;

    //该方法是程序的主方法
    //initialDelay表示任务启动等多久执行方法
    //fixedDelay表示隔多久执行方法
    // @Scheduled(initialDelay = 1000,fixedDelay = 100*1000)
    public void process(){
        Spider.create(new JobProcess())
                .addUrl(url)
                .setScheduler(new QueueScheduler().setDuplicateRemover(new BloomFilterDuplicateRemover(100000)))
                .thread(10)
                .addPipeline(this.jobPipeline)
                .run();
    }
}

```

```java
@Component  //标识由Spring可以注入对象
public class JobPipeline implements Pipeline {
    @Autowired
    private JobService jobService;
    @Override
    public void process(ResultItems resultItems, Task task) {
        //获取封装好的对象
        Job job = resultItems.get("job");
        if(job!=null){
            this.jobService.save(job);
        }
    }
}
```
将薪资抽取出来转化为数字的工具类(部分完成)
```java
public class Salary {
    public static Integer[] getSalary(String salaryStr){
        //声明存放薪水范围的数组
        Integer[] salary=new Integer[2];
        /*
            500/天
            0.8-1.2万/月
            5-8千/月
            5-6万/年
        */
        String date=salaryStr.substring(salaryStr.length()-1,salaryStr.length());
        //如果是按照天则直接乘以240进行计算
        if(!"月".equals(date) && !"年".equals(date)){
            salaryStr = salaryStr.substring(0, salaryStr.length() - 2);
            salary[0]=salary[1]=str2Num(salaryStr,240);
            return salary;
        }

        String unit=salaryStr.substring(salaryStr.length()-3,salaryStr.length()-2);
        String[] salarys=salaryStr.substring(0,salaryStr.length()-3).split("-");

        salary[0]=mathSalary(date,unit,salarys[0]);
        salary[1]=mathSalary(date,unit,salarys[1]);
        return salary;
    }

    /**
     *
     * @param date 时间
     * @param unit 工资单位
     * @param salaryStr 工资字符串
     * @return
     */
    private static Integer mathSalary(String date, String unit, String salaryStr) {
        Integer salary=0;
        //判断是否是万
        if ("万".equals(unit)){
            //如果是万，薪水乘 10000
            salary=str2Num(salaryStr,10000);
        }else {
            salary=str2Num(salaryStr,1000);
        }

        //判断时间是否是月
        if("月".equals(date)){
            salary=str2Num(salaryStr,12);
        }
        return salary;
    }

    private static Integer str2Num(String salaryStr, int num) {
        try {
            //将字符串转化为小数，必须用Number接收，否则会有精度丢失问题
            Number result=Float.parseFloat(salaryStr)*num;
            return result.intValue();
        } catch (NumberFormatException e) {
            e.printStackTrace();
        }
        return 0;
    }
}

```

### 使用定时器
Cron表达式有专门的生成器，如 Quartz表达式生成器
使用Spring内置的Spring Task，Spring3.0加入的定时任务，
@Scheduled
参数cron表示在任务特定时间执行
```java
@Component
public class Test {
    //使用cron表达式设置定时任务 从0秒开始，每隔五秒执行一次  0/5 * * * * *
    @Scheduled(cron="0/5 * * * * * ")
    public void test(){
        System.out.println("我是定时器");
    }
}
```

#### 网页去重
前两种方式，计算相同时，只要数据的内容有一个符号不一样就会认为重复，不适合分析分账内容是否重复
1.md5加密
2.bloomFilter
3.KMP算法：时空复杂度较高，不适合数据多的场景
4.Google的simhash，进行相似度对比，实现网页去重
simhash实现:github[点我](https://github.com/CreekLou/simhash.git)

[github](https://github.com/Espoir0/Crawler/tree/master/crawler_demo)
### 使用代理
免费代理网址1 : [点我](https://proxy.mimvp.com/free.php)
免费代理网址2 : [点我](https://www.xicidaili.com/)
获取自己Ip网址: [点我](http://200019.ip138.com/)
#### 使用代理
```java
@Component
public class ProxyTest implements PageProcessor {

    @Scheduled(fixedDelay = 1000)
    public void process(){
        //创建下载器downloader
        HttpClientDownloader downloader = new HttpClientDownloader();
        //给下载器设置代理服务器信息
        downloader.setProxyProvider(SimpleProxyProvider.from(new us.codecraft.webmagic.proxy.Proxy("117.127.16.208",8080)));

        Spider.create(new ProxyTest())
                .addUrl("http://200019.ip138.com/")
                .setDownloader(downloader)  //设置下载器
                .run();

    }

    @Override
    public void process(Page page) {
        System.out.println(page.getHtml().css("body p","text").toString());
    }

    private Site site=Site.me();
    @Override
    public Site getSite() {
        return site;
    }
}
```
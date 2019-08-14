---
title: json&ajax
date: 2019-0-0
tags: 
- front-end
categories:
- front-end
---
***
<!-- more -->
### ajax
#### 1.ajax简介
ASynchronous JavaScript And XML 就是异步的JavaScript 和 XML
1.异步和同步：在客户端和服务器端相互通信的基础上
  同步:客户端必须等待服务器端的响应。在等待的期间客户端不能做其他操作。
  异步:客户端不需要等待服务器端的响应。在服务器处理请求的过程中，客户端可以进行其他的操作。

Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术，
通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。
传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。
#### 2.实现方式：
1.原生的JS实现方式:(不重要)
```html
<head>
    <script>
        //定义方法,发送异步请求
        function fun() {
            //1.创建核心对象
            var xmlhttp;
            if (window.XMLHttpRequest){// code for IE7+, Firefox, Chrome, Opera, Safari
                xmlhttp=new XMLHttpRequest();
            }else{// code for IE6, IE5
                xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
            }
            //2. 建立连接
            /* 参数：1. 请求方式：GET、POST
                        * get方式，请求参数在URL后边拼接。send方法为空参
                        * post方式，请求参数在send方法中定义
                    2. 请求的URL：
                    3. 同步或异步请求：true（异步）或 false（同步）*/
            xmlhttp.open("GET","ajaxServlet?username=tom",true);
            
            //3.发送请求
            xmlhttp.send();
            
            //4.接受并处理来自服务器的响应结果
            //获取方式 ：xmlhttp.responseText
            //什么时候获取？当服务器响应成功后再获取
            //当xmlhttp对象的就绪状态改变时，触发事件onreadystatechange。
            xmlhttp.onreadystatechange=function(){
                //判断readyState就绪状态是否为4，判断status响应状态码是否为200
                if (xmlhttp.readyState==4 && xmlhttp.status==200){
                    //获取服务器的响应结果
                    var responseText = xmlhttp.responseText;
                    alert(responseText);
                }
            }
        }
    </script>
</head>
<body>
    <input type="button" value="发送异步请求" onclick="fun();">
</body>
```
2.JQeury实现方式
1.$.ajax()
    语法：$.ajax({键值对});
```html
<head>
	 <script>
        //定义方法
        function fun() {
            //使用 $.ajax() 发送异步请求
            $.ajax({
                url:"ajaxServlet",//请求路径
                type:"POST",      //请求方式
                //data : "username=libai&age=21." //请求参数
                //一般这样写,等效于上面那样json格式
                data:{
                    "username":"libai",
                    "age":21,
                },
                dataType:"json",   //服务器响应回来的数据类型
                
                //服务器响应成功后自动执行的回调函数,参数名任意，参数用来接收服务器响应回来的值
                success:function (data) {
                    alert(data);
                },
                error:function () {  //服务器响应出错执行的回调函数，例如：请求路径错误，数据之间逗号没写
                    alert("出错");
                }
                //最后一个值不要写逗号
            })
        }
    </script>
</head>
<body>
    <input type="button" value="发送异步请求" onclick="fun();">
</body>
```

2.$.get()：发送get请求

    * 语法：$.get(url, [data], [callback], [type])
    * 参数：
        * url：请求路径
        * data：请求参数
        * callback：回调函数
        * type：响应结果的类型
```html
//定义方法
function fun() {
    //使用 $.get() 发送异步请求
    $.get("ajaxServlet",{username:"李白"},function (data) {
        alert(data);
    },"text");
}
```
3.$.post()：发送post请求

    * 语法：$.post(url, [data], [callback], [type])
        * 参数：
            * url：请求路径
            * data：请求参数
            * callback：回调函数
            * type：响应结果的类型

```html
//定义方法
function fun() {
    //使用 $.get() 发送异步请求
    $.post("ajaxServlet",{username:"李白"},function (data) {
        alert(data);
    },"text");
}
```
### json
#### 1.简介:JavaScript Object Notation	JavaScript对象表示法(Notation符号，记号)
java对象
```java
Person p = new Person();
p.setName("张三");
p.setAge(23);
```
josn对象
```
var p = {"name":"张三","age":23,"gender":"男"};
var p = {
         "name":"张三",
         "age":23,
         "gender":"男"
         };
```
	json现在多用于存储和交换文本信息的语法
	进行数据的传输
	JSON 比 XML 更小、更快，更易解析。
	
语法

	基本规则
		数据在名称/值对中：json数据是由键值对构成的
			*键用引号(单双都行)引起来，也可以不使用引号，建议使用
			* 值的数据类型：
				1.数字（整数或浮点数）
				2.字符串（在双引号中）
				3. 逻辑值（true 或 false）
				4. 数组（在方括号中）	{"persons":[{},{}]}
				5. 对象（在花括号中） {"address":{"province"："北京"....}}//里面又是Json对象
				6. null
		* 数据由逗号分隔：多个键值对由逗号分隔
		* 花括号保存对象：使用{}定义json 格式
		* 方括号保存数组：[]
	获取数据:
		1. json对象.键名
		2. json对象["键名"]
		3. 数组对象[索引]
		4. 遍历

```html
<script>
    //定义json对象
    var person={
        name:"李白",
        skill:"青莲剑歌",
        ranking:"1"
    }
    //获取json对象的值
    var name=person.name;
    var name=person["name"];

    //定义json对象，值是数组
    var persons={persons:[{"name":"李白","skill":"青莲剑歌"},
                            {"name":"雅典娜","skill":"无敌"}
                            ]}
    //获取对象的值，是一个数组，然后通过索引获取对应的值
    var name1=persons.persons[1].name;

    //定义json对象，数组中存放json格式
    var persons2=[{"name":"李白","skill":"青莲剑歌"},
            {"name":"雅典娜","skill":"无敌"}];
    var name2=persons2[1].name;
</script>

  //json的遍历
<script>
    //定义json对象
    var person={
        name:"李白",
        skill:"青莲剑歌",
        ranking:"1"
    }

    //遍历json
    for(var key in person){
        alert(key+":"+person[key]);
    }

    var persons2=[{"name":"李白","skill":"青莲剑歌"},
            {"name":"雅典娜","skill":"无敌"}];

    for(var i=0;i<persons2.length;i++){
        var p=persons2[i];
        for(key in p){
            alert(key+":"+p[key])
        }
    }
</script>
```
json转为java对象
```java
public void test3() throws IOException {
    ObjectMapper mapper = new ObjectMapper();
    String json = "{\"name\":\"李白\",\"skill\":\"青莲剑歌\"}";
    Hero hero = mapper.readValue(json, Hero.class);
    System.out.println(hero);
}
```
---
title: java基础
date: 2019-08-10
tags:
- java基础
categories:
- java基础 
---
***
<!-- more -->
#### java学习路线
[1](https://blog.csdn.net/love20165104027/article/details/81271556)
[3](https://blog.csdn.net/m0_37530301/article/details/61441664)
#### java简介
 
    javaSE （java Standard Edition），标准版  个人计算机上的应用
    javaEE(java Enterprise Edition) 企业版，定位在服务端的应用
    javaME java Micro Editon 微型版，定位在 消费型电子产品的应用
    
    JVM  执行bytecode 字节码的虚拟机
    JRE  java runtime environment  :包含jvm ,类库，等必须文件
    JDK  java Development Kit : 包含JRE，java开发工具包，编译器，调试器等

#### 数据类型
1.基本数据类型（primitive data type）

    Java中定义了3类8种基本数据类型
        数值型－ byte 1个字节、 short 2个字节、int  4个字节、 long  8个字节、float  4个字节、 double 8个字节
        字符型－ char    2个字节
        布尔型－boolean  一个bit
        
2.引用数据类型（reference data type）

    引用数据类型的大小统一为4个字节，记录的是其引用对象的地

#### IO
java.io.File 类是文件和目录/文件夹路径名的抽象表示，用于文件和目录的创建、查找和删除,获取，遍历文件夹

    绝对路径：从盘符开始的路径，这是一个完整的路径。
    相对路径：相对于项目目录的路径
      
    //获取当前项目路径
    File file = new File("");
    File absoluteFile = file.getAbsoluteFile();
    String canonicalPath = file.getCanonicalPath();
    
    static String pathSeparator   win ;    linux :      //路径分割符
    static String separator       win \    linux  /     //分隔符
                  

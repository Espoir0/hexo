---
title: OpenCV
date: 2019-08-01
tags:
- 图像识别
categories: 
- 图像识别
---
1.基于win10的OpenCV下载安装及环境配置
<!-- more -->
基于`win10` 的`OpenCV`下载及环境配置
<!-- more -->
#### 下载安装
##### 一、下载OpenCV
进入opencv官网，找到 opencv-win ，点击进入之后，选择版本，下载解压即可。
在解压出来的文件中， bulld 目录下的 java 文件夹下就可以看到 opencv.jar 和其他ddl文件<br/>
[OpenCV官网链接](https://sourceforge.net/projects/opencvlibrary/files/)
##### 二、配置环境
1.在项目的lib目录下新建winlib 然后把上一步解压的文件build目录下的java目录下的文件全部拷贝到winlib目录下
![mark](http://simon.bzk.ink/blog/20190803/65UMt9gQ8gMs.png?imageslim) <br/>
2.打开项目结构，选择Libraries,然后点击加号添加Jar包<br/>
![mark](http://simon.bzk.ink/blog/20190803/0mDKraDY6lP2.png?imageslim)<br/>
3.选择你刚才复制进来的opencv的jar包并确定<br/>
![mark](http://simon.bzk.ink/blog/20190803/7gd6XN3DEigC.png?imageslim)<br/>
4.选中你添加的jar包，点击右侧的加号<br/>
![mark](http://simon.bzk.ink/blog/20190803/YG9EaFlWPPoX.png?imageslim)<br/>
5.根据你的操作系统选择对应的ddl文件<br/>
![mark](http://simon.bzk.ink/blog/20190803/LcO6JioRdigJ.png?imageslim)<br/>
6.配置好后会出现一个下图中的本地库，说明配置成功<br/>
![mark](http://simon.bzk.ink/blog/20190803/aPWHv9NSLEHQ.png?imageslim)<br/>
7.添加到指定模块<br/>
![mark](http://simon.bzk.ink/blog/20190816/s3zrez5OPpwL.png?imageslim)<br/>
##### 三、测试环境是否搭建成功
在yys 模块中新建测试类:<br/>
![mark](http://simon.bzk.ink/blog/20190803/bxPy9si8R2FS.png?imageslim)<br/>
测试代码:
```java
public class Test {
    static {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    }

    public static void main(String[] args) {
        System.out.printf("hello"+Core.VERSION);
        Mat mat=new Mat(5,10, CvType.CV_8UC1,new Scalar(0));
        System.out.println("mat:"+mat);
    }
}
```
测试结果:显示版本即可
```
hello3.4.7
mat:Mat [ 5*10*CV_8UC1, isCont=true, isSubmat=false, nativeObj=0x12105a0, dataAddr=0x1213d80 ]
```
***
#### 常见报错
1.jdk12 ，然后将yys设置为启动classpath
![mark](http://simon.bzk.ink/blog/20190816/G2niSm9t0Qb5.png?imageslim)
2.Objects.isNull报错的时候。将language level设置到8以上
![mark](http://simon.bzk.ink/blog/20190816/sVuvMKDLt0P4.png?imageslim)
3.报错是图片路径不对
```java
Exception in thread "main" CvException [org.opencv.core.CvException: cv::Exception: OpenCV(3.4.7)
 C:\build\3_4_winpack-bindings-win64-vc14-static\opencv\modules\imgproc\src\templmatch.cpp:1104: 
error: (-215:Assertion failed) (depth == CV_8U || depth == CV_32F) && type == _templ.type() && _img.dims() <= 2 
in function 'cv::matchTemplate'
]
```
4.配置文件无法导入的时候，查看是否将该类的working directory设置为module，不应该是project
![mark](http://simon.bzk.ink/blog/20190817/J7GSgJtSENp1.png?imageslim)

5.模块之间无法项目引用，在modules ->dependencies->添加需要引入的模块
#### opencv
OpenCV的英文全称是Open Source Computer Vision Library。它是一个开源的计算机视觉库，它由一系列 C 函数和少量 C++ 类构成，实现了图像处理和计算机视觉方面的很多通用算法
Mat 类是存储和操作 OpenCV 中图像的主要数据结构
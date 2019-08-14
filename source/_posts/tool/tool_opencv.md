---
title: OpenCV
date: 2019-08-01
tags:
- OpenCV
categories: 
- 图像识别
---
1. 基于win10的OpenCV下载安装及环境配置
<!-- more -->
##### 1.下载OpenCV
opencv官网:找到 opencv-win ，点击进入之后，选择版本，下载解压即可。
在解压出来的文件中，bulld目录下的java文件夹下就可以看到 opencv.jar
[OpenCV官网链接](https://sourceforge.net/projects/opencvlibrary/files/)
##### 2.配置环境
[配置环境链接](https://jingyan.baidu.com/article/0202781151ec471bcc9ce5ca.html)
##### 3.测试环境是否搭建成功
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
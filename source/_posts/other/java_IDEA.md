---
title: IDEA
date: 2019-08-01
tags:
- IDEA
categories:
- IDEA
---
***
<!-- more -->
#### 一、jdk环境变量的配置
1. win+E 打开资源管理器，找到我的电脑，右击鼠标打开属性，->高级系统设置->环境变量
2. 在系统变量栏中点击新建, 变量名;`JAVA_HOME`, 变量值是jdk安装目录: `D:\tools\Java\jdk8u152`
3.继续在系统变量里面找到 Path并选中，然后点击编辑 ，进去之后点击新建 输入`%JAVA_HOME%\bin` ，然后将`%JAVA_HOME%\bin`上移到最上面，最后在你打开的所有界面中选择确认，然后退出即可
***
#### 二、IDEA使用
##### 创建文件或者代码模板
settings->搜索templates->选择code and file templates->就可以创建文件或者代码模板了
##### 1.IDEA导出项目结构树

    tree /f src  //包括文件
    tree >> src  //只有目录

##### 2.IDEA插件下载
[IDEA插件官网](https://plugins.jetbrains.com/idea_ce)
下载插件的两种方式:

    1.file->settings—>Plugins—>browse repo(输入插件名下载)—>Install
    2.file->settings—>Plugins—>Install plug from disk —>选择下载好的插件安装（注意插件和IDEA版本对应）
    注意：Plugin 'XXX' is incompatible with this installation 插件不兼容
    解决: Help->about ->查看idea版本，然后去官网下载对应的版本

常用插件:

    log日志高亮  `Grep Console`
    翻译插件  `Translation`
    自动生成get,set方法插件:`lombok`
    
    设置背景图的插件  `Background Image Plus`
    安装成功后，setting->appearance->background image plus 中设置背景图所在目录,可以设置多张图片切换
    设置单张图片背景 setting->appearance->background image 

##### IDEA更换主题:<br/>
[主题链接](http://www.themesmap.com/)
然后在工具栏点击: File->Import Setting ,提示重启，重启即可
2.字体设置：
修改安装目录下 JetBrains\IntelliJ IDEA 2018.3\jre64\lib\ 中的 ``fontconfig.properties.src``文件部分内容：
    
    allfonts.chinese-ms936=Microsoft YaHei
    allfonts.chinese-ms936-extb=Microsoft YaHei
    allfonts.chinese-gb18030=Microsoft YaHei
    allfonts.chinese-gb18030-extb=Microsoft YaHei
    allfonts.chinese-hkscs=Microsoft YaHei
    allfonts.chinese-ms950-extb=Microsoft YaHei
    
然后在setting ->Editor->Font 中设置自己想要的字体即可. 
打开File->Settings->Editor->Colors Scheme->Colors Scheme Font
 
 
##### IDEA常用快捷键

    psvm/main  		   //main方法快捷键
    sout  	  	       //System.out.println()
    new User().var     //快速创建对象
    5.fori             //快速创建for循环. for(int i=0;i<5;i++){}
    按ctrl并点击        //查看源码
    ctrl+alt+v         //自动补全 等号 左侧代码、（  变量名和属性名，）
    
    Tab			       //输入一个标签后，按 Tab，补全代码
    home ←    pgup ↑    pgdn ↓     end→    //光标跳至代码位置
    
    shift  +f6         //选中变量修改变量名，替换所有相关代码中的该变量名
    ctrl+shift +f10    // 运行程序
    双击shift		   //全局查找文件、类
    ctrl +f            //在文件内查找内容
    Ctrl+/或Ctrl+Shift+/	//注释（//或者/**/）
    
    alt + insert  	        // 快速创建   generate constructor ,getter or setter method。。。。。。
    alt + insert            //光标放在packet上面时，alt+insert快速创建文件  
    alt + enter             //将光标放在提示代码行按   alt+enter    快速导包 || 捕获异常 ||rename类名||
    ctrl  +win +alt +t      //选中代码块，快速创建try catch || if else 等
    Tab / Shift+Tab         //选中的代码块后退 / 前进
    _
    ctrl+w 		            //自动选取代码
    Ctrl+Y                  // 删除光标所在行
    ctrl+X                  //剪切行
    Ctrl+D                  //复制光标所在行的内容，插入光标位置下面

#### 三、IDEA常见问题
1.IDEA手动创建webapp项目，web.xml文件中部分配置无效时

    如:web.xml中配置的 servlet-mapping无效时
    打开Project Structure界面，Modules>Web>Deployment descriptor，中点击加号添加你的web.xml文件即可
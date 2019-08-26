---
title: AdminLTE
date: 2019-0-0
tags:
- front-end
categories:
- front-end
---
***
<!-- more -->
#### 1.简介:AdminLTE介绍 AdminLTE是一款建立在bootstrap和jquery之上的开源的模板主题工具，
它提供了一系列响应的、 可重复使用的组件，并内置了多个模板页面；同时自适应多种屏幕分辨率，兼容PC和移动端。
通过AdminLTE，我们可以快速的创建一个响应式的Html5网站。AdminLTE框架在网页架构与设计 上，
有很大的辅助作用，尤其是前端架构设计师，用好AdminLTE 不但美观，而且可以免去写很大 CSS与JS的工作量。
[github地址](https://github.com/almasaeed2010/AdminLTE)
[admin中文文档](http://adminlte.la998.com/documentation/)
[adminlte国内版](https://github.com/itheima2017/adminlte2-itheima)
#### 2.index.html各部分
```html
<body class="hold-transition skin-purple sidebar-mini">
    <div class="wrapper">
    <!-- 页面头部 -->
    <header class="main-header">
    <!-- 导航侧栏 -->
    <aside class="main-sidebar">
    <!-- 内容区域 -->
    <div class="content-wrapper">
    <!-- 底部导航 -->
    <footer class="main-footer">
```
#### 3.AdminLTE布局与皮肤 布局.wrapper包住了body下的所有代码 

    .main-header里是网站的logo和导航栏的代码 
    .main-sidebar里是用户面板和侧边栏菜单的代码 
    .content-wrapper里是页面的页面和内容区域的代码 
    .main-footer里是页脚的代码 .control-sidebar里是页面右侧侧边栏区域的代码 
布局选项 

    fixed：固定 layout-boxed：盒子布局 
    layout-top-nav：顶部隐藏 
    sidebar-collapse：侧边栏隐藏 
    sidebar-mini：侧边栏隐藏时有小图标 
    皮肤skin-blue：蓝色 skin-black：黑色 skin-purple：紫色 skin-yellow：黄色 skin-red：红色 skin-green：绿色
    以上项我们可以在start.html页面中查看。

#### 4.使用
汉化版中最终发布的release，只需要关注release下的文件即可
在release目录下有css、img、pages、plugins目录，plugins中是相关的插件，例如jquery、bootstrap等相关的css与js文件。使用的时候将release目录下的4个文件复制即可
pages中有两种文件,一种是以all开头的html页面，一种不是。例如admin-404.html与all-admin- 404.html页面，这两个文件的区别在于all开头的文件可以直接运行显示页面全部信息，而未以all开 头的文件它是需要与其它文件进行组合而展示 页面全部信息。

    1.添加index.html
    2.修改图标,打开完整版的UI图标，找到合适的图标class，复制到对应位置即可，F12,点击元素抓取，找到图标位置
    3.复制一份index.html，在内容区域复制一个table进来
    4.下单时间中，复制一个时间div进来，如果发现效果没有，还需要复制相应的jq代码

---
title: AdminLTE
date: 2019-0-0
tags:
- front-end
categories:
- front-end
---
***
<!-- more -->
#### 1.简介:AdminLTE介绍 AdminLTE是一款建立在bootstrap和jquery之上的开源的模板主题工具，
它提供了一系列响应的、 可重复使用的组件，并内置了多个模板页面；同时自适应多种屏幕分辨率，兼容PC和移动端。
通过AdminLTE，我们可以快速的创建一个响应式的Html5网站。AdminLTE框架在网页架构与设计 上，
有很大的辅助作用，尤其是前端架构设计师，用好AdminLTE 不但美观，而且可以免去写很大 CSS与JS的工作量。
[github地址](https://github.com/almasaeed2010/AdminLTE)
[admin中文文档](http://adminlte.la998.com/documentation/)
[adminlte国内版](https://github.com/itheima2017/adminlte2-itheima)
#### 2.index.html各部分
```html
<body class="hold-transition skin-purple sidebar-mini">
    <div class="wrapper">
    <!-- 页面头部 -->
    <header class="main-header">
    <!-- 导航侧栏 -->
    <aside class="main-sidebar">
    <!-- 内容区域 -->
    <div class="content-wrapper">
    <!-- 底部导航 -->
    <footer class="main-footer">
```
#### 3.AdminLTE布局与皮肤 布局.wrapper包住了body下的所有代码 

    .main-header里是网站的logo和导航栏的代码 
    .main-sidebar里是用户面板和侧边栏菜单的代码 
    .content-wrapper里是页面的页面和内容区域的代码 
    .main-footer里是页脚的代码 .control-sidebar里是页面右侧侧边栏区域的代码 
布局选项 

    fixed：固定 layout-boxed：盒子布局 
    layout-top-nav：顶部隐藏 
    sidebar-collapse：侧边栏隐藏 
    sidebar-mini：侧边栏隐藏时有小图标 
    皮肤skin-blue：蓝色 skin-black：黑色 skin-purple：紫色 skin-yellow：黄色 skin-red：红色 skin-green：绿色
    以上项我们可以在start.html页面中查看。

#### 4.使用
汉化版中最终发布的release，只需要关注release下的文件即可
在release目录下有css、img、pages、plugins目录，plugins中是相关的插件，例如jquery、bootstrap等相关的css与js文件。使用的时候将release目录下的4个文件复制即可
pages中有两种文件,一种是以all开头的html页面，一种不是。例如admin-404.html与all-admin- 404.html页面，这两个文件的区别在于all开头的文件可以直接运行显示页面全部信息，而未以all开 头的文件它是需要与其它文件进行组合而展示 页面全部信息。

    1.添加index.html
    2.修改图标,打开完整版的UI图标，找到合适的图标class，复制到对应位置即可，F12,点击元素抓取，找到图标位置
    3.复制一份index.html，在内容区域复制一个table进来
    4.下单时间中，复制一个时间div进来，如果发现效果没有，还需要复制相应的jq代码


---
title: maven
date: 2019-08-04
tags: 
- maven
categories:
- 工具
---
1. maven简介
2. maven使用
***
<!-- more -->
#### Maven简介:
Maven 是一个项目管理工具，它包含了一个项目对象模型 (POM：Project Object Model)，
一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管理系统(Dependency Management System)，
和用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)

Maven可以帮你构建工程，管理 jar包，编译代码，还能帮你自动运行单元测试，打包，生成报表，甚至能帮你部署项目，生成 Web 站点。

Maven 的一个核心特性就是依赖管理。当我们涉及到多模块的项目（包含成百个模块或者子项目），管理依赖就变成
一项困难的任务。Maven 展示出了它对处理这种情形的高度控制。
传统的 WEB 项目中，我们必须将工程所依赖的 jar 包复制到工程中，导致了工程的变得很大。那么
maven 工程是如何使得工程变得很少呢？而maven的依赖管理就是管理jar包的过程

maven中，确定一个jar包需要根据坐标来确定,maven会根据坐标去仓库中寻找对应的jar包

坐标 组织名+项目名+项目版本，如：
```
<groupId>com.espoir.aa</groupId>
<artifactId>spring_learn</artifactId>
<version>0.0.1</version>
```
#### maven创建项目使用 archetype
在确认项目信息界面添加一组键值对
添加`archetypeCatalog` `internal`可解决项目创建速度慢的问题

#### Maven项目热部署
我们在开发中反复修改类、页面等资源，每次修改后都是需要重新启动才生效，这样每次启动都很麻烦，浪费了大量的时间，我们可以在修改代码后不重启就能生效，在 pom.xml 中添加如下配置就可以实现这样的功能，我们称之为热部署。

```xml
<!--热部署配置-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

注意：IDEA进行SpringBoot热部署失败原因
原因是因为Intellij IEDA默认情况下不会自动编译，需要对IDEA进行自动编译的设置，如下配置自动编译
Compiler  ->Build project automatically 选上即可
然后 Ctrl+Shift+Alt+/，选择Registry ->compiler.automake.allow.when.app.running 选上即可
maven项目不显示依赖的时候注意看pom.xml文件是否是maven项目，项目是否添加为了maven项目

maven配置环境变量和jdk一样 



---
title: Linux 
date: 2019-0-0
tags:
- 0
categories:
- 0
---
***
<!-- more -->
#### linux下安装jdk1.8
`yum install java-1.8.0-openjdk* -y`

#### 运行spring boot项目 
`java -jar blog.jar`
`nohup java -jar blog.jar &`         //守护式运行

如果没有退出客户端界面，可以先通过 “jobs” 命令查看程序是否在运行，此时只有序号没有PID号；
输入命令 “jobs -l” 会显示程序的PID号，然后通过 “kill -9 PID”杀死程序；
输入命令 “jobs” 查看程序是否被杀死。
方法2：
如果退出过客户端界面，输入 “jobs” 命令查不到正在运行的程序；
输入 `ps ux` 来查看所有程序的进程号PID，然后再通过 “kill -9 PID” 杀死程序


该命令是查看当前所有已经使用的端口情况
`netstat   -nultp`
`netstat  -anp  |grep  3306`   //查看端口

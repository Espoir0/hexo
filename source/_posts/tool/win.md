---
title: win
date: 2019-0-0
tags:
- tool
categories:
- tool
---
***
<!-- more -->
#### 快捷键
    
    win+q   //启动搜索功能
#### win下面的dos命令

    win下面使用命令 /? 可以查看该命令的使用文档
    netstat /?                   //查看netstat的使用文档
    
    iponfig                      //查看本机Ip
    ping www.baidu.com           //测试网络
    dir             //查看当前目录下面的所有文件
    
    netstat
        -a 显示所有连接和侦听端口
        -n 以数字形式显示地址和端口号
        -o 显示拥有的与每个连接关联的进程 ID。 
    netstat -nao|findstr 4000    //找到占用该端口的应用，
        findstr                  //表示在某个文件中查找字符串
    taskkill /pid 21560          //结束该进程
    taskkill /?                  //查看该命令的帮助文档


#### 工具
下载[谷歌访问助手](https://github.com/haotian-wang/google-access-helper)，然后将压缩包解压，
在浏览器输入`chrome://extensions`，（也就是谷歌插件安装的地方），然后将解压后的文件夹拖到安装插件的地方即可

安装插件后，无法正常使用提示，同时提示“该扩展程序未列在Chrome网上应用店中，并可能是在您不知情的情况下添加的”。
    
    1.点击删除按钮，把该插件删除。
    2.找到下载的Crx插件安装文件，把其后缀扩展名重命名为.rar
    3.用解压软件解压刚刚修改的文件。
    4.打开Chrome浏览器，依次点击主界面上的“菜单/更多工具/扩展程序”菜单项。
    5.在打开的扩展程序管理窗口中，点击右上角的“开发者模式”开关，设置为开
    6.接下来点击“加载已解压的扩展程序”快捷链接。  
    7.选择上面刚刚解压后的文件夹，最后点击确定按钮。ok
[Chrome主题](https://www.themebeta.com/)

github加速
[点击这里进行ip解析](https://www.ipaddress.com/)
查询以下三个链接的DNS解析地址，查询到之后，每次查询到的ip任意选取一个即可

    github.com                    192.30.253.112
    assets-cdn.github.com         185.199.108.153
    github.global.ssl.fastly.net  151.101.185.194

修改hosts文件

    文件路径:C:\Windows\System32\drivers\etc\hosts
    
    在文件末尾添加下面内容即可:
        192.30.253.112     github.com 
    
        185.199.111.153    assets-cdn.github.com 
    
        151.101.193.194    github.global.ssl.fastly.net
         
刷新dns缓存:

    打开cmd运行 ipconfig /flushdns 手动刷新系统DNS缓存

机械键盘:
    
    fn+ps 切换
    fn+←  切换灯光


---
title: vim
date: 2019-8-10
tags: 
- tool
categories:
- tool
---
***
<!-- more -->

    vi a.txt       //打开a.txt文件，进入到vim模式，如果不存在则自动新建
    h j k l  	 //是在normal模式下的  上下左右    
    i          //进入插入模式
    esc     //回到 normal模式    
    ctrl+v   // 进入视图模式 visual block     //esc退出

visual block模式下：

    通过h j k l 键或者上下左右键 来操作光标的移动
    选中字符  按   x      //删除选中的所有字符
    选中字符  按   r      //replace 替换选中的所有字符

    多行插入：在视图模式中
    选中多行，shift +i
    输入要插入的内容		 //例如 下面的  1111
    按  esc          			 //退出之后就会看到选中的几行都插入了相同的内容，


ctrl + alt + Z  打开qq·
normal模式下： 
    u                           //在normal模式下按U,回退到上个版本
    
    双击 C  // 删掉当前行，并进入   insert 模式
    
    ctrl +u      //清空光标所在命令行光标之前的内容
    dd      //剪切当前行内容
    p        //粘贴
    yy       //复制当前行
    nyy      //复制多行
    p        //粘贴
    y1y | y2y |y3y      等//表示复制 n 行，y3y复制3行
    d2d      //删除两行
    
    gg 		//跳至第一行
    shift +g	//跳至到最后一行
    5+shift+g    //表示跳至第五行
    gg   d    shift+g   //  删除所有
    .           //表示重复上步操作，或上一组操作
    
    ctrl +u 
    ctrl+a
    tig
    :q
    q

docker中使用vim不小心按 ctrl+ z  后，解决方式，关掉进程
    
    ps  //找到进程的PID
    kill -9 PID
    






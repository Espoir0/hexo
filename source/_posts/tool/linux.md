---
title: Linux 
date: 2019-0-0
tags:
- tool
categories:
- tool
---
***
<!-- more -->
#### $ 普通用户    # root 用户
#### linux目录结构
    /bin：bin是Binary的缩写, 这个目录存放着最经常使用的命令。

    /boot：存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。

    /dev ：(Device设备),存放Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

    /etc：存放所有的系统管理所需要的配置文件和子目录。etc不是缩写，是and so on的意思 来源于 法语的 et cetera 翻译成中文就是 等等 的意思.
     至于为什么在/etc下面存放配置文件， 按照原始的UNIX的说法(linux文件结构参考UNIX的教学实现MINIX) 这下面放的都是一堆零零碎碎的东西, 就叫etc, 这其实是个历史遗留.

    /home：用户主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

    /lib：存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库。

    /lost+found：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

    /media：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。

    /mnt：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。

    /opt：这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

    /proc：虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
    这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件，比如可以通过下面的命令来屏蔽主机的ping命令，使别人无法ping你的机器：
    echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all

    /root：该目录为系统管理员

    /sbin：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

    /selinux：这个目录是Redhat/CentOS所特有的目录，Selinux是一个安全机制，类似于windows的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。

    /srv：存放一些服务启动之后需要提取的数据。

    /sys：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。
    sysfs文件系统集成了下面3种文件系统的信息：针对进程信息的proc文件系统、针对设备的devfs文件系统以及针对伪终端的devpts文件系统。
    该文件系统是内核设备树的一个直观反映。当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。

    /tmp：这个目录是用来存放一些临时文件的。

    /usr：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。

    /usr/bin：系统用户使用的应用程序。

    /usr/sbin：超级用户使用的比较高级的管理程序和系统守护程序。

    /usr/src：内核源代码默认的放置目录。

    /var：系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在/var/log 目录下，另外mail的预设放置也是在这里

    /run：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。

    /bin, /sbin, /usr/bin, /usr/sbin: 这是系统预设的执行文件的放置目录，比如 ls 就是在/bin/ls 目录下的。

    /bin, /usr/bin 是给系统用户使用的指令（除root外的通用户），而/sbin, /usr/sbin 则是给root使用的指令。
#### linux基本命令

    ls: 列出目录（命令参数可以结合）
        -  ：不加参数，表示显示为隐藏的文件
        -a ：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来
        -d ：仅列出目录本身，而不是列出目录内的文件数据
        -l ：长数据串列出，包含文件的属性与权限等等数据
        -al : //参数组合
    cd：切换目录   //Change Directory
        mkdir test
        cd /root/test         //绝对路径
        cd ./test             //相对路径
        cd ~                  //回到家目录 即/root
        cd ..                 //回到上一级目录
    pwd：显示目前的目录          //Print Working Directory
    mkdir：创建一个新的目录
        -p : 递归创建   mkdir test/te/t
    rmdir：删除一个空的目录
        -p : 递归删除
    cp: 复制文件或目录
        -i：若目标档(destination)已经存在时，在覆盖时会先询问
        -p：连同文件的属性一起复制过去，而非使用默认属性(备份常用
        -r：递归持续复制，用於目录的复制行为；(常用)
        cp ~/.bashrc /tmp/bashrc
    rm: 移除文件或目录
        -f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
        -i ：互动模式，在删除前会询问使用者是否动作
        -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！
    mv: 移动文件与目录，或修改文件与目录的名称
        -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
        -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
        -u ：若目标文件已经存在，且 source 比较新，才会升级 (update)
        mv test test2       //修改文件名      

    man cp          //查看命令
    cat a.txt       //查看文件内容
    
    绝对路径：/usr/share/doc 这个目录。
    相对路径：，例如由 /usr/share/doc 要到 /usr/share/man 底下时，可以写成： cd ../man
    
#### 文件查看

    touch  readme.md    //创建新文件  
    cat demo.txt   由第一行开始显示文件内容
    tac  从最后一行开始显示，可以看出 tac 是 cat 的倒著写！
    nl   显示的时候，顺道输出行号！
    more 一页一页的显示文件内容
    less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
    head 只看头几行
    tail 只看最后几行
#### 磁盘管理

    df：列出文件系统的整体磁盘使用量
        -h  以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示
    du：检查当前文件得使用量
        -a   默认仅统计目录底下的文件量
        -h ：以人们较易读的容量格式 (G/M) 显示；
    fdisk：用于磁盘分区
#### vi/vim
vi/vim 共分为三种模式
分别是命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）

命令模式:启动 vi/vim，便进入了命令模式

    vi a.txt		//进入vim编辑文件
    i 切换到输入模式，以输入字符。
    x 删除当前光标所在处的字符。
    : 切换到底线命令模式，以在最底一行输入命令
    hjkl         //上下左右
    ctrl+f       //屏幕向下移动一页
    ctrl+b       //屏幕向上移动一页
    0/Home       //光标移动到行首
    G            //移动到文件的最后一行
    nG           //移动到文件的第n 行
    gg/1G        //移动到文件的第一行
    /word        //光标向下查找word单词
    ?word        //光标向上查找单词
    
输入模式：

    字符按键以及Shift组合，输入字符
    ENTER，回车键，换行
    BACK SPACE，退格键，删除光标前一个字符
    DEL，删除键，删除光标后一个字符
    方向键，在文本中移动光标
    HOME/END，移动光标到行首/行尾
    Page Up/Page Down，上/下翻页
    Insert，切换光标为输入/替换模式，光标将变成竖线/下划线
    ESC，退出输入模式，切换到命令模式

底线命令模式
    
    q 退出程序
    w 保存文件
    ESC 退出底线命令模式
    
    
#### linux下安装jdk1.8
`yum install java-1.8.0-openjdk* -y`
#### 运行spring boot项目 
`java -jar blog.jar`                 //普通运行             
`nohup java -jar blog.jar &`         //守护式运行
，
`jobs`查看程序是否在运行，此时只有序号没有PID号；
`jobs -l `会显示程序的PID号

如果退出过客户端界面
`ps ux`   来查看所有程序的进程号PID
`kill -9 PID`  杀死程序


该命令是查看当前所有已经使用的端口情况
`netstat   -nultp`
`netstat  -anp  |grep  3306`   //查看端口

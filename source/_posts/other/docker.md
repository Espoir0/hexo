---
title: docker的使用
date: 2019-08-02
tags:
- docker
categories:
- docker
---
docker使用
<!-- more -->
Docker的三个概念
镜像（Image）：里面包含有文件系统。任何应用程序运行都需要环境，而镜像就是用来提供这种运行环境的
。例如一个Ubuntu镜像就是一个包含Ubuntu操作系统环境的模板，同理在该镜像上装上Apache软件，就可以称为Apache镜像。
容器（Container）：
一个极简的Linux系统环境（以及运行在其中的应用程序。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。注意：镜像本身是只读的，容器从镜像启动时，Docker在镜像的上层创建一个可写层，镜像本身不变。
仓库（Repository）：镜像仓库，是Docker用来集中存放镜像文件的地方。
注册服务器（Registry）的区别：注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分
##### 1.ssh链接云主机报错:
`ssh root@59.110.xx.xx`
报错：`THE AUTHENTICITY OF HOST XX CAN’T BE ESTABLISHED`
解决办法：
`ssh -o StrictHostKeyChecking=no root@123.59.xx.xx`
输入密码，链接成功
一、`vi ~/.ssh/known_hosts`  删除与想要连接的主机相关的行；或者直接删除known_hosts这个文件。继续使用今后还会出现这样的情况，还得再删除。

二、用OpenSSH的人都知ssh会把你每个你访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts。当下次访问相同计算机时，OpenSSH会核对公钥。如果公钥不同，OpenSSH会发出警告，避免你受到DNS Hijack之类的攻击。
SSH对主机的public_key的检查等级是根据StrictHostKeyChecking变量来配置的。默认情况下，StrictHostKeyChecking=ask。
1.StrictHostKeyChecking=no  
#最不安全的级别，相对安全的内网测试时建议使用。如果连接server的key在本地不存在，那么就自动添加到文件中（默认是known_hosts），并且给出一个警告。
2.StrictHostKeyChecking=ask  #默认的级别，就是出现刚才的提示了。如果连接和key不匹配，给出提示，并拒绝登录。
3.StrictHostKeyChecking=yes  #最安全的级别，如果连接与key不匹配，就拒绝连接，不会提示详细信息。
修改好配置后，重新启动服务即可，命令为：/etc/init.d/sshd restart （或 service sshd restart ）

登录阿里云后，进入的是 /root目录

sudo yum update 将本地yum包更新到最新
2.安装需要的软件包 yum-util提供yum-config-manager功能，另外两个是devicemapper的驱动依赖
 `sudo yum install -y yum-utils device-mapper-persistent-data lvm2`
3.设置yum源为阿里云
`sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`

安装docker
`sudo yum install docker-ce`

设置ustx镜像
vi /etc/docker/daemon.json

`"registry-mirrors":["http://docker.mirrors.ustc.edu.cn"]`

    systemctl start docker  //启动docker
    systemctl status docker  //查看docker状态
    systemctl restart docker  //重启docker
    systemctl enable docker  //设置开机自启
    docker info     // docker信息
    docker --help   // docker帮助文档
    docker images   //docker所有镜像
    REPOSITORY   TAG   ID         created
    所有镜像      标签   镜像唯一id   创建时间
    
    docker search centos 搜索所有的centos镜像
    docker pull docker.io/darksheer/centos   //拉取docker镜像，参数是镜像的完整名,不指定版本默认最新
    
    docker pull centos:7   //拉取docker centos7
    
    删除镜像
    docker rmi id   //或者输入镜像名也行
    docker rmi `docker images -q`   //删除所有镜像 `是反引号
    
    容器
    docker ps   //查看正在运行的所有容器   
    docker ps -a  //查看所有容器  
    
    docker run //创建容器
    -i //运行容器， 
    -t  //容器启动后进入命令行，进入一个虚拟终端
    -i -t // 连在一起写 -it 表示启动并进入
    -v  //目录映射
    -p  //端口映射
    -d //守护式创建
    --name //指定创建的容器的名称
    docker run -it --name=mycentos centos:7 /bin/bash
    exit //退出容器
    docker run -di --name=mycentos2 centos:7  //守护式创建，等你进入docker容器后，使用exit退出，不会关闭容器
    docker exec -it mycentos2 /bin/bash      //进入docker容器
    
    docker stop name     //停止容器
    docker start name    //启动容器
    
    docker cp filename mycentos:/usr/local          //将宿主机的文件复制到docker中
    docker cp mycentos:/usr/local/filename filename2 //将docker中的文件复制的宿主机
    
    docker run -di -v /usr/local/myhtml:/usr/local/myhtml --name=mycentos3 centos:7
    //将宿主机的目录和docker容器的目录关联，产生映射关系，改变其中一个该目录下的内容时，另一个中的内容也随之改变
    
    docker inspect mycentos3 //查看docker的所有信息
    docker inspect --format='{{.NetworkSettings.IPAddress}}' mycentos3  //查看指定信息
    docker rm mycentos  //删除docker容器
    
    docker pull centos/mysql-57-centos7   //拉取mysql镜像
    
##### docker安装mysql
    docker run -di --name=aa_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=admin
     centos/mysql-57-centos7
     
     
    阿里云暴露端口(防火墙->添加规则)即可连接:连接用服务器的ip和宿主机的端口
    //-p 33306:3306表示把宿主机的33306端口和docker容器的3306端口映射，访问宿主机的33306端口就可以访问到容器的3306
    -e MYSQL_ROOT_PASSWORD=admin   //表示root用户的密码
    
##### docker安装tomcat
    docker pull tomcat
    docker run -di --name=mytomcat -p 9000:8080 -v /usr/local/webapps:usr/local/tomcat/webapps tomcat:7-jre7
    
    用xshell上传文件之后
    将将文件移动到 ：mv demo.war /urs/local/webapps 
    
    
##### docker 部署 Nginx

    docker pull nginx   //拉取镜像
    docker run -di --name=mynginx -p 80:80 nginx  //  然后访问即可 
    进入nginx
    docker exec -it mynginx /bin/bash
    cd etc   //nginx安装在etc目录下
    cd nginx //
    cat nginx.conf  //查看nginx配置文件
    cd conf.d
    cat default.conf  //可以在该文中查看配置信息
    
    发现资源配置在 // /usr/share/nginx/html 文件夹下
    mv myhtml html   //移动的文件多的时候，将文件夹改名为html
    docker cp html mynginx:/usr/share/nginx/        //将改文件夹复制到nginx下
    
##### xshell使用:
    输入ip:xxxxx  确定，第一次连接输入服务器的账号和密码
    yum   -y  install  lrzsz      //安装rz
    rz 上传文件
    
    
##### docker部署redis
    docker pull redis
    docker run -di --name=myredis -p 6379:6379 redis
    redis-cli -h ip  //本地客户端连接远程redis
    
    将docker容器保存为镜像
    docker commit myredis myredis_i
    
    //将镜像保存为文件，然后就可以将文件转移到其他服务器
    docker save -o myredis.tar myredis_i      //-o output
    //将文件恢复
    docker load -i myredis.tar    // -i  input  
    
    
##### Dockerfile使用

    //创建目录
    mkdir -p /usr/local/jdk
    mv jdk-8u221-linux-x64.tar.gz /usr/local/jdk
    cd /usr/local/jdk
    vi Dockerfile   //Dockerfile 是指定名称不可改
    
    
    //文件内容
    FROM centos:7   //指定基础镜像
    MAINTAINER aa   //要创建镜像的作者
    WORKDIR /usr    //指定当前工作目录
    RUN mkdir /usr/local/java  //创建文件夹
    ADD jdk-8u221-linux-x64.tar.gz /usr/local/java
    
    ENV JAVA_HOME /usr/local/java/jdk1.8.0_221  //配置环境变量
    ENV JRE_HOME $JAVA_HOME/jre
    ENV CLASSPATH $JAVAHOME/bin/dt.jar:$JAVAHOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
    ENV PATH $JAVA_HOME/bin:$PATH
    //文件内容结束
    
    
    docker build -t='jdk1.8' .  //构建 -t代表镜像名称， .代表当前文件夹，
    docker images   //可以看到已经出现新创建的镜像 jdk1.8

    
    
    
##### docker 私有仓库

    docker pull registry
    docker run -di --name=registry -p 5000:5000 registry
    //http://59.110.243.60:5000/v2/_catalog    //查看私有仓库 {"repositories":[]}
    "insecure-registries":["59.110.243.60:5000"] //添加信任，让服务器信任仓库地址
    systemctl restart docker   //重启docker
    docker tag myredis_i 59.110.243.60:5000/redis  //将镜像标签
    
    docker images //就会看到镜像中多了一个和 myredis_i的id相同的镜像，是对同一个镜像的不同引用
    59.110.243.60:5000/redis            latest              070e71ec8746        About an hour ago   98.2 MB
    myredis_i                           latest              070e71ec8746        About an hour ago   98.2 MB
    
    docker start registry   //刚才重启了docker，需要重新开启容器
    docker push 59.110.243.60:5000/redis   //上传到仓库
    
    //访问地址可以看到已经将镜像上传到私有仓库
    http://59.110.243.60:5000/v2/_catalog   //{"repositories":["redis"]}
    
    
1、查看安装过的docker：yum list installed | grep docker

2、卸载docker：yum remove -y docker-ce.x86_64

3、删除容器镜像：rm -rf /var/lib/docker
`

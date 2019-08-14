---
title: Hexo
date: 2019-07-26
tags:
- Hexo
categories:
- Blog
---
 用Hexo搭建你的专属博客
 1.下载安装及环境配置
 2.部署到github
 3.绑定域名
 4.部署到coding
<!-- more -->
[Hexo官方文档](https://hexo.io/zh-cn/docs/)
[博客](https://blog.enjoytoshare.club/article/hexo-do-optimization.html)
[csdn](https://blog.csdn.net/sinat_37781304/article/details/82729029)
[教程二](https://blog.csdn.net/BLSPers/article/details/82986039)
[教程一](https://blog.csdn.net/wugenqiang/article/details/88372848)

### 安装软件
1.在官网分别安装`git` 和 `node.js`，安装完成之后,分别用下面命查看对应版本：显示版本则表示安装成功
[git官网](http://git-scm.com/)，安装git一路next
[node.js官网](https://link.jianshu.com/?t=http://nodejs.org/)，点开windows选则current的安装包下载，
安装node.js的过程中有个`add to path` 记得勾选这个
[详细步骤链接](https://blog.csdn.net/wugenqiang/article/details/88372848)

    git --version  
    npm -v


2.:Hexo执行依赖`node .sj`所以先安安装`node.js` ,进入 `git bash`执行下面一行代码设置`node.js`源

    alias cnpm="npm --registry=https://registry.npm.taobao.org --cache=$HOME/.npm/.cache/cnpm --disturl=https://npm.taobao.org/dist --userconfig=$HOME/.cnpmrc"


3.安装Hexo

    cnpm install -g hexo-cli


4.验证是否安装成功

    hexo -v

### 开始配置
1.创建文件夹 `myblog`(文件名任意),打开该文件夹然后鼠标右键进入gitbash环境

    hexo init    
    cnpm install  
    hexo s    //hexo server
    //出现warn不用管，   

2.浏览器访问` http://localhost:4000`出现Hexo 欢迎界面则配置正常

3.
    `crtl+c`   //停止


### 部署
1.github新建仓库，仓库名格式如下：
 
    github用户名+.github.io


2.编辑器打开myblog文件夹
找到  `_config.yml`文件并打开，然后找到 `deploy`标签修改其内容为

    deploy:
      type: git
      repository: https://github.com/Espoir0/Espoir0.github.io.git #复制过来
      branch: master

继续在`_config.yml`文件中找到 `url`,将内容改为你的github仓库链接

    url:  http://Espoir0.github.io


3.git bash环境进入blog根目录：
然后安装hexo-deployer-git


    cnpm install hexo-deployer-git --save



然后：//每次提交的时候都要执行后面两个命令：

    hxeo clean    //清空缓存，clean当你主题设置后发现没效果时，执行该命令
    hexo  g       //生成本地文件 generate
    hexo  d       //部署到github deploy 


4.执行成功后,输入自己的github链接 `http://Espoir0.github.io`   访问出现hexo界面说明成功


### 修改主配置文件：
在config.yml文件中，找到Site，然后设置对应的信息

    #Site
    title: EnjoyToShare  #网站标题
    subtitle: Notes      #网站副标题
    description:  This is MyBlog Created and Used By Espoir     #详情
    keywords:
    author: Espoir   #作者
    language: zh-CN  # 语言
    timezone: Asia/Shanghai    #时区

2.git bash 用下面命令创建`my-blog.md`文件：你的blog文件夹路径最好不要有中文

    hexo new my-blog
    
    
3.创键menu中分类对应的界面
    hexo new page about   #看看menu上还有什么标签没创建就行创建
    hexo new page tags    #创建标签等
    hexo new page categories #创建分类等
    
4.配置主题可以在，theme文件夹中的 config.yml文件配置

DaoVoice 可以提供在线联系的功能，我们可以借助于此在自己的站点上接入了此功能

### 注册域名
1.阿里注册域名  

2.注册成功后,在阿里云的控制台中，打开域名控制台->云解析DNS->域名解析->解析设置  
 
3.点击添加记录  

添加两个A记录，对应的值分别分：

    主机记录: @ 
    记录值: 192.30.252.153 
    主机记录: @ 
    记录值: 192.30.252.154 
    
//默认的域名DNS使用的是：dns29.hichina.com 和 dns30.hichina.com  

再添加一个 CNAME记录

    主机记录: www 
    记录值:   githubname.github.io 
    
4.在hexo本地目录 source 目录下面新建文件 `CNAME.txt`,文件内容就是你的域名

    bzk.ink
    
5.git bash 环境执行下面命令将你的Blog部署的github:

    hexo g  //生成
    hexo d  //部署
    
5.域名绑定

在之前创建的Github仓库里面,点击 Settings，将里面的Custom domain内容设置为我们自己的域名值
例如 `bzk.ink` 点击Save保存

6.浏览器访问域名出现博客页面，则一切OK
    
    http://bzk.ink
    
### 部署到coding,
注册coding,新建任意项目,在查看代码中初始化仓库
设置->代码托管->部署公钥
创建page
复制ssh链接到 _config.yml中

### hexo主题设置
#### next主题设置，在根目录下执行下面代码
    
    git clone https://github.com/theme-next/hexo-theme-next themes/next

   
#### 添加分类.在主题的_config.yml文件中找到menu修改添加tags等即可

    menu:
      home: / || home
      about: /about/ || user
      tags: /tags/ || tags
      categories: /categories/ || th
      archives: /archives/ || archive
      #schedule: /schedule/ || calendar
      #sitemap: /sitemap.xml || sitemap
      #commonweal: /404/ || heartbeat
    
    # Enable / Disable menu icons / item badges.
    menu_settings:
      icons: true #显示图标
      badges: false #显示数字
      
#### 在根目录下创键menu中分类对应的界面

    $ hexo new page about   #
    $ hexo new page tags    #创建标签等
    $ hexo new page categories #创建分类等
    $ hexo new page achives #创建归档等

#### 主题方案设置

    # Schemes
    #scheme: Muse  #这是 Nex默认版本，黑白主调，大量留白
    #scheme: Mist  #Muse 的紧凑版本，整洁有序的单栏外观
    #scheme: Pisces #双栏 Scheme，小家碧玉似的清新
    scheme: Gemini  #双子座，也是双栏形式，和Pisces类似

#### social设置
    social:
      GitHub: https://github.com/Espoir0 || github
      ZhiHu: https://www.zhihu.com/people/bao-zhuang-ke/activities || ioxhost
      E-Mail: mailto:@qq.com || envelope
      CSDN: https://blog.csdn.net || heartbeat
      Gitee: https://gitee.com/ || gitlab
      #简书: https://www.jianshu.com || heartbeat
     
    social_icons:  #设置图标是否显示这里
      enable: true #表示开启
      icons_only: true 只显示图片
      transition: false
      exturl: false
      
#### 头像设置

    avatar:
      url: http://simon.bzk.ink/head.jpg  #头像图片路径 图片放置在next/source/images
      rounded: true  #是否显示圆形头像，true表示圆形，false默认
      opacity: 0.7  #透明度0~1之间
      rotated: true  #是否旋转 true表示旋转，false默认
#### toc边栏中的目录设置
    
    toc:
      enable: true #是否启动侧边栏
      number: true  #自动将列表编号添加到toc。
      wrap: false #true时是当标题宽度很长时，自动换到下一行

#### excerpt_description: 
  
    excerpt_description: true #是否自动摘录主页中的描述作为前导文本。



[主题链接一](https://blog.csdn.net/nightmare_dimple/article/details/86661502)
#### 来必应评论
```
<!-- 来必力City版安装代码 -->
<div id="lv-container" data-id="city" data-uid="MTAyMC80NTYyNy8yMjEzOA==">
	<script type="text/javascript">
   (function(d, s) {
       var j, e = d.getElementsByTagName(s)[0];

       if (typeof LivereTower === 'function') { return; }

       j = d.createElement(s);
       j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
       j.async = true;

       e.parentNode.insertBefore(j, e);
   })(document, 'script');
	</script>
<noscript> 为正常使用来必力评论功能请激活JavaScript</noscript>
</div>
<!-- end -->
```






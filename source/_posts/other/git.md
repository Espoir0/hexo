---
title: Git
date: 2019-08-01
tags:
- Git
categories:
- Tool
---
Git的使用
<!-- more -->

##### git常用命令

    git version    	                // 查看git版本  git --version
    git help 		                //查看git命令（--help在任何环境里都可以用）
    history 		                //查看历史命令
   
    git config --global user.email "you@example.com"      //设置本地账号
    git config --global user.name "Your Name"
    
    git init 		                //创建版本库
    git add .		                //添加   //修改后的文件添加提交，和新文件添加提交过程相同
    git commit -m "'                //提交
    git diff 		                //查看文件被修改了哪些内容
    git log  		                //查看git 日志 ，提交记录，里面会有每次commit的注释信息
    git log --pretty=oneline        //log信息输出在一行上面
    
版本回退：

    HEAD                            //指向当前分支      上个版本   HEAD^   /    HEAD~1
    git reset --hard HEAD^          //回到上个版本
    git reset --hard aa             //回到commit id是qa开头的版本
    git reflog                      //里面记录了你的每次git 命令，里面有每次提交的commit id号，可以通过它进行版本切换
    
    git diff HEAD -- readme.txt     //查看工作区和版本库的区别
    
撤销修改：

    git checkout -- readme.txt      //把readme.txt文件在工作区的修改全部撤销，文件回到修改之前的状态。
    git reset HEAD readme.txt       // 可以把暂存区的修改撤销掉（unstage），重新放回工作区,文件回到未add状态    
    git reset --hard HEAD^          //版本回退
    
删除文件：

    rm     a.txt  	                //删除工作区中的文件，git status发现有文件删除，工作区和版本库的文件不同步
    git rm a.txt                    //如果确认删除，则执行git rm,删除版本库中的文件,
    git commit                      //之后才能提交到版本库，版本中才会删除
    git checkout -- a.txt           //撤销掉工作区的修改，和前面的撤销修改一样，删除掉的文件就恢复了
                                    //git checkout其实是用版本库里的版本替换工作区的版本
    rm -rf .git       //删除git的版本库
    
    
远程库：

    git remote add origin https://github.com/Espoir0/learning_git.git   //添加远程库，origin是自己给添加的远程库起的名字
    git push -u origin master
    git push origin master
    //git push 直接使用默认吧本地当前分支推送到远程当前分支
    //第一次推送master分支时，加-u参数，表示吧本地的推送到远程库，并且和远程库关联
    
    git remote remove origin  //删除关联的远程库
    git remote -v           //查看远程库版本
    git remote 		    //查看远程库
    
    git clone  https://github.com/Espoir0/arryn.git   //克隆下来的库自动关联，不需要再初始化和关联
    
分支：

    git branch name                 //创建分支
    git checkout name               //切换到分支
    git checkout -b name            //创建并切换
    git branch                      //查看分支
    git branch -a                   //查看本地和远程的所有分支
    git merge name                  / /将name分支合并到当前分支
    git branch -d name              //删除分支
    
    分支管理：
    git checkout -b Arryn origin/Arryn     //创建本地分支并关联对应的远程分支，本地和远程分支的名称最好一致  
    git push origin Arryn                  //提交到对应分支
    
    
    //默认本地git库里面显示的文件内容是本地master分支上面的内容，而克隆远程仓库的时候会把本地的master分支和远程库的master分支关联。
    如果想在本地库中查看其他远程库上面的分支，必须在本地库中新建与远程库相对应的分支，然后本地分支切换到要查看的分支即可
    
    
冲突： 
    
    git pull origin master --rebase       
    git rebase continue
    
##### 配置ssh
    
    git的连接方式分为两种一种是https连接，一种是ssh连接
    使用ssh连接比https每次传输的时候都需要输入账号密码，比较麻烦
    
1. 在git环境下使用 ` ssh-keygen -t rsa -C "youremail@example.com" `配置ssh,
  然后在本机c盘中找到`C:\Users\Administrator\.ssh` 文件夹下生成两个文件，id_rsa和id_rsa.pub，
  用记事本打开id_rsa.pub复制里面的内容
2. 登录github点击你的头像，找到setting,打开setting里面的 `ssh and GPG keys`  然后new ssh keys
title 随便取，Key就是你刚刚复制的id_rsa.pub里面的内容，粘贴然后确定就好了。 
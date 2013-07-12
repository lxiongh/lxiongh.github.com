---
layout: post
title:   git手册
category : git
tagline:  
tags : [git]
---
{% include JB/setup %}

[**Git 在线教学**](http://pcottle.github.io/learnGitBranching/)

# 使用git的理由

当写代码的时候, 往往会遇到这样一个问题: `代码的迭代更新`, 以前在还没有接触*版本控制*时, 一般会将代码文件重新复制到一个文件夹, 并命名为`xx(修改版)` `xx(最终版)` `xx(最最终版) ...`, 这样的做法, 即占用了过多的空间, 也会让你在日后对这些代码摸不到头脑. 同时最麻烦的是: 你没法直观地比较不同版本间代码的差异.

正在苦恼摸索之时, *git* 走进了我的视野. *git* 是一个开源的分布式版本控制系统，用以有效、高速的处理从很小到非常大的项目版本管理, 它起初是Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

------------------

# git的配置

## 用户名及邮箱

首先, 你必须让git知道你的名字及邮箱, 以便在后续的代码提交过程中作注释
  
    git config --global user.name "Your Name Here"
    git config --global user.email "your_email@example.com"

---------------

# 关联github账户

## 检查是否已存在 SSH KEY

想要让本地版本库与远程版本库通信, 需要配置下SSH KEY. 如果出现““No such file or directory”或类似的语句，说明缺少ssh的key。

    cd ~/.ssh

## 创建新的 SSH KEY

    ssh-keygen -t rsa -C "your_email@youremail.com"

会出现一些确认信息, 连续按ENTER即可. 一切顺利的话，你可以查看下`~\.ssh\id_rsa.pub`文件，复制里面的key码。

## 增加 SSH KEY 到 github 上 

登录到你的 github 账户, 在 `Setting`栏下的`SSH Public Keys` 中输入刚才得到的 key 码

---------------------------

# 测试配置

测试是否连通github, 可输入如下指令

    ssh git@github.com

如果配置正确，显示 `ERROR: Hi xxx! You've successfully authenticated, but GitHub does not provide shell access
Connection to github.com closed.`

-----------------------

# git 的一些命令

## 创建本地新项目工作树
    
   mkdir new-project
    cd new-project
    git init
    touch README
    git add README
    git commit -m 'first commit'

## 定义远程服务器别名origin

    git remote add origin git@github.com:xxx/new-project.git   

## 本地和远程合并，本地默认分支为master

    git push origin master  


## 更新文件

    vi README

## 自动commit更改文件

    git commit -a     

## 更新至远程

    git push origin master

## 创建和合并分支

    git branch 显示当前分支是master
    git branch new-feature  创建分支
    git checkout new-feature 切换到新分支
    vi page_cache.inc.php
    git add page_cache.inc.php

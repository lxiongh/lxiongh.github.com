---
layout: post
title:  linux 环境变量的设置
category : linux
tagline:  
tags : [path, linux]
---

# 使用命令`echo`显示环境变量

显示觉的变量HOME

    $ echo $HOME
    /home/xionghao

# 设置一个新的环境变量

    $ export MYNAME="my name is lxiongh"
    $ echo $MYNAME
    my name is lxiongh

# 修改已存在的环境变量

    $ MYNAME="change name"
    $ echo $MYNAME
    change name

# 使用`env`命令显示所有的环境变量
  
    $ env
    
More information [1](http://www.cnblogs.com/zhuocheng/archive/2012/02/17/2356234.html)
[2](http://hi.baidu.com/yvoilee/item/68da9e62f8434697c5d24989)
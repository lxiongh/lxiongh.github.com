---
layout: post
title:  Teamviewer 在 Debian 7 下的安装
category : others
tagline: 
tags : [teamviewer]
---

# Teamviewer 在 Debian 7 下的安装

[Teamviewer](http://www.teamviewer.com/zhcn/download/linux.aspx) 的 *deb* 包在 Debian 6 环境下安装是没有问题的，
但是在 Debian 7 环境下只能用 [**tar.gz**](http://download.teamviewer.com/download/teamviewer_linux.tar.gz) 包。

解压 *tar.gz* 包，运行时还会出错，提示缺少 *tvwine.dll.so* 共享库，此时安装 *wine* 即可解决问题。如果是64位系统，
还需按下面方法安装。

    $ sudo apt-get install libxtst6:i386 wine
    

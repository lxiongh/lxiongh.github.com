---
layout: post
title:   Uget安装及启用aria2插件
category : linux
tagline:  
tags : [aria2, uget, dowload]
---
{% include JB/setup %}

## Linux下的多线程下载工具
[Edit From](http://northcamel.com/uget-installed-and-enabled-aria2-plug/)   
在window下, 有迅雷, 快车等优秀的多线程下载工具. 而在linux下, 同样有相当优秀的多线程下载工具 `aria2`, 但是`aria2`基于命令行的下载软件，但还是用着不习惯，那么多命令记不住，可以采用其前端应用uget来实现图形化。

uget 从1.72版本开始采用 aria2 作为后端下载程序，在后台其实是通过 xml-rpc 和 aria2进行通信的。所以，要使得uget + aria2 这个组合正常工作，需要保证下面两个条件都成立：

* 确保：aria2 在编译时激活了 XML-RPC,亦即 aria2 支持 XML-RPC 特性
* 确保 : uget：启动 aria2 时指定了参数`–enable-rpc=true`

第一个条件可以通过terminal来确认。只要Enabled Features中包括 XML-RPC
即可。此时terminal输入`aria2c -v `命令. 

第二个条件的满足修改 编辑 -> 设置 -> 插件-> arguments， 把 –enable`-xml-rpc `修改成`--enable-rpc=true`，若还出错就如下图修改下path为`aria2c`，没错的话path不用改。

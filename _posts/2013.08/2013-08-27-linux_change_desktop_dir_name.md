---
layout: post
title:  Linux桌面文件夹中英文转换
category : linux
tagline: 
tags : [desktop]
---

[转至](http://www.wokeke.com/?p=184)

习惯问题，喜欢使用fedora为您在home目录下自创建的"桌面"、"文档"，"图片" 、"公共" 、"下载"、 "音乐"、"视频"等目录。时间久了，总感觉有一点痛快，在命令行下操作这些目录下的文件时总要切换输入法，总不是那么酣畅淋漓。要是能把这些中文目录改成英文目录就好了。有人会说，重命名不是得了。我试过，重命名可以，不过好像有点问题。例如，把“下载”重命名为“Downloads”，浏览器默认的下载目录变了。

上网查，才知道一些技术牛人们蔑视桌面用户的智商，认为用桌面的人都是连自己的文件目录都管不好的，一定要帮你主动搞好。于是管理Linux桌面的freedesktop.org搞出一个xdg-user-dirs（详见http://www.freedesktop.org/wiki/Software/xdg-user-dirs），要主动为用户创建好一堆子目录，方便我们的使用，于是就有了home目录下那些目录。

好的，切入正题，如何修改，打开终端，在终端下输入命令：

    export LANG=en_US
    xdg-user-dirs-gtk-update

这个时候会弹出一个配置界面，提示是否将中文目录切换为英文目录。选中不再提示，确定。系统会删除没有内容的中文目录，而有内容的目录会保持。并创建8个相应的英文目录如下： "Desktop"、 "Download"、 "Templates"、 "Public"、 "Documents"、 "Music"、 "Pictures"、 "Videos"。此时，您在"位置"里看到的常用中文目录已经变成英文目录。只需要将原中文目录的内容拷贝到相应英文目录，并删除中文目录即可。

接下来再执行

    export LANG=zh_CN.UTF-8

以显示中文。

OK，行了，三个命令就行。可就为一次改变要记三个命令，也麻烦。能不能不记啦。当然可以，那您注销，然后在登录界面选择语言种类为“English(united states)”，进入系统后你就发现整个系统都变成English了，同时还会弹出一个对话框提示是否切换用户目录，选择“Update names”，确认并再注销，在登录界面选择“汉语”，进入系统后事个系统又变成了Chinese了，同样会弹出同一个对话框，选择”Keep old names”。OK，大功告成，一个命令都不需要记，就可完成您想要的结果
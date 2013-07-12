---
layout: post
title:  Display Images in Github Page
category : github
tagline:  
tags : [github, Images]
---
{% include JB/setup %}


问题描述
---------------

在我的github page仓库中，_post的文件结构是

    ./
    ../
    image/
        ./
        ../
        latex_symbol/
        wingide_crack/

    20xx-xx-xx-post.md

我将博文中用到的图片都存储在文件夹image中，并在markdown文件中用相关(relative)引用图片，如

    ![image demo](image/wingide_crack/WingIDE.png)

但是生成github page时无法正常显示图片

![image demo](image/wingide_crack/WingIDE.png)

-------------------

解决方法
--------------

使用绝对路径引用图片

    https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/file.png

正常显示如下

![image demo](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/WingIDE.png)

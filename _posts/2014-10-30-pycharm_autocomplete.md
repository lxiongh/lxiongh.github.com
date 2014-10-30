---
layout: post
title:  Autocomplete setting in IDE pycharm
category: tips
tags: [pycharm, opencv, wxpython, autocomplete]
---

要想pycharm能够自动补全代码，需要将关键文件链接到目录/Library/Python/2.7/site-packages下，其他目录无效。
当目录结构改变后，pycharm可能无法立刻产生变化，可以通过修改Project Interpreter来生效。

## opencv

> 将cv.py, cv2.so链接到目录 /Library/Python/2.7/site-packages

    cd /Library/Python/2.7/site-packages
    sudo ln -s ~/opencv/build/lib/cv.py cv.py
    sudo ln -s ~/opencv/build/lib/cv2.so cv2.so

> 如果没有文件 cv.py，可自行建立一个，里面只有一行代码，为：

    from cv2.cv import *

## wxpython

> 将wxpython下site-packages目录的所有文件及文件夹链接到/Library/Python/2.7/site-packages

    cd /Library/Python/2.7/site-packages
    sudo ln -s ~/homebrew/Cellar/wxpython/3.0.1.1/lib/python2.7/site-packages/wx-3.0-osx_cocoa wx-3.0-osx_cocoa
    sudo ln -s ~/homebrew/Cellar/wxpython/3.0.1.1/lib/python2.7/site-packages/wxversion.py wxversion.py
    sudo ln -s ~/homebrew/Cellar/wxpython/3.0.1.1/lib/python2.7/site-packages/wx.pth wx.pth
    sudo ln -s ~/homebrew/Cellar/wxpython/3.0.1.1/lib/python2.7/site-packages/wxversion.pyc wxversion.pyc
    sudo ln -s ~/homebrew/Cellar/wxpython/3.0.1.1/lib/python2.7/site-packages/wxPython_common-3.0.1.1-py2.7.egg-info wxPython_common-3.0.1.1-py2.7.egg-info



---
layout: post
title:  Python 常用库
category : python
tagline:  
tags : [lib, python]
---

# [用Python做科学计算](http://hyry.dip.jp/tech/book/page/scipy/index.html)

Python是一种面向对象的、动态的程序设计语言，具有非常简洁而清晰的语法，既可以用于快速开发程序脚本，也可以用于开发大规模的软件，特别适合于完成各种高层任务。

随着NumPy、SciPy、matplotlib等众多程序库的开发，Python越来越适合于做科学计算。与科学计算领域最流行的商业软件MATLAB相比，Python是一门真正的通用程序设计语言，比MATLAB所采用的脚本语言的应用范围更广泛，有更多程序库的支持，适用于Windows和Linux等多种平台，完全免费并且开放源码。虽然MATLAB中的某些高级功能目前还无法替代，但是对于基础性、前瞻性的科研工作和应用系统的开发，完全可以用Python来完成。

## [NumPy-快速处理数据](http://hyry.dip.jp/tech/book/page/scipy/numpy.html)

标准的Python中用列表(list)保存一组值，可以用来当作数组使用。但由于列表的元素可以是任何对象，因此列表中所保存的是对象的指针。这样为了保存一个简单的列表[1,2,3]，需要有三个指针和三个整数对象。对于数值运算来说这种结构显然比较浪费内存和CPU计算时间。

此外Python还提供了array模块，它所提供的array对象和列表不同，能直接保存数值，和C语言的一维数组类似。但是由于它不支持多维数组，也没有各种运算函数，因此也不适合做数值运算。

NumPy的诞生弥补了这些不足，NumPy提供了两种基本的对象：ndarray和ufunc。ndarray(下文统一称之为数组)是存储单一数据类型的多维数组，而ufunc则是能够对数组进行处理的函数。

[http://www.numpy.org/](http://www.numpy.org/)

## [SciPy-数值计算库](http://hyry.dip.jp/tech/book/page/scipy/scipy.html)

SciPy在NumPy的基础上增加了众多的数学、科学以及工程计算中常用的模块。例如线性代数、常微分方程数值求解、信号处理、图像处理、稀疏矩阵等等。

[http://www.scipy.org/](http://www.scipy.org/)  
[使用Numpy和Scipy处理图像](http://reverland.org/python/2012/11/12/numpyscipy/)

## [SymPy-符号运算好帮手](http://hyry.dip.jp/tech/book/page/scipy/sympy.html)

SymPy是Python的数学符号计算库，用它可以进行数学表达式的符号推导和演算。

[SymPy Tutorial(译)](http://reverland.org/python/2012/08/30/sympy-tutorial/)  
[SymPy User’s Guide](http://docs.sympy.org/dev/guide.html#guide)

## [matplotlib-绘制精美的图表](http://hyry.dip.jp/tech/book/page/scipy/matplotlib.html)

matplotlib是Python最著名的绘图库，它提供了一整套和MATLAB类似的绘图函数集，十分适合编写短小的脚本程序进行快速绘图。此外，matplotlib采用面向对象的技术实现，因此组成图表的各个元素都是对象，在编写较大的应用程序时通过面向对象的方式使用matplotlib将更加有效。

[Matplotlib Tutorial(译)](http://reverland.org/python/2012/09/07/matplotlib-tutorial/)  
[http://matplotlib.org/](http://matplotlib.org/)

## [Mayavi 三维可视化](http://code.enthought.com/projects/mayavi/)

Mayavi全称"The MayaVi Data Visualizer"，读作"Ma-ya-vee"，目前已经是发展到第二代Mayavi2，到笔者写这篇文章时，最新版本为3.3.1。它可以绘制几乎所有Matlab能绘制的3D数据图，并且有比Matlab更强大的交互性，甚至能录制下对数据图的操作。它使用VTK(Visualization Toolkit)作为3D渲染工具，数据封装则使用的大名鼎鼎的python数值库numpy，Mayavi使用比GPL更宽松的BSD开源协议，免费使用。Mayavi不仅仅是一个python库，它可以作为独立的应用程序(甚至不用安装python解释器)。

[mayavi 4.2.1 documentation](http://docs.enthought.com/mayavi/mayavi/index.html)

----------------

# 图像处理

## Python OpenCV

OpenCV的全称是：Open Source Computer Vision Library。  
OpenCV于1999年由Intel建立，如今由Willow Garage提供支持。OpenCV是一个基于（开源）发行的跨平台计算机视觉库，可以运行在Linux、Windows和Mac OS操作系统上。它轻量级而且高效——由一系列C函数和少量C++类构成，同时提供了Python、Ruby、MATLAB等语言的接口，实现了图像处理和计算机视觉方面的很多通用算法。

[OpenCV 2.1 Python Reference](http://opencv.willowgarage.com/documentation/python/)

## Python Image Library

Python Imaging Library (PIL)为Pthon解释器增加了图像处理能力。这个库支持多种文件格式，并提供强大的图像处理和图形处理能力。

[The Python Imaging Library Handbook](http://effbot.org/imagingbook/)  
[以Python Imaging Library进行图像处理](http://tech.seety.org/python/python_imaging.html)  
[Packages List](http://www.pythonware.com/products/pil/)  

# 其他

## Pydot

[项目主页](https://code.google.com/p/pydot/)  
[http://www.graphviz.org/](http://www.graphviz.org/)  

Demo:  
<pre class="prettyprint">
import pydot
import os
from PIL import Image

g = pydot.Dot(graph_type='graph')

g.add_node(pydot.Node('1',fontcolor='transparent',
            shape='rectangle',image=os.getcwd() + '/a.png'))
g.add_node(pydot.Node('2',fontcolor='transparent',
            shape='rectangle',image=os.getcwd()+'/b.png'))
g.add_edge(pydot.Edge('1','2'))
g.write_png('a_b.png')
</pre> 

<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>

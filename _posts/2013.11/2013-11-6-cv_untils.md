---
layout: post
title:  CV常用工具集
category : research
tagline: 
tags : [Computer Vision, 工具, optimization, convex]
---
{% include JB/setup %}
    
*下面的内容是我在做CVPR2014时用到的工具，其中包括一些图片特征的提取，及优化的工具包*

-----------------------------------------------------------------

# 特征
在计算机视觉问题里，常常需要对图片进行特征提取，以此来表达图片或者图片块。

## Image Level
*GIST*, 这是一个描述图片全局特征的描述子。[Matlab Code](http://people.csail.mit.edu/torralba/code/spatialenvelope/).

![GIST](http://people.csail.mit.edu/torralba/code/spatialenvelope/MixtureRealSketch.jpg)

*BOW*, 基于词袋模型的方法也常用来描述图片，在论文中用到的是 [J. C. Yang](http://www.ifp.illinois.edu/~jyang29/) 的 [ScSPM](http://www.ifp.illinois.edu/~jyang29/ScSPM.htm) 

![ScSPM](http://www.ifp.illinois.edu/~jyang29/images/ScSPM.PNG)

## Region Level
对于每个region的特征，用到了 [	Stephen Gould](http://users.cecs.anu.edu.au/~sgould/) 开发的软件包([The STAIR Vision Library](http://ai.stanford.edu/~sgould/svl/)), 包括了颜色、纹理、形状、位置等信息，总共225维。提取特征的示例代码如下。

The following command processes all .jpg (and corresponding .seg) files in $IMAGEDIR and output feature and label files to $OUTPUTDIR.

    bin/ segImageExtractFeatures −v −o $OUTPUTDIR $IMAGEDIR

------------------------------------------------------------------

# Semantic Texton Forests (STF)
这是一个相当强大的pixel-wise分类器，作者 [Matthewa Johnson](http://www.matthewajohnson.org/research/stf.html)提供了 [C# 源码](http://www.matthewajohnson.org/downloads/research/stf.zip)，有网友还将 C# 源码翻译成了 [Matlab Code](https://github.com/akanazawa/Semantic-texton-forests), 但是只提供了训练 Fisrt Forests 的代码，且没有全监督及弱监督的设置，所以在应用中，还是建议用原作者提供的 C# 源码。

![STF](http://www.matthewajohnson.org/images/research/stf.png)

------------------------------------------------------------------

# Image Segmentation
[Berkeley](http://www.eecs.berkeley.edu/Research/Projects/CS/vision/grouping/resources.html), 此方法相关耗时，分割一张图（320*240）起码也得5分钟（i5, 4-cores）。但是分割效果相当好，如下图所示。

![Berkeley 分割效果图](http://www.eecs.berkeley.edu/Research/Projects/CS/vision/grouping/files/seg.png)

相对于 Berkeley 耗时的方法，CVPR2014 中采用的图片预分割方法是 [SLIC Superpixels](http://ivrg.epfl.ch/research/superpixels), The C++ source code and executable for SLIC superpixels and supervoxels available here: [MS Visual Studio 2008 workspace](http://ivrg.epfl.ch/files/content/sites/ivrg/files/supplementary_material/RK_SLICsuperpixels/SLICSuperpixelsAndSupervoxelsCode.zip), 分割效果如下图：

![SLIC分割效果图](http://ivrg.epfl.ch/files/content/sites/ivrg/files/research/images/RK_SLICSuperpixels/intro_pics/54082_combo.jpg)

------------------------------------------------------------------

# Unary Priors

*[Objectness](http://groups.inf.ed.ac.uk/calvin/objectness/)*, The objectness measure acts as a class-generic object detector. It quantifies how likely it is for an image window to contain an object of any class, such as cars and dogs, as opposed to backgrounds, such as grass and water. We release here software for computing objectness and sampling any desired number of windows from an image according to their probability of containing an object.

![Objectness](http://www.vision.ee.ethz.ch/~calvin/objectness/page/nmsMS+CC+SS_002053.jpg)

*[Salient](http://mmcheng.net/salobj/)*, The proposed algorithm is simple, efficient, naturally multi-scale, and produces full-resolution, high-quality saliency maps. These saliency maps are further used to initialize a novel iterative version of GrabCut for high quality salient object segmentation. 

![Salient](http://cg.cs.tsinghua.edu.cn/people/~cmm/Saliency2/Saliency.JPG)

------------------------------------------------------------------

# 凸优化包

*CVX*, 这是一个 Matlab 的凸优化包，其一个示例如下：

    m = 20; n = 10; p = 4;
    A = randn(m,n); b = randn(m,1);
    C = randn(p,n); d = randn(p,1); e = rand;
    cvx_begin
        variable x(n)
        minimize( norm( A * x - b, 2 ) )
        subject to
            C * x == d
            norm( x, Inf ) <= e
    cvx_end

More detail please click the [website](http://cvxr.com/cvx/)


*MLPython*, 此凸优化包只有v0.1版本，貌似也不更新了，[Website](http://www.dmi.usherb.ca/~larocheh/mlpython/#)

*[Stephen P. Boyd – Software](http://www.stanford.edu/~boyd/software.html)*， 这里包含有很多优化包，其中也包含是 CVX 的 Python 版本 [CVXMOD](http://cvxmod.net/)

*[CVXOPT](http://cvxopt.org/)*, CVXOPT is a free software package for convex optimization based on the Python programming language. It can be used with the interactive Python interpreter, on the command line by executing Python scripts, or integrated in other software via Python extension modules. Its main purpose is to make the development of software for convex optimization applications straightforward by building on Python’s extensive standard library and on the strengths of Python as a high-level programming language.

*MATLAB 官方优化包*， 链接为 [http://www.mathworks.cn/cn/products/optimization/](http://www.mathworks.cn/cn/products/optimization/).
  
------------------------------------------------------------------

# Graph-Cut

*[Multi-label optimization](http://vision.csd.uwo.ca/code/)*, The [gco-v3.0 library](http://www.csd.uwo.ca/~olga/OldCode.html) is for optimizing multi-label energies via the α-expansion and α-β-swap algorithms. It supports energies with any combination of unary, pairwise, and label cost terms. Written in C++, it comes bundled with a MATLAB wrapper. The code was developed by [Olga Veksler](http://vision.csd.uwo.ca/viki/Olga_Veksler) and [Andrew Delong](http://vision.csd.uwo.ca/viki/Andrew_Delong). Andreas Mueller (U. Bonn) has written a [Python wrapper for gco](http://peekaboo-vision.blogspot.ca/2012/05/graphcuts-for-python-pygco.html).


*注：[更多源码](http://blog.csdn.net/zouxy09/article/details/8550952)*
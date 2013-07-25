---
layout: post
title:  Python CV 常用库
category : python
tagline:  
tags : [lib, python]
---
{% include JB/setup %}

## VLFeat

vlfeat是一个开源的计算机视觉算法实现库，其中包括 `HOG, SIFT, MSER, K-Means, hierarchical k-means`等算法

[python 版本项目主页](https://launchpad.net/pyvlfeat)  
[VLFeat.org](http://www.vlfeat.org/index.html)  
[pyvlfeat 0.1.1a3](https://pypi.python.org/pypi/pyvlfeat/0.1.1a3)  

vlsift base demo  

<pre class="prettyprint">
import Image
import numpy
import pylab
import vlfeat
from vlfeat.plotop.vl_plotframe import vl_plotframe
from numpy.random import shuffle


if __name__ == '__main__':
	# VL_DEMO_SIFT_BASIC  Demo: SIFT: basic functionality
	
	# --------------------------------------------------------------------
	#                     Load a figure and convert the to required format
	# --------------------------------------------------------------------
	I = Image.open('../../../data/a.jpg')
	I = vlfeat.vl_rgb2gray(numpy.array(I))	
	I = numpy.array(I, 'f', order='F') # 'F' = column-major order!

	pylab.gray()
	pylab.imshow(I)	
	print 'sift_basic_1'
	
	# --------------------------------------------------------------------
	#                                                             Run SIFT
	# --------------------------------------------------------------------
	f, d = vlfeat.vl_sift(I)
	sel = numpy.arange(f.shape[1])	
	shuffle(sel)
	vl_plotframe(f[:, sel[:50]], color='k', linewidth=3)
	vl_plotframe(f[:, sel[:50]], color='y')
	
	print 'sift_basic_2'
	
#	h3 = vl_plotsiftdescriptor(d(:,sel),f(:,sel)) ;
#	set(h3,'color','k','linewidth',2) ;
#	h4 = vl_plotsiftdescriptor(d(:,sel),f(:,sel)) ;
#	set(h4,'color','g','linewidth',1) ;
#	h1   = vl_plotframe(f(:,sel)) ; set(h1,'color','k','linewidth',3) ;
#	h2   = vl_plotframe(f(:,sel)) ; set(h2,'color','y','linewidth',2) ;
#	
#	vl_demo_print('sift_basic_3') ;

	# --------------------------------------------------------------------
	#                                                      Custom keypoint
	# --------------------------------------------------------------------
	pylab.figure()
	pylab.imshow(I[:200,:200]) 

	fc = numpy.array([99, 99, 10, -numpy.pi/8], 'float64')
	fc = numpy.array(numpy.atleast_2d(fc).transpose(), order='F')
	f, d = vlfeat.vl_sift(I, frames=fc, verbose=False)


#	h3   = vl_plotsiftdescriptor(d,f) ;  set(h3,'color','k','linewidth',3) ;
#	h4   = vl_plotsiftdescriptor(d,f) ;  set(h4,'color','g','linewidth',2) ;
	vl_plotframe(f, color='k', linewidth=4)
	vl_plotframe(f, color='y', linewidth=2)
	
	print 'sift_basic_4'

	# --------------------------------------------------------------------
	#                                   Custom keypoints with orientations
	# --------------------------------------------------------------------
	
	fc = numpy.array([99, 99, 10, 0], 'float64')
	fc = numpy.array(numpy.atleast_2d(fc).transpose(), order='F')
	f, d = vlfeat.vl_sift(I, frames=fc, orientations=True) ;
	
#	h3   = vl_plotsiftdescriptor(d,f) ;  set(h3,'color','k', 'linewidth',3) ;
#	h4   = vl_plotsiftdescriptor(d,f) ;  set(h4,'color','g', 'linewidth',2) ;
	vl_plotframe(f, color='k', linewidth=4)
	vl_plotframe(f, color='y', linewidth=2)
	
	print 'sift_basic_5'

	pylab.show()
	
</pre>  	
	

## GIST

*GIST* 是计算机视觉领域常用来描述图片全局特征的描述子

[GIST 描述子的理论](http://people.csail.mit.edu/torralba/code/spatialenvelope/)  
[pyleargist 2.0.5](https://pypi.python.org/pypi/pyleargist/)  

Demo:  
<pre class="prettyprint">
from pyleargist import leargist
from PIL import Image
path = 'test.png'
im = Image.open(path)
fea = leargist.color_gist(im)
</pre>  

<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>
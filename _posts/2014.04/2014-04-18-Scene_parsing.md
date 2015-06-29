---
layout: post
title: Scene Parsing (ICME 2014, MM 2015)
category : research
tagline: 
tags : [Scene Parsing, Algorithm]
---

Abstract
---
*Data-Driven Scene Understanding by Adaptive Exemplar Retrieval*, **Xionghao Liu**, Wei Yang, Liang Lin, and Jian-Huang Lai, Proc. of IEEE International Conference on Multimedia and Expo (ICME), 2014. 

*Data-Driven Scene Understanding with Adaptively Retrieved Exemplars*, **Xionghao Liu**, Wei Yang, Liang Lin, Qing Wang, Zhaoquan Cai, Jianhuang Lai, MultiMedia, IEEE  (Volume:PP ,  Issue: 99 )

---

This article studies a data-driven approach for semantically scene understanding, without pixelwise annotation and classifier pre-training. Our framework parses a target image with two steps: (i) retrieving its exemplars (i.e. references) from an image database, where all images are unsegmented but annotated with tags; (ii) recovering its pixel labels by propagating semantics from the references. We present a novel framework making the two steps mutually conditional and bootstrapped under the probabilistic Expectation-Maxima (EM) formulation. In the first step, the references are selected by 
jointly matching their appearances with the target as well as the semantics. 
We process the second step via a combinatorial graphical representation, in which the vertices are superpixels extracted from the target and its selected references. Then we derive the potentials of assigning labels to one vertex of the target, which depends upon the graph edges that connect the vertex to its spatial neighbors of the target and to its similar vertices of the references. Two steps can be both solved analytically, and the inference is conducted in a self-driven fashion. In the experiments, we validate our approach on two public databases, i.e. *MSRC-21* and *PASCAL VOC 2007*, and demonstrate superior performances over the state-of-the-arts methods.

Framework
---
A glance of our framework is shown in below, where we semantically segment the target image in a self-driven fashion: The algorithm iterates to retrieve (c) the exemplars matching with the target from (b) the auxiliary data , and (a) parse the target image in the virtue of the strength of the selected exemplars.

![framework](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/framework.jpg)

Experiment
----
Table below shows the average accuracies for our approach incomparison with other competitive algorithms, from whichwe clearly see that our algorithm outperforms the others. Benefit from the semantic constraints incorporated in our approach, we achieve a significant improvements for certain difficult classes, e.g., chair and cat. Serveral visualized results with the corresponding ground-truths are presented in Fig. 4, and more semantic segmentation results are in supplementary material as to the limited space of article. 

![result](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/result.jpg)

The most semantic segmentation results are shown below,

![s0](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/s0.jpg)

![s0](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/s1.jpg)

![s0](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/s2.jpg)

![s0](https://raw.githubusercontent.com/lxiongh/lxiongh.github.com/master/_posts/image/scene_parsing/s3.jpg)

The more semantic segmentation results are available in supplementary material please refer to the site: [http://lxiongh.qiniudn.com/scene_parsing_supplementary_material.pdf](http://lxiongh.qiniudn.com/scene_parsing_supplementary_material.pdf)
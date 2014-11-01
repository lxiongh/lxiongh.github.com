---
layout: post
title:  opencv 与 matplotlib 图片显示不兼容问题
category: tips
tags: [opencv, matplotlib, imshow]
---

## 问题描述

由opencv读取的图片，用matplotlib显示时是有问题

    import cv2
    import matplotlib.pyplot as plt
    
    img = cv2.imread('demo.jpg', cv2.IMREAD_COLOR)
    ax = plt.subplot2grid((1, 1), (0, 0), title='imshow')
    ax.imshow(img, cmap=plt.cm.colors)
    plt.show()     # display not exactly what you want

## 产生原因

由于opencv中彩色图片保留为(b, g, r)形式，而matplotlib显示的图片应为(r, g, b)形式，
所以出现显示由opencv读取的图片，用matplotlib显示时会有问题。


## 解决方法

    b, g, r = cv2.split(img_rlt)
    img = = cv2.merge([r, g, b])
    ax.imshow(img, cmap=plt.cm.colors)

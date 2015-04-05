---
layout: post
title:  Deep Localization Network for car
category: research
tags: [Deep Learning, Caffe, C++, Detection, Localization, 车辆定位]
---

利用了 X.L. Wang [NPIP 2014] (Deep Joint Task Learning for Generic Object Extraction)中的 Localization Network,输入为 227*227 的 RGB 图片,网 络输出为上下角的坐标(x1,y1,x2,y2)。在 X.L. Wang [NIP 2014]的 Localization 网络模型(从 Imagenet 训练得到)的基础上, 利用 2500 张标定好的车辆数据进行 fine-tuning。其中 2000 张用于训练,剩下 500 张用于测试。
本项目源码修改可移步 [http://lxiongh.com/2015/01/20/Caffe_Change_for_Localization/](http://lxiongh.com/2015/01/20/Caffe_Change_for_Localization/)

## Localization Network and Some Results

![](http://lxiongh.qiniudn.com/blog/2015-2-4/fw.png)
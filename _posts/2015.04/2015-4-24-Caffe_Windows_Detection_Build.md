---
layout: post
title:  车辆定位（Windows Build）
category: code
tags: [car, detection, localization, caffe window, window]
---
【原创声明，转载请注明出处】[http://lxiongh.com/2015/04/24/Caffe_Windows_Detection_Build/][post_link]

[post_link]: http://lxiongh.com/2015/04/24/Caffe_Windows_Detection_Build/

本人修改的 caffe detection (windows)（[gitcafe 链接][gitcafe_detection_src]，[github 链接][caffe_det_src]），针对车辆检测编译后的结果，目录中的主要文件如下所示。（注：本机为Window 7 x64 位系统，GTX 650 GPU 显卡。在无显卡机上请使用CPU模式运行）

    images/                    -> 存储了一些测试用图片
    base.prototxt              -> 网络结构定义，匆删除匆修改
    car_mean.binaryproto       -> 车辆均值文件，匆删除匆修改
    model.caffemodel           -> 训练好的模型参数
    det_net.exe                -> 车辆检测主程序
    easy_go.py                 -> python 粘接检测过程，使检测更友好，建议使用
    
    *.dll                      -> 一些必要的动态运行库

> 编译后的版本与模型文件过大，压缩后的大小为 374.2M，所以上传至了百度网盘 [http://pan.baidu.com/s/1kT1OQTT][build]，可自行前往下载。相应代码修改可参见 [博文: Caffe Window 版本][caffe_win_post]

[caffe_win_post]: http://lxiongh.com/2015/04/23/Caffe_Windows_Detection/
[build]: http://pan.baidu.com/s/1kT1OQTT


# det_net.exe
由 [Caffe_Windows_Detection][caffe_det_src] （[gitcafe 链接][gitcafe_detection_src]，[github 链接][caffe_det_src]）源码中的 `./tools/test_net.cpp` 编译得到。用法为：

    det_net.exe net_model pretrained_net_proto iterations output_dir 
    [DRAW/LABEL/BOTH] [GPU/CPU] [Device ID]
    
其中 `net_model` 参数为训练好后的模型，即此处为 `model.caffemodel`，`pretrained_net_proto`参数为模型的结构定义文件，此处为 `base.prototxt`。另外可选参数 `[DRAW/LABEL/BOTH]` 不同设置分别对应『图像上画检测框并输入』、『仅输出检测的上下角点值（x1, y1, x2, y2）』、『同时输出前两者信息』

> 注意：如果用 `det_net.exe` 作检测，文件 `base.prototxt` 里的 `source` 及 `meanfile` 需要做相应的修改，指定到其绝对路径。但不建议这么做，因为提供了 `easy_go.py` 这个更加简便的方法调用 `det_net.exe` 作车辆检测，其隐藏了一些调用细节。

[gitcafe_detection_src]: https://gitcafe.com/lxiongh/Caffe_Windows_Detection
[caffe_det_src]: https://github.com/lxiongh/Caffe_Windows_Detection


# easy_go.py

主要完成需要测试的目录图片文件的检索，临时生成 `imlist.txt`文件。以 `base.prototxt` 为原本，自动修改 `source` 与 `meanfile` 对应的值，临时生成相对应的模型结构文件 `exp.prototxt`，供 `det_net.exe` 调用。`easy_go.py` 用法如下（在 `easy_go.py` 目录下进行）：


    usage: easy_go.py [-h] im_dir out_dir [{DRAW,LABEL,BOTH}] [{GPU,CPU}] [num]
    positional arguments:
      im_dir             the directory of input image
      out_dir            the output directory
      {DRAW,LABEL,BOTH}  type of output {BOTH, DRAW, LABEL}
      {GPU,CPU}          choose GPU or CPU
      num                the num of image to be test
    
    optional arguments:
      h, help         show this help message and exit


---

Enjoy it!

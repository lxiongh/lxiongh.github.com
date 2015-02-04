---
layout: post
title:  Caffe 环境配置
category: research
tags: [Deep Learning, Caffe, Cuda, Ubuntu]
---

本文介绍 [Caffe](http://caffe.berkeleyvision.org) 环境的配置，其实在 [Caffe Installtion](http://caffe.berkeleyvision.org/installation.html) 文档里已经有相当详细的说明。写于此备忘。

## 系统环境

    Ubuntu 12.04 LTS 64位
	Pentium(R) Dual-Core  CPU      E6500  @ 2.93GHz 
	显卡 NVIDIA Corporation GK107 [GeForce GTX 650]
	内存 8G
	
> 注：GPU 不是必须的，Caffe 代码里同时包括了 CPU 及 GPU 版本的代码，且编译不同版本的代码完全可以由 makefile.config 配置（# CPU_ONLY := 1，注释了为 编译 GPU 及 CPU版本，不注释则只编译 CPU版本）

## Caffe 依赖的库


* CUDA library version 6.5 (recommended), 6.0, 5.5, or 5.0 and the latest driver version for CUDA 6 or 319.* for CUDA 5 (and NOT 331.*) (本文选择了 CUDA 5.5)
* BLAS (provided via ATLAS, MKL, or OpenBLAS). （本文选择了 Intel Math Kernel Library (MKL)，需要序列号，但可以用教育网邮箱申请）
* OpenCV (>= 2.4) （直接 apt-get 出来的 OpenCV）
* Boost (>= 1.55, although only 1.55 and 1.56 are tested)
* glog, gflags, protobuf, leveldb, snappy, hdf5, lmdb
* For the Python wrapper Python 2.7, numpy (>= 1.7), boost-provided boost.python （不编译 python 接口的可以不安装）
* For the MATLAB wrapper MATLAB with the mex compiler. (不编译 matlab 接口的可以不安装)


### Intel MKL

commercial and optimized for Intel CPUs, with a free trial and student licenses. 
> Set BLAS := mkl in Makefile.config
	
申请链接 [https://software.intel.com/en-us/intel-parallel-studio-xe][1]

[1]: https://software.intel.com/en-us/intel-parallel-studio-xe

## Library

Ubuntu 12.04 可以直接用 apt-get 安装的依赖库

> sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev
	
Ubuntu 12.04 不能直接用 apt-get 安装的依赖库，系统中应还安装了 git。新建文件 『run.sh』，将下面代码拷贝进去，在命令行下运行 `sudo ./run.sh`，一定要 sudo 权限，这样文件给编译后才能顺利安装进系统。

	# glog
	wget https://google-glog.googlecode.com/files/glog-0.3.3.tar.gz
	tar zxvf glog-0.3.3.tar.gz
	cd glog-0.3.3
	./configure
	make && make install
	# gflags
	wget https://github.com/schuhschuh/gflags/archive/master.zip
	unzip master.zip
	cd gflags-master
	mkdir build && cd build
	export CXXFLAGS="-fPIC" && cmake .. && make VERBOSE=1
	make && make install
	
	# lmdb
	git clone git://gitorious.org/mdb/mdb.git
	cd mdb/libraries/liblmdb
	make && make install

## Cuda 5.5

如果不使用 GPU，可跳过 Cuda 的安装，此时在编译 Caffe 的时候，要将 `CPU_ONLY=1` 取消注释，以便只编译 CPU 版本，防止编译 GPU 版本时报错。

Cuda 5.5 下载链接 [https://developer.nvidia.com/cuda-pre-production][2]，选择对应的系统版本。安装包中包含了『显卡的驱动』及『CUDA Toolkit』，如果系统事先安装了显卡驱动，则只安装『Toolkit』即可，否则同时安装。

Cuda 5.5 的安装及配置可参见博客 [http://www.zhaoyanpeng.cn/archives/352][3]

[2]: https://developer.nvidia.com/cuda-pre-production
[3]: http://www.zhaoyanpeng.cn/archives/352

## Enjoy Caffe
	
	cp Makefile.config.example Makefile.config
	make


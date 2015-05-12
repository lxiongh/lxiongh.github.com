---
layout: post
title:  Caffe Window 代码修改（适用于车辆定位）
category: code
tags: [Deep Learning, Caffe, caffe window, window]
---

【原创声明，转载请注明出处】[http://lxiongh.com/2015/04/23/Caffe_Windows_Detection/][post_link]

修改后适应于车辆检测的代码地址为：[https://github.com/lxiongh/Caffe_Windows_Detection][github_caffe_win_det] 或者 gitcafe 地址 [https://gitcafe.com/lxiongh/Caffe_Windows_Detection][gitcafe_caffe_win_det]

[post_link]: http://lxiongh.com/2015/04/23/Caffe_Windows_Detection/
[github_caffe_win_det]: http://github.com/lxiongh/Caffe_Windows_Detection
[gitcafe_caffe_win_det]: https://gitcafe.com/lxiongh/Caffe_Windows_Detection

# Caffe Window 编译
[caffe][caffe] 原生代码只支持Linux，并没有 window 版本下的相关实现。 由于一个项目中需要用到车辆的检测与定位（已在 linux 下利用 [修改的 caffe][mycaffecode] 代码实现）， 而项目本身的代码至今还没有移植到 linux 下（*无力吐槽师弟这效率*）。 自己动手，丰衣足食。 Google 了下，发现在 caffe 官网下其实给出了第三方的 [window caffe][caffe_win] 版本，由 [niuzhiheng][niuzhiheng_github] 这位牛人修改得到。真是天无绝人之路，得来全不费功夫（*其实也费了些功夫，主要在修改 niuzhiheng 的代码适用于自己的车辆定位问题*）。

## [caffe window][caffe_win] 的环境及其依赖
1. windows-64 bit
2. MS Visual Studio 2012
3. CUDA Tooltik 6.5
4. 其他第三方库（如 opencv, boost, etc）

> niuzhiheng 给出的第三方库[链接][author_other_libs]已经无法打开，可能是 GFW 在作恶。本人在百度网盘里上传了一个[第三方库][my_other_libs]备份（*小插曲：从百度网盘中搜索关键字 dependency-20140804.7z，结果发现是杨师兄上传的，缘分啊~~~*）。

## 编译步骤
为了判断环境及依赖是否正确，利用 niuzhiheng 没有修改过的源码进行编译，先排除由于环境或者依赖不正确带来的编译问题。
1. 解压下载的[第三方库][my_other_libs]，里面包含“3rdparty,bin,tools”这三个文件夹，将三个文件夹拷贝到 caffe window 源码目录下，合并。
2. 用 VS 2012 打开解决方案文件 `./build/MSVC/MainBuilder.sln`
3. 将编译目标切换到 x64 平台
4. 重新生成解决方案

> 按照如下步骤，由于 RP 问题，本人并没有直接编译通过，经研究是由于项目中没有配置 CUDA 的环境。将 CUDA 相关配置增加到项目的“包含目录”及“库目录”，`项目->属性->VC++目录`，编译通过。编译会生成在目录 `./bin` 目录下生成文件 `MainCaller.exe`， 通过改变 `./examples/MainCaller.cpp` 文件内容，选择编译的文件。

---
# cafee window 修改（适用于车辆定位问题）
由于有了之前修改 linux 下的 caffe 源码的相关经验（[博文：Caffe 源码的修改（用于车辆的定位）][caffe_linux_detection]），这个修改还是得心应手的。 为了将代码修改成适用于多标签的训练与测试，总体来讲，修改了下图中所示的部分，其中左栏为修改后的，右栏为未修改前的。

> 如下代码变动截图，均利用 `Beyond Compare` 软件比较得到。

![caffe windows 源码修改部分][caffe_win_change]

## `./src/caffe/proto/caffe.proto`
![caffe.proto change][caffe_proto]

如上图，将 `label` 由 `optional` 修改为 `repeated`，即使其支持多标签输入。 改变这个文件后， 需要用批处理 `./scripts/GeneratePB.bat` 重新生成文件 `caffe.pb.cc` 与 `caffe.pb.h`。项目编译前也会自动调用该批处理程序，但如果已经存在文件 `caffe.pb.h`，则其不会被重新生成。所以在修改了 `caffe.proto` 后，一定要删除原来的 `caffe.pb.cc` 与 `caffe.pb.h` 文件，然后再由批处理程序生成，或者在编译前调用生成。

## `./include/caffe/util/io.hpp`
![io.hpp change][io_hpp]

将 `label` 由 `const int` 型转变为 `const std::vector<int> label`， 用于存储多标签信息。同时需要在 `io.cpp` 文件中作相应的修改。

## `./src/caffe/util/io.cpp`
![io.cpp change][io_cpp]

`io.cpp` 及 `io.hpp` 是最基本的数据读取实现，在 `convert_imageset.cpp` 与 `image_data_layer.cpp` 会直接调用 `ReadImageToDatum()` 函数读取图片数据。

## `./include/caffe/vision_layers.hpp`
![vision_layers.hpp change][vision_layers_hpp]

将 `lines_` 由  `vector<std::pair<std::string, int> >` 转变为 `vector<std::pair<std::string, std::vector<int> > >`，用于在存储图片的路径及标签信息。特别地，将 `lines_` 由 `protected` 修饰改为 `public` 修饰，可只是为了在后续能够更加方便的直接访问图片的路径数据（具体体现在 `test_net.cpp` 中）。

## `./src/caffe/layers/data_layer.cpp`
![data_layer.cpp change][data_layer_cpp]

`data layer` 中数据是从数据库（如 `leveldb`）中读取中，其并不涉及到图片底层的读取， 因为其数据已经由 `convert_imageset.cpp` 转化成了 `leveldb` 形式的数据。 此处只需从 `datum` 中读取相应的标签数据 `datum.label(label_i)` 赋值给 `top_label[item_id*datum.label_size()+label_i]` 即可，同时不要忘了分配相应的空间。

## `./src/caffe/layers/image_data_layer.cpp`
![image_data_layer.cpp change][image_data_layer_cpp]

此层直接读取图片数据及标签信息，首先从模型的配置文件 `*.prototxt` 中读取 `source`（里面包含了图片的绝对路径及图片的标签信息），如下所示。

    E:/cars/03_041.jpg 65 85 151 202
    E:/cars/03_042.jpg 84 85 178 201
    E:/cars/03_043.jpg 90 71 183 191
    
特别地，测试也需给出上例中的文件内容，不同于之处在于用 `0 0 0 0` 替代相应的标签信息。


## `./src/caffe/layers/memory_data_layer.cpp`
![memory_data_layer.cpp change][memory_data_layer_cpp]

本实例中没有用到此层，但还是作了相应的修改。

---

# 车辆检测代码（参考 `test_net.cpp` 修改而来）

{% highlight cpp lineno %}
// Copyright 2014 BVLC and contributors.
//
// This is a simple script that allows one to quickly test a network whose
// structure is specified by text format protocol buffers, and whose parameter
// are loaded from a pre-trained network.
// Usage:
//    test_net net_proto pretrained_net_proto iterations [CPU/GPU]

#include <cuda_runtime.h>

#include <cstring>
#include <cstdlib>
#include <vector>

#include "caffe/caffe.hpp"

// for read and write image
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/highgui/highgui_c.h>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/opencv.hpp>
#include <fstream>

using namespace caffe;  // NOLINT(build/namespaces)

enum OUTPUT_MODE {DRAW, LABEL, BOTH};

int main(int argc, char** argv) {
  if (argc < 5 || argc > 8) {
    LOG(ERROR) << "test_net net_proto pretrained_net_proto iterations output_dir"
				<< " [DRAW/LABEL/BOTH]"
        << " [CPU/GPU] [Device ID]";
    return 1;
  }
  Caffe::set_phase(Caffe::TEST);
	OUTPUT_MODE mode = BOTH;
	if(argc >= 6){
		if(strcmp(argv[5], "DRAW") == 0){
			mode = DRAW;
		}else if(strcmp(argv[5], "LABEL") == 0){
			mode = LABEL;
		}
	}
  if (argc >= 7 && strcmp(argv[6], "GPU") == 0) {
    Caffe::set_mode(Caffe::GPU);
    int device_id = 0;
    if (argc == 8) {
      device_id = atoi(argv[7]);
    }
    Caffe::SetDevice(device_id);
    LOG(ERROR) << "Using GPU #" << device_id;
  } else {
    LOG(ERROR) << "Using CPU";
    Caffe::set_mode(Caffe::CPU);
  }

  Net<float> caffe_test_net(argv[1]);
  caffe_test_net.CopyTrainedLayersFrom(argv[2]);
  int total_iter = atoi(argv[3]);
	string output_dir(argv[4]);
	//creat output_dir
	string mkdir_cmd("mkdir " + output_dir);
	int found = mkdir_cmd.find("/");
	while(found > 0){// replace invalid "/" with "\" supported by system command
		mkdir_cmd.replace(found, 1, "\\");
		found = mkdir_cmd.find("/");
	}
	system(mkdir_cmd.c_str());

  LOG(ERROR) << "Running " << total_iter << " iterations.";
	vector<Blob<float>*> input_vec;
	int image_index = 0;
	const vector<shared_ptr<Layer<float> > > layers = caffe_test_net.layers();
	ImageDataLayer<float>* image_layer = reinterpret_cast<ImageDataLayer<float>*>(layers[0].get());
	CHECK(image_layer);
	int new_height = image_layer->layer_param().image_data_param().new_height();
	int new_width = image_layer->layer_param().image_data_param().new_width();

  for (int i = 0; i < total_iter; ++i) {
		caffe_test_net.Forward(input_vec);
		const shared_ptr<Blob<float> > blob_ = caffe_test_net.blob_by_name("fc_8_det");
		const float* det_points = blob_->mutable_cpu_data();
		for(int n=0; n<blob_->num(); n++){
			det_points = blob_->mutable_cpu_data() + blob_->offset(n);
			string image_path(image_layer->lines_[image_index].first);
			found = image_path.find_last_of("/\\");
			string fn(image_path.substr(found+1));
			//LOG(ERROR) << output_dir << "/" << fn << " " \
				<< det_points[0] << " " << det_points[1] << " " << det_points[2] << " " << det_points[3] << "\n";
			int x1 = det_points[0];
			int y1 = det_points[1];
			int x2 = det_points[2];
			int y2 = det_points[3];
			cv::Mat cv_img;
			cv_img = cv::imread(image_path, CV_LOAD_IMAGE_COLOR);
			CHECK(!cv_img.empty());
			if(new_height > 0 && new_width > 0){
				// rescale points
				x1 = float(x1)/new_width * cv_img.cols;
				y1 = float(y1)/new_height * cv_img.rows;
				x2 = float(x2)/new_width * cv_img.cols;
				y2 = float(y2)/new_height * cv_img.rows;
			}
			// draw boundary in image
			if(mode==BOTH || mode==DRAW){
				// write image with boundary
				line(cv_img, cv::Point(x1, y1), cv::Point(x2, y1), cv::Scalar(0, 0, 255), 3);
				line(cv_img, cv::Point(x2, y1), cv::Point(x2, y2), cv::Scalar(0, 0, 255), 3);
				line(cv_img, cv::Point(x2, y2), cv::Point(x1, y2), cv::Scalar(0, 0, 255), 3);
				line(cv_img, cv::Point(x1, y2), cv::Point(x1, y1), cv::Scalar(0, 0, 255), 3);
				// write image
				string out_path(output_dir+"/"+fn);
				CHECK_EQ(imwrite(out_path, cv_img), true) << "write image " + out_path + " failed";
			}
			if(mode==BOTH || mode==LABEL){
				// write image predict label
				std::fstream fs;
				found = fn.find(".");
				string fpath(output_dir+"/"+fn.substr(0,found)+".lbl");
				fs.open(fpath, std::ios::out);
				if(fs.is_open()){
					fs << x1 << " " << y1 << " " << x2 << " " << y2; 
					fs.close();
				}else{
					LOG(ERROR) << "Can't create " << fpath << "\n";
				}
			}

			LOG(ERROR) << "[" << image_index+1 << "/" << image_layer->lines_.size() << "]" << " " << fn << "\n";
			image_index ++;
			if(image_index >= image_layer->lines_.size()){
				LOG(ERROR) << "restart from begining\n";
				image_index = 0;
			}
		}
  }

  return 0;
}

{% endhighlight %}


[caffe]: http://caffe.berkeleyvision.org/
[mycaffecode]: http://lxiongh.com/2015/02/04/car_localization/
[caffe_win]: https://github.com/niuzhiheng/caffe
[niuzhiheng_github]: https://github.com/niuzhiheng/
[author_other_libs]: http://dl.dropboxusercontent.com/u/3466743/caffe-vs2012/dependency-20140804.7z
[my_other_libs]: http://pan.baidu.com/s/1jG5o7Am
[caffe_linux_detection]: http://lxiongh.com/2015/01/20/Caffe_Change_for_Localization/

[caffe_win_change]: http://lxiongh.qiniudn.com/blog/2015-4-23/caffe_win_change.png
[caffe_proto]: http://lxiongh.qiniudn.com/blog/2015-4-23/caffe_proto.png
[io_hpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/io_hpp.png
[io_cpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/io_cpp.png
[vision_layers_hpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/vision_layers.png
[data_layer_cpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/data_layer_cpp.png
[image_data_layer_cpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/image_data_layer_cpp.png
[memory_data_layer_cpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/memory_data_layer_cpp.png
[convert_imageset_cpp]: http://lxiongh.qiniudn.com/blog/2015-4-23/convert_imageset_cpp.png


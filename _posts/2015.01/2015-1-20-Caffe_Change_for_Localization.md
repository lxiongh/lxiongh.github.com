---
layout: post
title:  Caffe 源码的修改（用于车辆的定位）
category: code
tags: [Deep Learning, Caffe, Cuda, Ubuntu]
---

修改后的caffe源码 [https://gitcafe.com/lxiongh/Caffe_for_Multi-label][1]

Caffe 的源码仅适用于模式分类问题，其标签是一维的。为了能够将 Caffe 代码应用于车辆的检测问题上，首先要解决的问题就是将 Caffe 源码中与标签相关的代码由一维改为多维。本项目由于只需预测车辆的上下两角的坐标（x1,y1, x2,y2），即将标签修改为4维。

> 这次能够很顺利的修改源码，很大一原因是实验室的师弟太给力了，他们对 Caffe 源码的研究开始地很早，并在以 Caffe 作为其深度学习的工具，并且在也发表了一些很不错的文章。Thanks to TianShui, LiangLi, JinZhu. 『车辆定位项目链接 [http://lxiongh.github.io/2015/02/04/car_localization/][2]
』

[1]: https://gitcafe.com/lxiongh/Caffe_for_Multi-label
[2]: http://lxiongh.github.io/2015/02/04/car_localization/

首先，利用 `Understanding` 软件，可以方便的查看到 `caffe` 源码的目录结构，如下图所示。
![butterfly](http://lxiongh.qiniudn.com/blog/2015-1-20/butterfly.png)

可以注意到，在 Caffe 源码里有一个『Tools』的目录，里面有一些相当有用的工具，如『compute_image_mean.cpp』、『convert_imageset.cpp』等，其中『convert_imageset.cpp』直接操作到了文本文件，如下列代码所示。那么函数『ReadImageToDatum』将就是突破口。

{% highlight cpp linenos %}
// convert_imageset.cpp, line 129-
for (int line_id = 0; line_id < lines.size(); ++line_id) {
    if (!ReadImageToDatum(root_folder + lines[line_id].first,
        lines[line_id].second, resize_height, resize_width, is_color, &datum)) {
      continue;
    }
    // ...
}
{% endhighlight %}


![datum](http://lxiongh.qiniudn.com/blog/2015-1-20/ReadImageToDatum.png)

定义在`io.cpp`里的函数`ReadImageToDatum`完成了将图片数据转换成`caffe`能够处理的`Datum`类型，主要修改的文件大都集中在数据层，将其单标签改成多标签支持。针对车辆检测，对`caffe`所作的修改有如下的部分：

---
* `caffe.proto`

![caffe.proto](http://lxiongh.qiniudn.com/blog/2015-1-20/caffe_proto.png)
> 将`optional`改为`repeated`，使得标签变量`label`为数组，即支持多标签。否则无此属性`Datum.label_size()`。

---
* `data_layer.hpp`

![data_layer_hpp](http://lxiongh.qiniudn.com/blog/2015-1-20/data_layer_hpp.png)
> 将`lines_`由`protected`修改为`public`，使得后续能够利用指针直接访问`lines_`数据，其中保存了图片名及其对应的标签信息。详见`test_det_net.cpp`

* `data_layer.cpp`

![data_layer_1.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/data_layer_1.png)
![data_layer_2.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/data_layer_2.png)
> 修改`top_label`，使得其保存图片的多标签信息。

---
* `image_data_layer.cpp`

![image_data_layer_1.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/image_data_layer_1.png)
![image_data_layer_2.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/image_data_layer_2.png)
![image_data_layer_3.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/image_data_layer_3.png)
> 从文本文件里读取图片的路径及标签信息，将原来`int label`修改成`std::<vector> vec_label`。同时需要特别注意的就是不要忘记申请相应的存储空间`(*top)[1]->Reshape(this->...)`，否则在初始化网络时就会出现错误。

---
* `memory_data_layer.cpp`

![memory_data_layer.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/memory_data_layer.png)
> 虽然这个在实际应用中没有用到，但因其涉及到最底层的数据层，所以也修改了。

---
* `convert_imageset.cpp`
![convert_imageset.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/convert_imageset.png)
> 这个程序是将图片数据打包与数据库的形式，默认为`leveldb`

---
* `io.hpp`

![io_hpp](http://lxiongh.qiniudn.com/blog/2015-1-20/io_hpp.png)

* `io.cpp`

![io_1.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/io_1.png)
![io_1.cpp](http://lxiongh.qiniudn.com/blog/2015-1-20/io_2.png)
> 这个文件涉及到最底层的数据读写工作。


---
* `test_det_net.cpp`，由`extract_feature.cpp`修改而来的对输入的图片进行车辆预测，并画框输出
{% highlight cpp linenos %}
#include <stdio.h>  // for snprintf
#include <string>
#include <vector>

#include "boost/algorithm/string.hpp"
#include "google/protobuf/text_format.h"
#include "leveldb/db.h"
#include "leveldb/write_batch.h"

#include "caffe/blob.hpp"
#include "caffe/common.hpp"
#include "caffe/net.hpp"
#include "caffe/proto/caffe.pb.h"
#include "caffe/util/io.hpp"
#include "caffe/vision_layers.hpp"

// liu 
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/highgui/highgui_c.h>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/opencv.hpp>
#include <sys/stat.h>  // for mkdir

using namespace caffe;  // NOLINT(build/namespaces)

template<typename Dtype>
int feature_extraction_pipeline(int argc, char** argv);

int main(int argc, char** argv) {
  return feature_extraction_pipeline<float>(argc, argv);
//  return feature_extraction_pipeline<double>(argc, argv);
}

template<typename Dtype>
int feature_extraction_pipeline(int argc, char** argv) {
  ::google::InitGoogleLogging(argv[0]);
  const int num_required_args = 5;
  if (argc < num_required_args) {
    LOG(ERROR)<<
    "This program takes in a trained network and an input data layer, and then"
    " extract features of the input data produced by the net.\n"
    "Usage: test_det_net  pretrained_net_param"
    "  feature_extraction_proto_file num_mini_batches"
		"  output_dir"
    "  [CPU/GPU] [DEVICE_ID=0]\n"
    "Note: the feature blob names is fixed as 'fc_8_det' in code\n";
    return 1;
  }
  int arg_pos = num_required_args;

  arg_pos = num_required_args;
  if (argc > arg_pos && strcmp(argv[arg_pos], "GPU") == 0) {
    LOG(ERROR)<< "Using GPU";
    uint device_id = 0;
    if (argc > arg_pos + 1) {
      device_id = atoi(argv[arg_pos + 1]);
      CHECK_GE(device_id, 0);
    }
    LOG(ERROR) << "Using Device_id=" << device_id;
    Caffe::SetDevice(device_id);
    Caffe::set_mode(Caffe::GPU);
  } else {
    LOG(ERROR) << "Using CPU";
    Caffe::set_mode(Caffe::CPU);
  }
  Caffe::set_phase(Caffe::TEST);

  arg_pos = 0;  // the name of the executable
  string pretrained_binary_proto(argv[++arg_pos]);

  string feature_extraction_proto(argv[++arg_pos]);
  shared_ptr<Net<Dtype> > feature_extraction_net(
      new Net<Dtype>(feature_extraction_proto));
  feature_extraction_net->CopyTrainedLayersFrom(pretrained_binary_proto);
	// to get image_paths
	const vector<shared_ptr<Layer<float> > > layers = feature_extraction_net->layers();
	const caffe::ImageDataLayer<float> *image_layer = dynamic_cast<caffe::ImageDataLayer<float>* >(layers[0].get());
	CHECK(image_layer);
			
  const string blob_name = "fc_8_det";
  
  CHECK(feature_extraction_net->has_blob(blob_name))   \
		<< "Unknown feature blob name " << blob_name      \
		<< " in the network " << feature_extraction_proto;


  int num_mini_batches = atoi(argv[++arg_pos]);
	string output_dir = argv[++arg_pos];
	CHECK_EQ(mkdir(output_dir.c_str(),0744), 0) << "mkdir " << output_dir << " failed";

  LOG(ERROR)<< "Extracting Features";

  vector<Blob<float>*> input_vec;
  int image_index=0;
  for (int batch_index = 0; batch_index < num_mini_batches; ++batch_index) {
    feature_extraction_net->Forward(input_vec);
		
		const shared_ptr<Blob<Dtype> > feature_blob = feature_extraction_net->blob_by_name(blob_name);
	
		int batch_size = feature_blob->num();
		
		int dim_features = feature_blob->count() / batch_size;
		CHECK_EQ(dim_features, 4) << "the dim of feature is not equal to 4";
		
		Dtype* feature_blob_data;
		int x1, y1, x2, y2;
		for (int n = 0; n < batch_size; ++n) {
			feature_blob_data = feature_blob->mutable_cpu_data() + feature_blob->offset(n);

			x1 = feature_blob_data[0];
			y1 = feature_blob_data[1];
			x2 = feature_blob_data[2];
			y2 = feature_blob_data[3];
			
			string image_path	= image_layer->lines_[image_index].first;
			//LOG(ERROR) << "image_index " << image_index << " " <<  image_path   \
								 << " x1 " << feature_blob_data[0] << " y1 " << feature_blob_data[1] \
								 << " x2 " << feature_blob_data[2] << " y2 " << feature_blob_data[3];
			
			cv::Mat img_origin = cv::imread(image_path);
			
			std::vector<string> part_names;
			boost::split(part_names, image_path, boost::is_any_of("/"));
			string subname = part_names[part_names.size()-1];             // the last element is the image name.
			string out_path(output_dir + "/" + subname);
			
			//LOG(ERROR) << subname;
			line(img_origin, cv::Point(x1, y1), cv::Point(x2, y1), cv::Scalar(0, 0, 255), 3);
			line(img_origin, cv::Point(x2, y1), cv::Point(x2, y2), cv::Scalar(0, 0, 255), 3);
			line(img_origin, cv::Point(x2, y2), cv::Point(x1, y2), cv::Scalar(0, 0, 255), 3);
			line(img_origin, cv::Point(x1, y2), cv::Point(x1, y1), cv::Scalar(0, 0, 255), 3);
			CHECK_EQ(imwrite(output_dir + "/" + subname, img_origin), true) << "write image " + out_path + " failed";
			
			image_index ++ ;
			if (image_index>=image_layer->lines_.size()){
				LOG(ERROR) << "Restarting data prefetching from start.";
				image_index = 0;
			}
			// (image_index>image_layer->lines_.size()-1)?(image_index=0):(image_index++);
		}
		
  }  // for (int batch_index = 0; batch_index < num_mini_batches; ++batch_index)
  // write the last batch
  
  LOG(ERROR)<< "Successfully extracted the features!";
  return 0;  
}
{% endhighlight %}

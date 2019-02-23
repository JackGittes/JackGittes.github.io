---
title: |
      读论文：VGG-Net
category: 神经网络
author: 赵明心
excerpt: |
 本文是2014年牛津大学和DeepMind公司发表用于在ImageNet上进行分类任务的神经网络。全名《Very Deep Convolutional Networks For Large-Scale Image Recognition》
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概览
网络的配置如下所示：

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fucgwbqhw8j20js0jr404.jpg" width="700px">
</center>

VGG有多个版本，深度分别为11到19层，作者通过比较不同深度的网络在ImageNet分类任务上的表现证明了深度对于神经网络精度的影响，越深的网络精度越高。VGG延续了AlexNet使用的224×224固定大小图像输入的方式，但是舍弃了LRN层。实际上，VGG作者在11层网络里对LRN层的作用进行了对比，发现LRN对网络精度几乎没有什么影响，所以在后续更深的网络里直接拿掉了LRN(LRN会占用大量的存储和计算资源)。关于VGG对AlexNet以及其他经典工作的改进会在下面描述。

### 二、实现细节

#### 2.1 卷积核
VGG使用了更小的卷积核，在AlexNet里，第一层卷积层使用了11×11的大卷积核(现在已经基本不会使用这么大的卷积核了)，同时AlexNet的第一层卷积步长是5，这也是非常大的跨度，现在很少使用。VGG最小的卷积核为1×1，大部分为3×3，padding大小固定1个像素。最大值池化使用2×2大小的窗口，池化步长为2，这也和AlexNet提出的重叠池化方法不一样。


### 三、训练方法

#### 3.1 动量算法
VGG延续了AlexNet使用的训练方法，但是扩大了batch的大小，每个batch为256，动量因子



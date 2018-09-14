---
title: |
    SqueezeNet ShuffleNet MobileNet PeleeNet
category: 网络压缩
author: 赵明心
excerpt: |
    这节笔记总结了几个常见的针对移动和嵌入式平台设计的神经网络，分析了其中常见的设计技巧。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 紧凑型网络

紧凑型网络是针对移动平台和嵌入式设备设计的深度神经网络，它们一般都会采用一些简化技巧减少网络参数和计算量同时又不会对精度产生太大的影响。

按照紧凑型网络的影响，主要选择了SqueezeNet/ShuffleNet/MobileNet/PeleeNet来总结这类网络设计中的常用技巧。

### SqueezeNet

SqueezeNet在2017年的ICLR上被提出，可以使AlexNet级别的网络压缩到0.5MB以下。
#### 设计思想

- 使用1$\times$1卷积替代3$\times$3卷积，卷积过程的时间复杂度为$O(\frac{HW}{stride}d^2)$，其中$H$和$W$是输入feature map的长和宽，$stride$为卷积的步长，$d$为卷积窗口的宽度。一般情况下，输入feature map的尺寸和卷积步长是不能改变的，想要降低卷积的计算复杂度只能减小卷积窗口的大小。

- 减少3$$times$3卷积核的输入通道数，3$times$3卷积的参数数量是$input channels \times number of filters \times 3*3$，在卷积核尺寸和个数不变的情况下，能改变的只有输入通道数。

- 推迟下采样，下采样可以通过增大卷积步长或者引入pooling层来实现，大步长或者加入pooling会使得feature map变小。一个很直观的想法是，大特征图会带来更高的分类精度，而在网络太靠前的层使用下采样会使后面大部分特征图变小，这个想法在何恺明等人的研究中也得到了证实。之前两个策略已经减少了参数，最后这个策略则是试图在参数受限的情况下恢复精度。

#### 网络构造

SqueezeNet将这部分称为Fire Module。


### MobileNet

MobileNet是谷歌公司于2017年设计的神经网络，在发布时有多个版本，主要受到了Inception的影响。

#### 




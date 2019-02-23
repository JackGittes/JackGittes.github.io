---
title: |
    紧凑型网络设计(一)
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

按照紧凑型网络的影响，主要选择了SqueezeNet/ShuffleNet/MobileNet/PeleeNet来总结这类网络设计中的常用技巧。ShuffleNet、MobileNet已经有了相应的V2版本，V2版本将会在下一篇写。

### SqueezeNet

SqueezeNet在2017年的ICLR上被提出，可以使AlexNet级别的网络压缩到0.5MB以下。
#### 设计思想

- 使用1$\times$1卷积替代3$\times$3卷积，卷积过程的时间复杂度为$O(\frac{HW}{stride}d^2)$，其中$H$和$W$是输入feature map的长和宽，$stride$为卷积的步长，$d$为卷积窗口的宽度。一般情况下，输入feature map的尺寸和卷积步长是不能改变的，想要降低卷积的计算复杂度只能减小卷积窗口的大小。

- 减少3$\times$3卷积核的输入通道数，3$\times$3卷积的参数数量是「**输入通道数×卷积核数×3×3**」，在卷积核尺寸和个数不变的情况下，能改变的只有输入通道数。

- 推迟下采样，下采样可以通过增大卷积步长或者引入pooling层来实现，大步长或者加入pooling会使得feature map变小。一个很直观的想法是，大特征图会带来更高的分类精度，而在网络太靠前的层使用下采样会使后面大部分特征图变小，这个想法在何恺明等人的研究中也得到了证实。之前两个策略已经减少了参数，最后这个策略则是试图在参数受限的情况下恢复精度。

#### 网络构造

SqueezeNet将这部分称为Fire Module。


### MobileNet

MobileNet是谷歌公司于2017年设计的神经网络，在发布时有多个版本，主要受到了Inception的影响。

#### 设计思想

深度可分离卷积，和SqueezeNet一样，MobileNet作者也认识到了小卷积核可以减少网络参数和计算量。但是仅仅把卷积核变小肯定会降低精度，所以MobileNet在小卷积核的构造上花费了一番心思。分析过程如下，在标准的卷积计算中，计算消耗为：
$$D_K  D_K  M N  D_F  D_F$$

其中$D_K$是卷积窗口大小，$M$为输入通道数，$N$为输出通道数，$D_F$为输出feature map的尺寸。普通的滤波器组在做卷积的时候，需要对单个卷积核产生的结果进行加权求和，得到一个滤波器的特征图，一组滤波器会产生输出通道数——N个特征图。

为了实现深度可分离，同时又不改变输出通道数N，MobileNet不对单个卷积核卷积结果进行加权求和，而是使用$M$个卷积核对每个输入通道单独进行卷积，同时保留卷积结果。之后再使用$N$组深度为$M$，大小为1$\times$1的卷积核对深度可分离卷积得到的$M$个通道进行合并。

经过以上过程，把原来输入$M$通道、经过卷积和加权得到输出$N$通道的过程分解成了「**深度可分离卷积 + 1$\times$1卷积合并输出**」两个过程。后面的计算量为：
$$D_K D_K M D_F D_F + M N D_F D_F$$

### ShuffleNet
很显然，MobileNet虽然可以对参数数量和计算量进行大幅改进，但是它对标的是AlexNet这种结构简单的网络，AlexNet从输入到输出直接贯通，不存在旁支()和跨层连接(Shortcut)。旁支和跨层已经被证明是非常有效的提升网络效果的方法。ShuffleNet就考虑到了这两个设计要点。

#### 设计思想

- 深度可分离卷积(Depthwise Seperable Convolution)


- 组卷积与重排(Group Convolution and Shuffle)


- 逐点卷积(Pointwise Convolution)

### PeleeNet

#### 设计思想

- 

-

- 




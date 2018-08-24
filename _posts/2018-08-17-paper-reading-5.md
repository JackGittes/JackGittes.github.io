---
title: |
      读论文：GoogleNet
category: 神经网络
author: 赵明心
excerpt: |
 本文是谷歌公司2015年发表于CVPR的论文，目的在于构造更深但参数更少的CNN网络来解决ImageNet分类问题。全名《Going Deeper with Convolutions》，本文大量使用1×1卷积核进行中间层的降维，并且在中间层的构造上，使用多个并行操作的卷积、Pooling层来获得feature的多尺度信息。其次，为了避免网络太深而造成梯度消失，网络在中间层引入了额外的两个输出层来计算Loss，防止梯度的消失。
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
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fuhbmy1ibqj213245lauj.jpg" width="400px">
</center>

GoogleNet比较特别的地方在于，网络中存在多个并行层(这里是说同一深度存在多个卷积、pooling操作，不是指「并行计算」)，文中成为Inception。其次是为了防止梯度消失，网络的中间存在两个辅助分类器，它们的分类损失以0.3的权重系数被累积到网络的总损失上。网络中这些Inception结构，可以实现一张图像的多尺度处理，这些多尺度特征会在下一层被融合在一起，而且大量存在的1×1卷积还能控制网络参数不至于太多。


### 二、实现细节

按照论文的说法，启发GoogleNet的是Hebbian原则和多尺度处理。Hebbian原则是神经学领域的一个说法，指那些经常被激活的神经连接在使用中会变得更加强。GoogleNet基于这两点对传统的神经网络进行了架构上的改进，一方面使网络变得更宽更深，另一方面也保证了网络参数和计算量没有出现大的增长。

#### 2.1 多尺度处理

首先是多尺度处理，GoogleNet并没有像其他网络那样逐层使用卷积和pooling，而是在同一深度进行多种卷积核的卷积处理。

传统神经网络一般是先卷积再pooling，重复若干次卷积、pooling的操作，最后由全连接层输出，这样做的缺点是对图像不同尺度信息的利用程度不高。类比于图像处理中常用的图像金字塔方法，通过对一幅图像进行不同程度的下采样，可以提取原图像中多个尺度的信息。2007年Serre的工作就从灵长目动物的视觉皮质构造中获得启发，使用了多个大小不同的Gabor滤波器构造了一个2层的神经网络来处理多尺度信息。GoogleNet的滤波器参数是通过学习得到的，而非固定的。

在同一幅图像(或者网络中的同一层)同时使用多种规格的卷积和pooling操作，最直接的方法就是如下图的左边所示。但是仔细观察就可以发现，一旦这样处理，就会遇到一个很大的问题，那就是每层中的参数和运算量将大大增加。

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fujzg32vxxj21660c041p.jpg" width="700px">
</center>

伴随网络的宽度和深度增加，参数的增加不可避免，尤其是在卷积逐层级联的情况下，参数几乎以二次方的速度在增长。为了避免这个问题，一般有两种手段。一种是模仿生物系统，建立一个稀疏结构的神经网络。Arora在2013年的工作证明，如果某个数据集的概率分布可以被一个非常大的稀疏神经网络表示，那么最优的网络拓扑结构就可以通过逐层分析网络节点的激活情况来进行构造。但是从今天硬件发展的状况来看，一旦涉及到稀疏数据，计算效率一定非常低，在访存和Cache不命中上的开销将会使计算性能难以提升。


<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fujzg3vupmj20pa0jcgmb.jpg" width="400px">
</center>

#### 2.2 改进训练方法




### 三、网络训练

GoogleNet训练使用了分布式计算系统DistBelief，是基于CPU的。他们使用了异步式随机梯度下降来优化网络，动量参数为0.9，并且学习率固定，每8个epochs会减少4%的学习率。

在图片采样的方式上，Google公司财大气粗，不惜时间金钱，在漫长的训练过程中使用了多种超参数的组合，所以没有一个很明确具体的图片采样建议。不过他们也给出了一个有用的技巧，就是使用不同大小的图片patch，这些patch的大小从8%~100%呈均匀分布，并且限制长宽比为$[\frac{3}{4},\frac{4}{3}]$。同时，某些改变图片光照的方式也有助于对抗过拟合。

### 四、总结

此篇文章的最大亮点就是在尽量不增加网络参数和运算量的情况下，使用一些稀疏子结构实现了对网络深度和宽度的扩展。

其次，作者使用了引入辅助分类层的方式来防止梯度消失。
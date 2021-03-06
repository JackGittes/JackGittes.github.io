---
title: |
    压缩 · 量化 · 加速
category: 网络压缩
author: 赵明心
excerpt: |
 「压缩、量化、加速」是伴随神经网络兴起以来的一个新研究领域，大部分深度网络的参数和运算量都远远超过经典模型，这导致深度神经网络虽然精度高，但却无法被部署在移动和嵌入式设备上。对神经网络进行压缩和加速成为了一个研究热点。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概要

近些年来深度神经网络已经取得了显著的进展，在计算机视觉、自然语言处理、语音识别等领域的很多任务上，DNN的表现已经超越了人类。但是，DNN对CPU和GPU资源的消耗使得它无法被部署在嵌入式系统和移动设备上，例如无人机、智能手机、智能眼镜。这些设备的计算资源不多，而且电池容量也相当有限。

为了解决此类问题，学术界和工业界提出了很多网络压缩和加速方案。一般而言，CNN的计算复杂度主要体现在卷积层，而它的存储消耗主要来自于全连接层的大量参数。因此，多数加速方案关注减少卷积复杂度，而压缩方案则关注于压缩全连接层参数。

现今主流的压缩和加速方案如下：
- 网络剪枝 
- 低秩分解
- 网络量化
- 师生网络
- 紧凑型网络设计

本文内容主要来自自动化所的程健老师在2018年发表的综述文章《Recent Advances in Efficient Computation of
Deep Convolutional Neural Networks》。

### 二、主流的压缩与加速方案

#### 2.1 网络剪枝
按照剪枝的粒度大小，可以把剪枝分成五种，分别是「精细级、向量级、核级、组级、滤波器级」。其中：

- 精细剪枝可以剪掉任意卷积核的任意元素

- 向量级剪枝可以剪掉某个卷积核上的一行或一列

- 核级剪枝可以剪掉任意一个卷积核

- 组级剪枝可以剪掉任意一组卷积核上的对应元素

- 滤波器级剪枝可以剪掉任意一个滤波器(卷积核的组)

下图是原文中的示意图，非常失败的示意图，缺少必要的图例和注释。

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1funfce3otpj20pj0ertan.jpg" width="500px">
</center>

##### 2.1.1 精细剪枝

神经网络模拟的是神经元之间的信息处理与传递过程，真实的人类神经系统是稀疏的，也即并非每个神经元之间都能建立直接联系。因此也就产生了剪枝的思想，借助训练中或训练后的一些技巧剪掉对输出影响不大的连接，这样可以在不降低精度的情况下减少参数数量。早期的精细剪枝需要借助损失函数的二阶导数，但随着网络规模的增大，这种方法不再具有可行性。更加实用的精细剪枝，如Han S提出的，在训练后使小于某个阈值的连接权重直接变为0，通过再训练恢复精度，反复这个过程可以剪去网络全连接层的大量参数。不过这些工作还是主要关注于全连接层，对于FCN(全卷积神经网络)，这些方法不是很适用。

其次是，精细剪枝往往会产生稀疏矩阵，CPU/GPU这类计算架构对稀疏矩阵的计算效率非常低，而专门设计稀疏加速的硬件对计算效率的提升也比较有限。
##### 2.1.2 向量级和Kernel级剪枝

向量级、Kernel级、滤波器级剪枝对硬件更加友好，但是这方面的探索工作还相对较少。从示意图就可以看出来，向量级、Kernel级、滤波器级剪枝作用后的滤波器组很规整，不会带来稀疏计算的负面效果。

##### 2.1.3 组级剪枝

##### 2.1.4 滤波器级剪枝

#### 2.2 低秩分解

卷积层的卷积核是一个4维张量，张量维度对应于卷积核的宽度、高度，以及输入和输出的通道数。$4-D$的张量可以分解成$t-D,(t=1,...4)$的张量。低秩分解的目的是为了寻找一个近似张量$\hat{W}$来逼近原始张量$W$，而近似张量可以有更高的计算效率。

低秩分解法的关键不同之处在于如何排列四个维度和低秩约束是怎么规定的。以下按照滤波器所分解成的元素个数来分类。

##### 2.2.1 两元分解

两元分解，权重张量被分解成两部分，卷积层会被两个连续相接的层替代。《Speeding up
convolutional neural networks with low rank expansions》将$\omega*h$的卷积核分解成$\omega×1$和$1×h$大小的小核，实现运算量的压缩。


##### 2.2.2 三元分解

##### 2.2.3 四元分解


#### 2.3 网络量化
标量/向量量化和定点数量化是量化方法中的两大类，其中标量/向量量化往往有一个密码本来存储量化后的值。

##### 2.3.1 标量与向量量化
标量/向量量化在数据压缩领域有很久的应用历史。码本一般是量化中心值的集合。通常来讲，量化中心值的数量远少于原始数据。此外量化中心值可以被无损压缩的方法进行压缩，例如用Huffman编码或者直接用低位宽定点数。

标量量化可以使用K-means算法来压缩参数。

##### 2.3.2 权重的定点数量化

##### 2.3.3 激活值定点数量化


#### 2.4 师生网络
通过一个训练好的大网络来设计一个更加紧凑和有效的小网络。

#### 2.5 紧凑型网络

压缩和加速的一个主要目标就是为了优化深度网络的执行和存储框架，设计一个更加有效、消耗更低的网络也是一种方法。

在《Network in network》中，作者提出了一种方案，在网络架构中大量使用1×1大小的卷积核来增加网络容量，这样做也使计算复杂度很小。同时，为了减少CNN的存储需求，他们舍弃了全连接层，取而代之的是一个全局的均值池化。这些技巧也被后续一些像GoogleNet和ResNet的SOTA的网络使用。

分支(多组卷积)是另一种常被用来降低网络复杂度的方案。最早在GoogleNet中出现，后来提出的SqueezeNet在AlexNet上实现了50倍的压缩效果。借助分支策略，ResNeXt在同样计算开销的情况下达到了比ResNet更高的精度。

MobileNet规模是VGG-16的$\frac{1}{32}$，但运行速度却是VGG-16的27倍，精度与VGG-16相当。MobileNet使用了在深度上的卷积操作并且有大量1×1的卷积核，绝大多数运算也基本集中在1×1卷积运算上。

进一步降低1×1卷积复杂度的方法是使用多组卷积。ShuffleNet引入通道

### 三、硬件加速设计

为了达到高性能、轻权重、低功耗，CPU/GPU的架构就不再适用了，这篇文章也是在FPGA/ASIC的语义下讨论硬件加速设计这个问题，而且主要讨论推断阶段的问题。

#### 3.1 通用架构
一个通用的神经网络加速架构一般包括了五个部分：数据缓存、参数缓存、处理单元(PE)、全局控制器、片外转移管理。
<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fuocuwsw6ej20hi0dtwh3.jpg" width="500px">
</center>

- 数据缓存用来缓存输入图片
- 权重缓存一般用来存储卷积核
- PE是一系列基本运算单元，包括乘加、非线性运算还有其他函数，例如归一化和量化
- 全局控制器用来管理片上数据流
- 片外数据和指令的转移由管理器控制

异构计算是加速器设计中被广泛采用的方法，像密集的乘加操作可以被放在加速部件上来提高吞吐率，而像数据预处理、softmax这些操作可以放在CPU、GPU上进行。


#### 3.2 处理单元(PE)

在所有加速器设计当中，区别最大的是处理单元的设计。加速单元在设计中主要是尽可能发掘数据复用性和并行性。


#### 3.3 高吞吐量优化

在神经网络计算中出现最频繁的矩阵-矩阵乘法和矩阵-向量乘法包含很多嵌套的循环操作，合理地处理这些嵌套循环对达到高吞吐率是很重要的。

循环优化是最频繁使用的优化设计，主要包括循环展开、循环分块、循环交换等。

#### 3.4 低功耗优化

#### 3.5 设计自动化

这是一个新兴的趋势，也就是把一个神经网络经过编译器设计自动映射为RTL级代码，这样便于FPGA实现各种不同的网络。

#### 3.6 正在涌现的技术

随机计算是近年出现的技术，主要是利用在计算过程中使用随机的位宽。而在硬件设计上，基于RRAM的加速器和3D DRAM存储也正在引起更多关注。

### 四、未来发展趋势


### 五、结论

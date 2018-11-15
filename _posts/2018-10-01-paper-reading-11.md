---
title: |
    量化
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

#### Weighted-Entropy-based Quantization for Deep Neural Networks

##### 1 背景

1.现有方法缺少在输出质量和推理性能上的权衡

2.主要针对的是分类任务，在其他种类的任务上实现的较少

3.XNOR-NET和DOREDA-NET没有对网络第一层和最后一层进行量化，避免精度损失太严重，但这样做也限制了低精度带来的好处

##### 2 启发

1.网络参数的数值分布特征：钟形曲线。经过ReLU之后是大于0的实数值。

2.启发点：（1）接近0的参数占据了分布中的大部分，但是对输出的影响很小。所以给小值分配的量化台阶应该少（2）大的权重和激活值对输出的影响很显著，但出现频率非常低，所以分配的量化台阶也不会太多。（3）不属于之前两类的值应当具有更多的量化台阶，因为它们对输出的影响比较显著且数量很多。

##### 3 量化算法

1.定义加权熵函数：


##### 4 实验结果

实验针对三种任务：图像分类、检测、语言模型(LSTM)。其中图像分类在ILSVRC 2012上进行，LSTM用tensorflow实现，CNN在Caffe上实现。

图像分类的对比是AlexNet、GoogleNet和ResNet-50/101。

权重量化和激活值量化是分别进行的，作者以(x,y)来标记权重和激活值的量化位数。在AlexNet上满足1%精度衰减的约束下，实现了(3,6)(4,4)(4,5)(4,6)的量化。效果比LogQuant方法要好，同时在GoogleNet上实现了4~5 bit的权重量化和6 bit的激活值量化。作者也指出GoogleNet比AlexNet要紧凑，所以能够实现的量化效果更有限。

在ResNet的量化上，（1）本文是第一篇对深度达到50～100层的深度网络进行量化的工作。（2）实验也发现了对于权重只需要3 bit，但是对于激活值需要6 bit，作者推断对激活值使用过低的位宽量化会造成量化误差在很深的网络中不断积累。



#### INCREMENTAL NETWORK QUANTIZATION: TOWARDS LOSSLESS CNNS WITH LOW-PRECISION WEIGHTS

##### 1 算法概况

1.作者回顾了以往的神经网络量化工作，并指出先前的工作在量化的时候大多采用的是对网络权重同时进行量化。同时对权重进行量化是以往的量化方案产生精度损失的一个重要因素，他们据此提出「增量量化」方案——在训练过程中逐渐增加被量化的权重数目直至整个网络的所有权重都被量化完毕。
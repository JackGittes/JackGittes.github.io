---
title: |
      读论文：SPP Net
category: 神经网络
author: 赵明心
excerpt: |
 SPP-Net是介于RCNN和Fast RCNN之间的一个工作，由何恺明提出。本文的最大亮点是SPP(spatial pyramid pooling，空间金字塔池化)，借助这个技巧，SPP-Net可以输入任意大小的图片而不再像以前的网络一样必须限制输入图像大小。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概览

从AlexNet提出之后的类似神经网络，都对输入图片的大小进行了限制，例如AlexNet规定图片必须为224×224。这些要求看起来非常「人为」。针对这个问题，何恺明提出了空间金字塔池化策略。带SPP的网络结构如下：

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fukv05pte5j20pr0h3gmx.jpg" width="500px">
</center>
---
title: |
      读论文：RCNN
category: 神经网络
author: 赵明心
excerpt: |
 本文是用于目标检测的RCNN系列开山之作，由Ross Girshick提出，后来经改进出现了Fast R-CNN、Faster R-CNN等变体，在当年的目标检测VOC上都达到了SOTA的水平。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概览
RCNN是之后用于目标检测的RCNN系列的开山之作，文章将传统方法和CNN相结合，实现了目标检测精度的提升。

网络的配置如下所示：
<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fujm7g6kc6j20up0angqt.jpg" width="700px">
</center>

RCNN实际上是一个「多阶段」的目标检测模型，第一个阶段是使用传统方法找到若干个可能包含物体的候选框，第二阶段是使用训练好的CNN对候选框进行特征提取，最后一个阶段是使用针对不同类别训练好的SVM对CNN提取到的特征进行分类。三个阶段使用的算法都需要分开训练，因此不是「end to end」的，这也是RCNN的缺点。

### 二、实现细节

#### 2.1 Proposal的获得
借助「Selective Search」算法从输入图片中获得2000个类别独立的候选框，候选框大小不固定。因为后续的CNN需要一个固定大小的图片作为输入，文章使用了直接变换(warp)的方法，把图像变换成CNN网络要求的输入大小(也因此会有变形)，本文使用的227×227大小RGB图片。

#### 2.2 网络
文章主要使用的是AlexNet网络，除了在输出的全连接层进行了对应调整之外，没有做更多的改动。RCNN也是比较早的使用大数据训练之后迁移到小数据集上进行Fine-tune的工作，他们先在ImageNet上对网络进行了预训练，之后转移到PASCAL数据集上，改变全连接层的输出类别。


### 三、网络训练

#### 3.1 训练数据

PASCAL上训练的时候，数据需要自行构造，先用Selective Search的方法给出候选框，当候选框和Ground-truth的交并比IoU大于0.5的时候，候选框为正样本，并且归为ground-truth对应的类。当IoU＜0.5的时候，候选框为负样本，计为背景。这样一共会有N+1个类别，其中N是物体的种类，1为背景。同时，作者注意到相对于图片中的物体而言，背景占主要成分，所以每个mini batch中，正样本(物体)只有32个，而负样本(背景)有96个，mini batch大小为128。

用以上方法构造好PASCAL上的训练数据之后就可以对ImageNet上训练完成的AlexNet进行Fine-tune，启动SGD的学习率是0.001，是预训练初始学习率的$\frac{1}{10}$。

#### 3.2 SVM分类器

「困难样本发掘」策略，对于

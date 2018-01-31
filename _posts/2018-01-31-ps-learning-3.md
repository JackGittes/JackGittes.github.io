---
title: |
      摄影后期笔记（三）：HSL色彩调节与影调
category: 摄影后期
author: 赵明心
excerpt: |
  摄影后期笔记第三篇，关于如何使用Adobe Camera Raw(ACR)中的HSL色彩模式对照片中不同的色彩成分进行调节，同时讲解了关于影调的基本理论。因为自己不是美术和摄影相关专业，我在记这节课的笔记之前还补充写了一部分色彩的基础知识，内容来自于李涛老师的《简单摄影后期高高手》视频教程。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---
### 概要

### 本节需要用到的一些基本色彩知识

#### 原色空间与补色空间

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fnzpehwuemj211d0j8q4b.jpg" width="400px">
</center>

#### 色温、色相与色调
色温在维基百科中的定义为：The color temperature of a light source is the temperature of an ideal black-body radiator that radiates light of a color comparable to that of the light source.也即，光源的色温是与光源发出同样光辐射的理想黑体所具有的温度，所以色温的单位是“开尔文(K)”。但是黑体在低温时发出偏红光，而高温时包含了更多偏蓝紫色的光，这和人们对于色彩温度的直觉感受正好相反，因此在ACR中虽然沿用了以开尔文标度的色温，但是将蓝紫色设置为低色温，偏黄色为高色温。


<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fnzra9qojhj21100n8b29.jpg" width="600px">
</center>

色相即色彩的名称，如红橙黄绿青蓝紫，都是色相。色彩按照波长在可见光范围内是连续变化的，对连续的色彩定义每种颜色的名字显然是不可能的，但我们有原色/补色原理。根据原色按照不同比例的混合(色光)，或者补色的不同比例混合(印刷)，我们可以获得各种其他的颜色，所以在ACR和Photoshop中调节色相的时候往往只有比较典型的几种颜色，下面就是ACR中HSL模式里可调节的几种色相。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fnzrgg1d2bj206a095mxj.jpg" width="300px">
</center>

### HSL色彩调节

### 影调
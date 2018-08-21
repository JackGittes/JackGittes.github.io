---
title: |
      SIFT尺度不变性特征
category: 图像处理
author: 赵明心
excerpt: |
 SIFT是一种对旋转、尺度缩放、亮度变化均不敏感的图像特征，对视角变化、仿射变换、噪声也具有一定的稳定性，在图像特征的提取中非常常见。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概述
我们希望一个好的图像特征可以：对抗尺度缩放(对图像大小不敏感)、对旋转不敏感、对亮度不敏感、抗噪声。从SIFT特征的构造步骤就可以看到，

SIFT算法主要包括了四个步骤：
- 构造尺度空间
- 寻找DoG空间特征点
- 求特征点主方向
- 生成特征描述子

以下按照这四个步骤叙述SIFT算法的细节。

### 二、构造尺度空间
为了实现尺度伸缩的不变性，SIFT算法需要借助图像金字塔来生成不同尺度所对应的高斯金字塔。高斯金字塔尺度并非按照像素的大小来分，而是按照对应高斯核的方差大小。对高斯分布$N(\mu,k\sigma_0),(k=1,2,3...)$来说，$\sigma_0$是一个基准方差，乘以不同的$k$值即对应不同的高斯尺度。相邻的$k$就是相邻的高斯尺度，在高斯尺度空间中，相邻层相减得到差分高斯空间(DoG space)。差分高斯空间是我们下面继续求特征点的基础。

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fuh89v4q2fj21830m1tbp.jpg" width="700px">
</center>

形象一点理解，图像金字塔代表了图像的尺度缩放，而对图像金字塔的每一层生成高斯尺度空间，本质是在获得大小不同且模糊程度不同的图像。同时为了求得特征，还需要一个类似求梯度(或者Hessian)的过程，这一步就用了图像差分的办法，通过相邻高斯尺度相减来获得包含特征的空间。至于为什么可以用相邻高斯尺度相减来近似替代求Hessian，可以参考Marr and Hidreth的论文。

### 三、寻找DoG空间特征点
DoG构造完成之后，开始寻找特征点，这个过程可以概括为：
- 在同一组DoG的$[3×3×3]$大小邻域求极值点
- 删除极值点中对比度较低和边缘响应点

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fuh84nopdbj20jd0g8gma.jpg" width="400px">
</center>

### 四、求特征点主方向



### 五、生成特征描述子
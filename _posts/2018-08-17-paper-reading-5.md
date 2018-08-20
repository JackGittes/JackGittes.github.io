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
网络的配置如下所示，由于网络太大太深了，da
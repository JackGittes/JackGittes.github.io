---
title: 读代码：用Tensorflow对神经网络进行剪枝
category: 网络压缩
author: 赵明心
excerpt: |
  现有的剪枝方案的实现基本都要依赖于一定的深度学习框架，caffe由于出现较早，功能也相对完善，所以基于caffe的剪枝代码有很多。Tensorflow作为现在最流行的深度学习框架，因为出现较晚，还缺少这方面的代码积累。本文选取了Github上一个用tensorflow对进行minist手写数字识别神经网络剪枝的代码来分析用TF进行剪枝该如何实现。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---
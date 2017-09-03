---
title: 网络压缩论文：Exploiting Sparsity in Recurrent Neural Networks
category: 网络压缩
author: 赵明心
excerpt: |
  本文是ICLR 2017的一篇已公开发表会议论文，主要介绍的是一个专门针对递归神经网络的稀疏化模型压缩方法。相比其他几个人工智能领域的顶会，ICLR对神经网络压缩方面的文章有偏爱，剪枝、Dropout、量化等各种压缩方法的论文都有收录，现在选取这篇来简要地解读一下。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

　　这篇论文非常地简洁，对实现方法的介绍仅有两页，但从效果来看还是不错的。在只对递归层使用剪枝稀疏化方法后，就可以去掉网络90%的参数，如果能继续配合其他的压缩方法，应该可以达到更高的压缩率。

### 1 论文主要工作
- 提出了一种在训练时对递归层进行剪枝的手段
- 在略微损失推理精度的情况下，实现了90%的稀疏性
- 根据以上稀疏化方法，提出了一种通过使用更大规模稠密模型进行训练然后再稀疏化的提高模型精度的方法

### 2 创新点
- 主要针对递归神经网络的递归层
- 使用了单调递增的阈值函数用来不断剪去网络权重
- 将训练时的迭代次数映射为阈值大小来实现对权重矩阵稀疏度的控制
- 只修剪递归层，对其他参数较少的层不做修剪

### 3 压缩方法细节
　　这个方法包含了6个超参数，需要在实际训练过程当中通过试错才能知道取什么值比较好。这六个超参数是：

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcgy1fj5ogu9vujj213n0iswhr.jpg" width="800px">
</center>
阈值是由这些超参数计算出来的。

#### 3.1 初始化权重和mask
　　权重初始化方法和单纯的训练没什么区别，只是要在每个权重上增加一个mask，来为后面的剪枝做准备，每个mask的初始值都是1，相当于不抑制任何权重。在优化器进行参数更新的步骤，每个权重都和mask相乘。mask会根据权重更新之后与阈值的比较结果来进行更新，那些小于阈值的权重所对应的mask会被设为0，这样就实现了权重剪枝。

#### 3.2 权重更新和mask更新

算法按照下面这个流程进行：
<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcgy1fj5p9yruycj21670m1gol.jpg" width="800px">
</center>
阈值按照下面这个公式进行更新：
<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcgy1fj5pf6kq3rj20ra03d3yu.jpg" width="600px">
</center>

这个公式看起来是经验公式，至少论文里没有给出公式是怎么推出来或者猜出来的。从直觉上来看，如果想让阈值和训练时的迭代次数成正相关的话


### 4 实现效果与文章结论


### 5 总结
　　这个方法是对以前常见的剪枝方法的一种改进，之前的训练过程剪枝方法一般用一个确定的阈值修剪小权重，论文的方法可以在训练过程中动态增加修剪阈值，实现更好的稀疏化效果。

　　但是，因为稀疏化是在训练过程中实现的,加上中间多了阈值和mask的更新过程，猜测相比“pretrain + prune + retrain”方法难免耗时要长。而且从头训练进行剪枝，是不是能适用到大部分现有的RNN上面，还是有些疑问。

---

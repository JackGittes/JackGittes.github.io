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

#### 3.2 阈值更新

算法按照下面这个流程进行：
<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcgy1fj5p9yruycj21670m1gol.jpg" width="800px">
</center>
当中的$\theta$按照下面这个公式计算：
<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcgy1fj5pf6kq3rj20ra03d3yu.jpg" width="600px">
</center>
阈值计算是一个关于当前迭代次数$current\\_ itr$的分段函数，小于$ramp\\_ itr$的时候采用第一种更新，大于$ramp\\_ itr$的时候采用第二种更新。很明显，$\epsilon$的两个更新公式是随当前迭代次数$current\\_ itr$增加而单调递增的。为了更直观地看到阈值是怎么更新的，我画了一下在训练过程中的阈值变化情况。

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcgy1fj6n2cjbdej20wb0kxtah.jpg" width="700px">
</center>

可以看到在$current\\_ itr$小于$ramp\\_ itr$的时候，阈值增长比较缓慢，当$current\\_ itr$大于$ramp\\_ itr$的时候，阈值增长变快了。

#### 3.3 权重和mask更新
　　根据算法，阈值在当前迭代次数整除freq参数的情况下会发生更新，随后mask也会发生变化，那些小于阈值的权重对应的mask会变成0。但是，这个mask只是在前向传播的时候会用到。按照论文的说法，反向传播的时候mask不起作用，也就是网络按照所有权重都没有被修剪的情况进行误差反传和权重更新。这样会出现一些在前向传播过程中被修剪掉的权重，因为反传的更新而再次大于阈值从而重新回到前向传播过程的情况。


### 4 实现效果与文章结论
　　论文用提出的这个算法在BRNN和GRU上做了实验，在BRNN上实现了92%的稀疏度，GRU上也达到了类似效果，但是两种稀疏化模型的精度都略有下降。文章最后利用cuDNN和cuSPARSE库对稀疏模型在Nvidia TitanX GPU上进行了计算，发现RNN稀疏模型可以加速3.5x ~ 1.15x，GRU稀疏模型可以加速7x ~ 3.5x。
### 5 总结
　　文章提出的这个方法是对常规剪枝方法的一种改进，以前的训练过程剪枝方法一般用一个确定的阈值修剪小权重，论文的方法可以在训练过程中动态增加修剪阈值，实现更好的稀疏化效果。

　　但是，因为稀疏化是在训练过程中实现的,加上中间多了阈值和mask的更新过程，猜测相比“pretrain + prune + retrain”方法难免耗时要长。而且从头训练进行剪枝，是不是能适用到大部分现有的RNN上面，还是有些疑问。

---

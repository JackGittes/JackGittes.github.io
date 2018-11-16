---
title: |
      Adaptive Inference
category: 神经网络
author: 赵明心
excerpt: |
 训练好的神经网络具有固定的结构，但这种固定结构是否是必须的呢？ECCV 2018上有两篇文章对这个问题进行了研究。他们使用了不同的方法在推理阶段动态改变网络结构，这一方面减少了推理过程的计算量，另一方面也因为这种类似正则化或者Dropout的操作提高了模型的泛化能力。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概览
神经网络在某种程度上可以看做是级联模型，级联模型在图像处理当中的一大好处就是可以在开始阶段过滤掉大量与推理结果无关的“负样本”而只保留有用信息。但目前的DNN在训练完成之后，网络结构就固定了，无论是什么样的图像输入，都要经过这些处理。从直观上看，这似乎也并不符合人类处理视觉信息的方式，ECCV 2018的两篇文章就希望解决这个问题——是否可以动态改变网络结构来避免不必要的计算。

之前关于神经网络的研究，证实了越靠近输入的层提取到的信息越为低级，而大部分图像处理任务是可以根据某些低级信息(例如边缘、角、颜色)等过滤掉很多无需处理的“负样本”的。这也就说明了，并不是每幅输入图像都需要走完从输入层到输出层之间的所有中间处理，如果我们能根据前一层的信息来决定下一层往何处跳转，将给推理带来很大的灵活性。

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fxa4sc9f4ij213o05pabw.jpg" width="700px">
</center>

### 二、细节

这两篇文章分别是《SkipNet:Learning Dynamic Routing in Convolutional Networks》和《Convolutional Networks with Adaptive Inference Graphs》。

#### 2.1 SkipNet

SkipNet把根据前一层的处理结果来动态决定下一层往何处跳转的问题归结为一个决策过程，这样的决策过程是不可导的，所以没有办法使用类似梯度下降之类的方法求解。作者转而求助于强化学习(RL)，RL可以从"探索-利用"当中学得一个最优策略，这样的策略优化过程正好适用于层间跳转的优化。

但这其中还有一个问题，那就是如何有效地利用前一层的输出信息。作者提出了三种方案，①是在ResNet的每个残差模块后连接一个又maxpooling、conv、fc组成的微型网络，这个微型网络能输出一个介于[0,1]区间的值，把这个值和预设阈值(本文为0.5)进行比较，决定是否跳过下一个残差模块。显然，这样做虽然能有效提取前一层的输出信息，但是却引入了更多的计算量。②为此作者还提出了第二种方案，这个方案类似于方案①，只不过计算量更小。③第三种方案完全不同于①、②，作者想借助LSTM单元来直接处理前一层的信息，这样的话只会额外引入0.04%的计算量，是最为节省的方案。

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fxa4saceo2j213w09ft9z.jpg" width="700px">
</center>

最后作者使用三种方案结合RL对策略进行了学习，最终的实验结果显示使用了LSTM的方案在更少的计算代价下获得了很好的性能。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fxa4s87w1fj20sl0ckwgg.jpg" width="500px">
</center>

#### 2.2 ConvNet-AIG


<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fxa69a6579g20a00670vb.gif" width="500px">
</center>

### 三、总结

实现自适应有很多种方法，但一般都需要利用输出信息，然后根据输出信息来进行动态调节以实现自适应。在电路系统当中，有时候通过把输出信号引回输入端也可以实现反馈作用下的自适应。而在神经网络里，每一层的输出信息都是堆叠在一起的张量特征图，不管是对这些特征图做简单的加法或是其他运算，似乎都至少是O(n)级别复杂度。也就是说，自适应调节层与层之间的跳转关系并非不可以，但是如何更加高效地利用前一层的输出信息会是一个关键。

### 四、参考文献

[1] Wang X, Yu F, Dou Z Y, et al. Skipnet: Learning dynamic routing in convolutional networks[J]. arXiv preprint arXiv:1711.09485, 2017.

[2] Belongie A V S. Convolutional Networks with Adaptive Inference Graphs[J].

---
title: |
      读论文：AlexNet
category: 神经网络
author: 赵明心
excerpt: |
 本文是2012年发表的在ImageNet上进行分类的神经网络。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 一、概览

AlexNet网络结构：
- 八层网络
- 五层卷积、三层全连接

主要的配置和结构示意图在下面：

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fubvrp5x0bj20s508rab4.jpg" width="700px">
</center>

### 二、网络性能提升的因素
文章总结了能使AlexNet精度、训练速度以及泛化能力得到提升的主要因素，以下按重要性列举：

#### 2.1 ReLU 非线性激活
Alex发现ReLU激活函数比普通的饱和型激活函数(Tanh，Sigmoid)收敛速度更快，在CIFAR-10数据集上测试的时候，达到同样的精度和收敛程度，ReLU要快6倍左右。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fucg9cgl08j20mc0ho3yr.jpg" width="400px">
</center>

这里作者使用了一个技巧，在训练大型神经网络的时候，可以预先在小一些的数据集上进行测试，然后再转移到大的数据集上，这样可以更快速方便地寻找合适的网络配置方案。

#### 2.2 多GPU训练
文章使用了两块GPU进行训练，这里不再描述，现在使用GPU训练已经是训练神经网络所必须的了。

#### 2.3 LRN(局部响应归一化)
这是Hinton提出的用来模拟某个生物神经元对周边神经元产生抑制效果的数学模型(未考证不深究，只解释其数学意义)，公式如下：

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fucg55ylbsj20oe05cq3a.jpg" width="400px">
</center>

这个公式看似比较复杂，但只要弄清中间的求和过程就很容易理解LRN是什么操作。求和指标范围max(0,i-n/2)到min(N-1,i+n/2)，式中N代表kernel map总数，例如前一层是只有1个channel的图像，使用了30个卷积核对前一层进行卷积，那么对应这一层就包含了30个channel，每个卷积核生成一个channel，也就是kernel map共30个。此时对第10个kernel求它在map坐标(7,7)处的LRN，并且规定n=4，那么求和范围就是max(0,10-4/2)=8到min(30-1,10+2)=12，也即需要对8/9/10/11/12 kernel生成channel的值进行平方加权求和。这里的超参数n相当于求和范围的半径，可以理解为局部响应归一的「局部化」范围，而包含min和max就是担心在边界处局部化的范围越界，需要在左右边界进行截断。

<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcgy1fucne0cn2jj21cv0mrwfr.jpg" width="600px">
</center>

为了形象一点理解，这里做一个实验，先生成一个长度为20的一维数组，然后计算局部半径为4的情况下这个数组对应的LRN，超参数$\alpha$取0.0001，$\beta$取0.75，$K$取2，把原始数据和LRN后的数据画成图如下：

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fucg55luz2j20mv09ot97.jpg" width="600px">
</center>

从这个一位数组的LRN可以看出来，似乎LRN只是对数据进行了某种程度的归一化使它们之间的差异缩小了，不知道这背后有没有更加深刻的数学原理和影响。不过在2015年的VGG论文里，VGG作者重新对LRN进行了一番测试，并指出LRN其实对网络的精度没有实质增强，所以至于LRN到底有什么效果，仍然是有争议的问题。

#### 2.4 重叠池化
当池化步长小于池化窗口大小的时候是重叠池化，文章发现重叠池化可以略微降低网络的错误率(Top-1大概降低0.4%，Top-5大概0.3%)，他们使用的池化窗口大小为3，步长为2。

#### 2.5 防止过拟合 —— 数据增强
使用运行在CPU上的Python代码来对图片数据进行变换处理，这个过程和GPU上进行的训练过程是同步的，GPU在使用上一批次处理好的图片进行训练的同时，CPU在对新一批的图片数据进行增强处理，所以整个数据增强不会造成太大的运算负担。图片变换操作主要包含了两种：

##### 2.5.1 图片翻转
文章使用了224×224大小的图像块来替代整个图像，每张图像会被随机裁切成这些大小的图片块，同时也包括水平镜像翻转的图像块，通过增加这些数据可以有效防止过拟合。

##### 2.5.2 PCA处理


#### 2.6 防止过拟合 —— Dropout
Dropout是一种训练时的技巧，在训练过程中，随机丢弃某个特定比例的神经元，而在推断阶段则使用全部神经元进行预测。主要是为了防止过拟合，但神经网络本身是非线性模型，不符合线性叠加原理，为什么能够在训练时随机丢弃而在推断时又全部使用仍然保持精度，原因还没有调研。

### 三、其他细节

#### 3.1 训练方法
batch大小为128，动量因子0.9，权重衰减系数0.0005。文章发现这个权重衰减系数对于模型学习非常重要。

动量算法的更新规则为：

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcgy1fucpgfgeh1j20z80dtadj.jpg" width="600px">
</center>

文章对每一层的权重使用高斯分布$N(0,0.01)$进行初始化，同时对2、4、5卷积层和全连接层的偏置bias进行了常数为1的初始化，对其他层的bias使用了常数为0的初始化。

本文尚且遗留的一些疑点是：
- LRN的依据是什么，真的对CNN有实质影响吗？
- 非线性系统的dropout操作有什么依据？
- 重叠池化以及PCA增强对泛化和精度改善有什么解释？

### 四、总结

本文提出的AlexNet结构从深度上来看仍然是一个不太深的神经网络，但是作者使用了多种改进方法(网络结构上的和数据处理上的)，并且利用GPU加速了网络的训练速度，使得它能在ImageNet那么大的数据集上实现收敛，精度上相比之前的传统方法有了大幅度提升，可以说对后续深度网络的快速发展有很大的启发作用。
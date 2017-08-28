---
title: NeuralTalk —— 一个面向图像标注的神经网络模型的算法分析
category: 神经网络
author: 赵明心
excerpt: |
  NeuralTalk是由斯坦福大学视觉实验室的Andrej Karpathy在2014年提出的一个神经网络模型，主要是面向图像标注任务，作用是输入一幅图像，模型输出一句对该图像的自然语言描述。这是一个典型的同时也是比较早期的“Encoder+Decoder”混合结构神经网络，对于理解之后更复杂的混合网络很有意义。
## feature_text: |
  ## NeuralTalk - 一个面向图像标注的神经网络模型的算法分析
 # The modern pot still is a descendant of the alembic, an earlier distillation device
## feature_image: "http://wx2.sinaimg.cn/large/41f56ddcgy1fiza7xeq59j21gh0paahk.jpg"
#image: "https://unsplash.it/1200/400?image=1048"
---

### 0. 文章内容：
- 算法简述
- 部分源代码分析
- 小结
- 参考文献

### 1. 算法简述
#### 1.1 模型效果
NeuralTalk模型是面向图像标注的“卷积神经网络+递归神经网络”模型，前后两个神经网络之间通过直接连接形成编码译码结构，来实现从图片特征到自然语言的转换。效果如图：

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fizay53m0rj21jo0v4wrh.jpg" width="800px">
</center>

从效果上来看是不错的，图片的文字说明和图片内容高度匹配。还有一些基于NeuralTalk的硬件实现开发了NeuralTalk在现实场景中的应用，例如文章[2]。

#### 1.2 训练集和测试标准 
NeuralTalk是在Flickr8k，Flickr30k和MSCOCO数据集上训练的，其中Flickr8k包含8000张图片，Flickr30k包含33000张图片，而MSCOCO包含3000000张图片。这三个数据集中的每张图片都包含了与之相对应的五句话描述，描述是由人完成的，五句描述各有侧重，一般是采用了不同语法结构或者是着眼于图片的不同部分。

模型最终输出是自然语言，因此它的评判标准是Perplexity(困惑度指标)，这个指标通过衡量一个语言算法在输出前一词语后对于紧跟着要输出的词语的自信程度来判断语言算法的好坏。简言之，如果语言算法越确定后一个词该输出哪一个，那么Perplexity越低，反之越高。所以，衡量NeuralTalk输出效果好坏主要是看困惑度高低，越高则效果越差。
#### 1.3 

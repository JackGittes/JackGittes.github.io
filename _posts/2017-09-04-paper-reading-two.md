---
title: 网络压缩论文：On Compressing Deep Models by Low Rank and Sparse Decomposition
category: 网络压缩
author: 赵明心
excerpt: |
  这篇文章要总结的是CVPR 2017的一篇论文，是剪枝稀疏化、低秩分解、参数量化这三大神经网络压缩流派里面的低秩分解派。有趣的是，通讯作者IEEE Fellow 陶大程2000年前后在中科大指导学生写过一篇短文《DNA序列分类的数学模型》，前几年我在做数学建模比赛的时候曾经读过。没想到现在看ICLR的论文又遇到了陶老师指导的文章，真是有意思。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---
### 文章简介
　　这篇CVPR论文利用了低秩分解和稀疏化的结合，但是这里的稀疏化并不是剪枝实现的稀疏化，而是借助于一个优化问题实现的。


---
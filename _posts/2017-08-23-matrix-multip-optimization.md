---
title: 总结：稀疏矩阵向量乘法的硬件加速
category: 硬件加速
author: 赵明心
excerpt: |
  稀疏矩阵是很多计算模型中经常遇到的一类矩阵。稀疏矩阵和向量相乘时的不规则访存操作，导致了计算过程中的低效，为了提升稀疏矩阵向量乘法的计算效率，已经有了各种各样的方案来进行优化。写这篇文章就是为了总结一下一些常用的优化稀疏矩阵向量乘的方法。
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

　　稀疏矩阵是很多计算模型中都会遇到的一类特殊矩阵，一般是指矩阵中非零元素个数远远小于零元素个数的矩阵。而稀疏矩阵的规模一般也都很大，包含几百万到上亿不等的矩阵元，直接使用普通的按行或按列存储格式会带来很大的存储开销和浪费。因此通常会对稀疏矩阵进行压缩，只保留其非零元并以一定的格式记录其坐标，来减少稀疏矩阵的存储空间。不过这样做会带来在计算过程中访存不规则的负面效果，所以对稀疏矩阵向量乘法(Sparse matrix-vector multiplication，下面简称SpMV)进行加速，经常要从矩阵分割、编码、解码、硬件乘法模块等多个方面共同设计才能实现更好地加速效果。

　　一个通用的SpMV设计流程如图所示：

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fix32x5nvqj218a0oeabu.jpg" width="800px">
</center>
其中灰色方框为稀疏矩阵预处理过程，稀疏矩阵按照特定的方法进行分割，分割目的是使每个矩阵分块的存储能尽量和芯片上的存储空间和计算资源相匹配，避免一个分块的处理过程中频繁访问片外存储。分块后的稀疏矩阵经过编码来压缩存储空间，之后被送入片上进行解码，矩阵元素值根据向量的乘法需求被访问，运算结束以后输出结果。

　　一个直观的表示像这样：
<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fix3dzztetj21gt08qq49.jpg" width="800px">
</center>

　　有了稀疏矩阵向量乘的计算流程，那么SpMV的大致优化方向就可以总结出来了，大概就从四个方向：
- 矩阵分割和遍历方法
- 压缩编码格式
- 负载平衡问题
- 乘法累加器(MAC)设计

　　下面就按照这四个方向逐一展开，做简要的说明：




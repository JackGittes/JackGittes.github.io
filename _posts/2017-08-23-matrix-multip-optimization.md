---
title: 总结：稀疏矩阵向量乘法的硬件加速
category: 硬件加速
author: 赵明心
excerpt: |
  稀疏矩阵是很多大型计算模型中经常遇到的一类矩阵。稀疏矩阵和向量相乘时的不规则访存操作，导致了计算过程中的低效，为了提升稀疏矩阵向量乘法的计算效率，已经有了各种各样的方案来进行优化。写这篇文章就是为了总结一下一些常用的优化稀疏矩阵向量乘的方法。
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

稀疏矩阵

{% include icon.html id="wechat" color="#36465D" %} wwwwww

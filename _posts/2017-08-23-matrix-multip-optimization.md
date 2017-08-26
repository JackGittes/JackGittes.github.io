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

### 简述
　　稀疏矩阵是很多计算模型中都会遇到的一类特殊矩阵，一般是指矩阵中非零元素个数远远小于零元素个数的矩阵。而稀疏矩阵的规模一般也都很大，包含几百万到上亿不等的矩阵元，直接使用普通的按行或按列存储格式会带来很大的存储开销和浪费。因此通常会对稀疏矩阵进行压缩，只保留其非零元并以一定的格式记录其坐标，来减少稀疏矩阵的存储空间。不过这样做会带来在计算过程中访存不规则的负面效果，所以对稀疏矩阵向量乘法(Sparse matrix-vector multiplication，下面简称SpMV)进行加速，经常要从矩阵分割、编码、解码、硬件乘法模块等多个方面共同设计才能实现更好地加速效果。

### SpMV的瓶颈分析和优化流程
　　稠密矩阵片上运算过程的访存是非常规则的，只需按行或按列对存储进行遍历，然后依次和向量元素进行乘加操作即可。稀疏矩阵一经压缩，矩阵元的位置便不再规则，根据向量元素去寻找对应相乘的矩阵元的过程也变得不规律。已经有了很多方案去解决SpMV的这些问题。

　　一个通用的SpMV设计流程如图所示[1]：
<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fix32x5nvqj218a0oeabu.jpg" width="800px">
</center>
其中灰色方框为稀疏矩阵预处理过程，稀疏矩阵按照特定的方法进行分割，分割目的是使每个矩阵分块的存储能尽量和芯片上的存储空间和计算资源相匹配，避免一个分块的处理过程中频繁访问片外存储。分块后的稀疏矩阵经过编码来压缩存储空间，之后被送入片上进行解码，矩阵元素值根据向量的乘法需求被访问，运算结束以后输出结果。

　　更加直观的表示像这样：
<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fix3dzztetj21gt08qq49.jpg" width="800px">
</center>

　　有了稀疏矩阵向量乘的计算流程，那么SpMV的大致优化方向就可以总结出来了，大概就这四个方向：
- 矩阵分割和遍历方法
- 压缩编码
- 负载平衡问题
- 乘法累加器(MAC)设计

　　下面就按照这四个方向逐一展开，做简要的说明。
#### 优化方向(1)：矩阵分割和遍历方法
　　矩阵分割的目的一是可以使矩阵变小，实现对每个小矩阵的一次性片上处理，减少外存访问，提高存储复用率。同时，分割后的矩阵块可以分别送入各个处理单元，提高计算的并行度。另一方面，矩阵的分割方式也决定了预处理时的最佳编码格式和计算时的遍历方式，影响计算效率。

　　常见的矩阵分割方式包括：
- 单行或单列分割
- 块状分割
- 间接方法(矩阵重排序)

##### 单行或单列分割
  

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fix60g3hihj21ck0f240g.jpg" width="800px">
</center>
　　单行分割即每一行作为一块，和向量进行乘加得到一个结果，这是最普通常见的方式。单列分割是将矩阵$A_{mn}$分成n列，第$i$列的所有元素和第$i$个向量元相乘，这样得到$n$个相乘之后的列向量，把这些列向量各个维度对应相加就得到了最终结果，这个方法的好处是

　　但是，单列、单行分割也有明显的缺点，分块的大小取决于矩阵的形状，不能跟片上存储空间和计算资源很好地匹配。为了使分割后的矩阵块不依赖于输入的矩阵形状，需要对分块方式进行改进。

##### 块状分割

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fix6t99jz1j214b0bx3zh.jpg" width="800px">
</center>
　　使用块状分割通常是根据后端存储空间以及乘法累加器的数量来决定前端矩阵分块的形状，常见有行分块、列分块、矩形分块等方法。行分块和列分块与单行、单列分割有相似之处，只不过是把若干行(列)拼接在一起，然后建立块索引来匹配后面的硬件资源。矩形分块则稍微麻烦，因为矩形分块遇到的矩形块维度不能被矩阵维度整除的情况更复杂，所以需要在矩阵边界进行填0来使矩阵恰好能被矩形块分割。

　　一旦分割完成，就要对每个块建立索引，例如按照每个分块第一个元素的坐标来记录各个块的位置作为索引，之后就可以对块内元素进行压缩存储。计算时，根据向量元素的访问需求，先定位到块，再在块中寻找对应的矩阵元素。

##### 间接方法
　　以上两种都是直接对矩阵进行了分割，但是考虑到直接分割往往是按照固定规律进行的，无法充分利用矩阵内非零元的空间分布特征。所以又有人提出了间接方法，即预先调整矩阵的行次序或者列次序，使矩阵内非零元分布更加集中，进而对矩阵进行分割。其思路如下图所示：

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fix6zwappdj216n0gxtal.jpg" width="800px">
</center>
　　不过在稀疏矩阵中寻找稠密块是一个NP问题，一般要依赖于启发式算法，这样就导致了更大的预处理开销。一定意义上相当于把计算过程的负担转嫁到了预处理过程上。

#### 优化方向(2)：压缩编码
　　矩阵压缩的好处不用多说，主要是为了减少稀疏矩阵的存储需求，避免不必要地对零元素的操作，提高计算效率。一般对矩阵的压缩包含了以下几个方面：
- 存储格式上进行压缩
- 对元素索引进行压缩
- 元素值压缩

##### 存储格式压缩
　　这个应该是大家比较熟悉的，一般本科的数据结构课上都会着重地将怎么对稀疏矩阵存储格式进行压缩，最常见的就是COO、CSR、CSS、ELL以及它们的变体了。为了详细地说明这几种格式和它们一些变体的具体实现，下面给出一张图，介绍各种存储压缩格式的异同：

##### 对索引进行压缩

##### 值压缩
　　值压缩更像是一种取巧的手段，这个方法要利用矩阵元素的取值特性。例如，矩阵中有10000个元素，且均为32位浮点数，但是这10000个元素只能取有限个分立值，例如16个值。那么实际上没有必要将每个元素都存成32位浮点数，我们其实可以给元素编个号，编号对应于它所取的16种取值中的一个。16种编号需要4位二进制数来表示，而编号对应的16个实际取值则可以存入查找表，当需要计算的时候再去查找表中取，矩阵只存储编号。这样的话，存储需求就变成了原来的1/8。

　　这种方法过度依赖于矩阵元素的取值范围，所以适用范围不是很广。但是，在现在十分流行的深度学习算法中，神经元之间的连接权重具有很强的抗噪声特性。这使得在不影响神经网络精度的情况下，可以对权重矩阵进行取值近似，把矩阵元素的取值范围约束成十几种甚至几种，进而再做压缩存储，可以大大减小神经网络的参数存储需求。

#### 优化方向(3)：负载平衡问题
　　当我们把矩阵分割和压缩以后，剩下的就是把矩阵送到片上的各个计算单元里面进行计算了。当然，我们也看到了前面的优化过程导致每个矩阵块的运算量并不能保证完全一致，这就造成了最终的SpMV处理时间是由计算最慢的计算单元所决定的。在负载极度不平衡的情况下，负载最重的单元会严重拖慢整个计算过程，所以负载平衡是不能忽视的问题。针对这个问题，按照处理方式，大概可以分成两种：
- 动态负载调整法
- 静态负载调整法

#### 优化方向(4)：乘法累加器(MAC)设计
　　MAC设计也是SpMV的重点之一
### 总结
　　可以看到，所列举的这些常见优化方法都存在着或多或少的不足，因此根据实际计算任务中的矩阵特点以及需要部署的硬件平台，SpMV计算需要进行有针对性的设计才能实现更好地加速效果。而像超级计算机这种面向大型计算任务的硬件平台更需要设计出能适合各种不同特性SpMV的优化方案。
### 参考文献
[1] Kevin R. Townsend, Computing SpMV on FPGAs[D] , 2016

[2] Dorrance R, Ren F, Markovic D, et al. A scalable sparse matrix-vector multiplication kernel for energy-efficient sparse-blas on FPGAs[C]. field programmable gate arrays, 2014: 161-170.

[3] Zhuo L, Prasanna V K. Sparse Matrix-Vector multiplication on FPGAs[C]. field programmable gate arrays, 2005: 63-74.

[4] 邬贵明.FPGA矩阵计算并行算法与结构[D].长沙:国防科学技术大学,2011.

[5] Kestur S, Davis J D, Chung E S, et al. Towards a Universal FPGA Matrix-Vector Multiplication Architecture[C]. field programmable custom computing machines, 2012: 9-16

[6] Han S, Mao H, Dally W J, et al. Deep Compression: Compressing Deep Neural Networks with Pruning, Trained Quantization and Huffman Coding[J]., 2015.

[7] Kourtis K, Goumas G, Koziris N, et al. Improving the Performance of Multithreaded Sparse Matrix-Vector Multiplication Using Index and Value Compression[C]. international conference on parallel processing, 2008: 511-519.

[8] Lee S, Eigenmann R. Adaptive runtime tuning of parallel sparse matrix-vector multiplication on distributed memory systems[C]. international conference on supercomputing, 2008: 195-204

[9] Shah M, Patel V J. An Efficient Sparse Matrix Multiplication for Skewed Matrix on GPU[C]. high performance computing, 2012: 1301-1306.




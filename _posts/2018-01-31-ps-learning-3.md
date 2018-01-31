---
title: |
      摄影后期笔记（三）：HSL色彩调节与影调
category: 摄影后期
author: 赵明心
excerpt: |
  摄影后期笔记第三篇，关于如何使用Adobe Camera Raw(ACR)中的HSL色彩模式对照片中不同的色彩成分进行调节，同时讲解了关于影调的基本理论。因为自己不是美术和摄影相关专业，我在记这节课的笔记之前还补充写了一部分色彩的基础知识，内容来自于李涛老师的《简单摄影后期高高手》视频教程。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---
### 前情回顾

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fnztsk34yqj20ri0xi4qp.jpg" width="500px">
</center>

### 概要
本节内容还是来自于李涛老师的《简单摄影后期高高手》视频课程，主要是关于HSL色彩调节和影调，此外我还补充了一些色彩知识。
- 色彩预备知识
- HSL色彩调节
- 影调
- 总结


### 一、色彩预备知识

#### 1.原色空间与补色空间
原色空间是RGB(红绿蓝)，补色空间是CMY(青品黄)，原色和补色大多数人都比较清楚，但是知道这些对于片子调色有什么用呢？实际上明白了原色、补色之后，想要控制某种色彩在片子中的比重就会变得很容易。
<center>
<img src="http://wx1.sinaimg.cn/large/41f56ddcly1fnzpehwuemj211d0j8q4b.jpg" width="400px">
</center>
先举个例子，在灰霾天气下拍摄的天空不够蓝，为了使片子里的天空看起来更蓝，我们需要怎么做呢？根据原色和补色的原理，蓝色的补色是黄色，黄色与蓝色在色光模式下混合会变成白色(在纯度较低的时候就是灰色)，那么为了突出蓝色，就需要在HSL模式中提高蓝色分量的同时，压低黄色分量，下图是在灰霾天下的国科大后山。

<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fnzsu2qnu4j21580q7npa.jpg" width="600px">
</center>
如果还需要更加精细地局部色彩调节，只能进入Photoshop中借助蒙版等工具实现。

#### 2.色温、色相与色调
色温在维基百科中的定义为：The color temperature of a light source is the temperature of an ideal black-body radiator that radiates light of a color comparable to that of the light source.也即，光源的色温是与光源发出同样光辐射的理想黑体所具有的温度，所以色温的单位是“开尔文(K)”。但是黑体在低温时发出偏红光，而高温时包含了更多偏蓝紫色的光，这和人们对于色彩温度的直觉感受正好相反，而且用黑体辐射这种非常物理化的方式来记忆色温并不能对调色产生多少帮助。因此ACR虽然沿用了以开尔文来标度的色温，但是将蓝紫色设置为低色温，偏黄色设为高色温，其色温变化范围是2000K~50000K。下图以国科大的学院二为例。

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fnzra9qojhj21100n8b29.jpg" width="600px">
</center>

色相即色彩的名称，如红橙黄绿青蓝紫，都是色相。色彩按照波长在可见光范围内是连续变化的，对连续的色彩定义每种颜色的名字显然是不可能的。但我们有原色/补色原理，原色(色光)或者补色(印刷)按照不同比例混合，我们可以获得各种其他的颜色，所以在ACR和Photoshop中调节色相的时候往往只有比较典型的几种颜色，下面就是ACR中HSL模式里可调节的几种色相。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fnzrgg1d2bj206a095mxj.jpg" width="150px">
</center>

### 二、HSL色彩调节
实际上，在前面介绍色彩知识的时候已经把HSL所能调节的部分给列举了。HSL是一种色彩模式(空间)，其中H(Hue)表示色相，S(Saturation)表示饱和度，L(Lighteness)表示明度。ACR的HSL调节中给了八种色相，分别是“红、橙、黄、绿、浅绿(青)、蓝、紫、洋红”，可以根据片子的色彩倾向，分别调节这几种色相的“色相值、饱和度、明度”。当直接调节每个色相的色相值时，颜色会发生偏移而转变成其他颜色，而调节“饱和度、明度”只会影响该种颜色的纯度和亮度，不会使一种颜色变成另一种颜色。以夜景下的国科大钟楼为例：


<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fo002kdhmkj20zf0q9qlh.jpg" width="500px">
</center>

但是ACR的调节也是有限的，例如无法实现红色变绿色，这种大跨度的色相修改需要在Photoshop中才能完成。


### 三、影调

影调是建立在风光摄影大师安塞尔·亚当斯提出的“影调分区理论”基础上，他把从白(明)到黑(暗)的灰阶划分成了11个区，每个区的定义按照下图所示。

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fnzxnwv0wuj215s0mmwg0.jpg" width="600px">
</center>

尽管这个分区是针对黑白片而言的，但同样也适用于有色彩的片子。按照这个分区法，当一张照片使用了三段和三段以内的分区时，称为短调片；使用了其中的五段分区时称为中调片；当使用了10个或以上分区时则称为长调片。同样，根据片子主要色彩的分布位置，又可分为低调片、中调片和高调片。这样两两组合形成了九种影调，外加一个全长调，构成了十种影调，如下图：
<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fnzymafk0fj20ph0sbdhx.jpg" width="500px">
</center>

关于影调，有很多大师级的摄影作品可以效仿，不同摄影师往往有各自的影调风格，这个也是需要通过逐渐训练才能形成的。就一般初学者而言，大部分片子属于中长调，也即片子当中有少许暗部和少许亮部，剩下绝大部分细节在亮度居中的区域，从直方图来看就是直方图居中，并且延伸到左右两侧。但根据统计，收藏级作品的影调以低长调和低中调居多，下面是两个例子。低调和短调片子对初学者来说很难控制，所以初学后期还是应当从中长调做起。

<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fnzyz0994fj21b40kl7qg.jpg" width="600px">
</center>
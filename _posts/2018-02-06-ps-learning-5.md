---
title: |
      摄影后期笔记（五）：风光摄影后期(一)
category: 摄影后期
author: 赵明心
excerpt: |
  从本节开始，我要写关于风光摄影的后期笔记，风光摄影后期的内容主要来自于国外摄影师Elia Locardi的风光摄影教程(在Youtube和B站上都有，未翻译无字幕)。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---
开始本节的笔记之前，先回顾和对比了一下各种后期方法的流程与差异，方便以后根据不同的需求使用合适的后期方法。美图软件法主要针对手机拍摄的jpg(JPEG)格式照片，后几种方式适用于JPEG和单反相机的RAW格式。Nikon的NEF、Canon的CR2、Sony的ARW、哈苏的DNG都属于RAW格式数码底片，其中DNG格式是Adobe公司推出的目前最优质的RAW格式数字底片，哈苏采用的就是DNG格式。
<center>
<img src="http://wx2.sinaimg.cn/large/41f56ddcly1fo614uk7bfj216l0h1gnf.jpg" width="600px">
</center>

### 一、概要
本节是关于风光摄影后期的学习笔记，内容来自国外摄影师Elia Locardi的风光摄影教程。这个视频在Youtube和B站上都有，但是没有翻译和字幕，不过因为他的讲解实在是非常详尽，所以我自己担当字幕组来记录这几节的内容。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fo76ky7axjj217j0fd7wh.jpg" width="600px">
</center>

本节是一些基本的Photoshop后期调节，主要包括：
- Lightroom基本调整
- Nik Collection插件调整
- 用高反差保留进行锐化与轮廓增强


### 二、Lightroom基本调整
Lightroom调整部分跟前几节笔记的内容差别不大，不过这里是针对风光片来说的，风光片的层次感很重要，所以一定要把曝光、高光、阴影、黑色、白色调节好，使图像充分展现出层次感。还要特别注意的是，一定要使用RAW格式数字底片，不能使用JPEG格式文件，JPEG文件损失了很多信息，其宽容度相比RAW格式要差很多。还有Elia Locardi强调了在PS中做锐化的时候，要把Lightroom中的锐化选项降到最低。

在拍摄照片的前期，曝光不足比曝光过度要好，低于正常曝光1到2挡可以在Lightroom中很容易把细节恢复出来，而曝光过度则很难再恢复正常曝光。所以拍照的时候可以在确定正常曝光的情况下，减曝一档。

### 三、Nik Collection插件
Nik Collection插件是一款集合了多种后期工具的Photoshop插件，现在已经是免费发行的了，不过需要单独下载和安装。我下载以后安装了一下，发现一个问题，如果在安装Nik Collection的时候直接设置语言为中文，之后再在PS中修改Nik的语言选项会导致Nik插件和PS一起崩溃，所以建议安装的时候选择语言为英文(主要是因为很多专有名词的汉语翻译不够统一，用英文可以消除歧义)。

<center>
<img src="http://wx4.sinaimg.cn/large/41f56ddcly1fo76eu2suij210q0klhbx.jpg
" width="600px">
</center>

Nik Collection包含了七个实用的后期工具，在风光片后期过程中，Elia推荐使用其中的Color Efex Pro。使用这个插件，可以很方便地调节色彩对比度和对比度，使风光片的层次感加强。

### 四、用高反差保留进行锐化和轮廓加强
跟在Lightroom中进行轮廓加强和锐化不同的是，在PS中进行轮廓加强可以使用锐化滤镜，也可以使用高反差保留(High Pass)。Elia在这里推荐使用高反差保留工具。首先复制原图层，然后在复制后的图层上使用高反差保留，建议高反差保留的半径在3个像素左右。在高反差保留的预览框中可以看到轮廓线的情况，当觉得轮廓线已经比较清晰的时候就可以使用了。之后，需要把通过高反差保留得到的灰色图层用“叠加”模式罩在原始图层之上，这样原片的细节就被大大加强了，质感也得到了很好的提升。


<center>
<img src="http://wx3.sinaimg.cn/large/41f56ddcly1fo75vihloij20y80tob29.jpg" width="600px">
</center>

如果不希望所有的部分都有那么强烈的质感和锐度，比如柔化的水流不需要锐化，可以对高反差保留图层使用图层蒙版，对蒙版填充黑色，之后把希望进行细节加强的部分用画笔涂出来，这样就实现了有选择性的锐化和轮廓加强。可能到这里也就明白了为什么Elia不希望在Lightroom中直接使用锐化，因为LR中的锐化是全局的，尽管有蒙版选项，但是也很难控制到一个特定的局部。而用高反差保留法进行锐化更加有选择性，也更加精准。



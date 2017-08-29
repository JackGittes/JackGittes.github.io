---
title: Github+Jekyll个人博客搭建的一些小问题
category: 文章写作
author: 赵明心
excerpt: |
  在Github上用Jekyll搭建个人博客并不难，Fork别人的主题也很方便，但是Jekyll的本地部署和对别人设计好的主题进行个性化定制还是会有一些麻烦，这里就讲讲我在设置过程中遇到的一些小问题和解决方法。
use_math: true
#feature_text: |
  ## The Pot Still
#  The modern pot still is a descendant of the alembic, an earlier distillation device
#feature_image: "https://unsplash.it/1200/400?image=1048"
#image: "https://unsplash.it/1200/400?image=1048"
---

　　用Jekyll在Github上搭建个人主页方便快捷，Github同时也支持一些其他平台，不过我最后还是选的Jekyll来做个人主页。

　　但是我在Fork别人的主题之后，进行改造还遇到了些困难。主要问题有三个：修改网站图标；插入代码、代码高亮和标注行号；插入公式。

　　我使用的主题模板当中，网站坐标是SVG文件，SVG文件本身包含了一些显示时的信息，由于之前没有SVG和一些前端设计的基础，所以在修改网站图标上踩到了第一个坑。

　　后来发现了一个解决方法，可以在Adobe Illustrator里面先画好矢量图标并保存为SVG格式，然后用文本文件格式打开，把原模板中SVG图标也用文本文件格式打开，把模板中SVG的viewPoint以及width，height等信息复制过去就可以实现在原图标位置替换成自己设计的标志。

　　第二个是代码高亮问题，首先在_config.yml文件中添加：
{% highlight ruby %}
markdown: kramdown
highlighter: ROUGE
{% endhighlight %}
这样可以使用kramdown作为markdown的编辑器，并且启用高亮渲染插件ROUGE，但接下来的问题是，当使用了参数linenos来添加行号之后，和模板自带的换行设置冲突。换行是在每一行末尾，代码超出了代码框宽度后会自动换到下一行。但是开启行号时，不仅代码会换行，行号也出现混乱。为了解决这个问题，我修改了原模板的换行设置，在CSS样式文件的pre和code标签下，设置：
{% highlight css linenos%}
pre {
    font-family:unquote(map-get($monospacetype, font-family));
    background:  $codeBackgroundColour;
    line-height: 1.2rem;
    white-space: pre;
    overflow-x: auto;
    overflow-y: hidden;
    word-break: inherit;
    word-wrap: inherit;
  }
code {
    @include fontsize(eta, all);
    line-height: 1.2rem;
  }
{% endhighlight %}
其中的6、7、8、9行主要实现在词发生溢出时不进行换行，overflow-x:auto则是在水平方向添加滚动条，来使溢出部分的代码可以通过滚动条看到，overflow-y:hidden则是隐藏竖直方向的滚动条。

其次是在_syntax.scss文件中添加：
{% highlight ruby %}
.highlight .lineno { 
  color: #3D3C3C; 
  padding:0.7rem;
  margin-left: -0.2rem;
  border-right:1px solid #F9F1E5;
  margin-top: -0.2rem;
  margin-bottom: -0.2rem;
  background-color: rgb(234, 236, 235); 
  font-family: "Courier";
}
{% endhighlight %}
对加入行号的边框进行设计，使得行号部分颜色能和代码主体区分开。
这样就实现了在能够正常看到所有代码又不打破窗口布局的情况下给代码添加行号。

最后一个问题是插入公式，由于markdown本身不支持latex公式的插入方法，所以需要用mathjax来处理公式。首先在_include文件夹下新建文件mathjax.html，里面写上：
{% highlight html %}
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
      TeX: {
        equationNumbers: {
          autoNumber: "AMS"
        }
      },
      tex2jax: {
        inlineMath: [ ['$','$'] ],
        displayMath: [ ['$$','$$'] ],
        processEscapes: true
      }
    });
  </script>
  <script type="text/javascript"
          src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
  </script>
{% endhighlight %}
之后再将mathjax.html这个文件include到_layouts\default.html中。

{% highlight html %}{

\{ % if page.use_math % \}
	\{% include mathjax_support.html % \}
\{% endif %\}

}
{% endhighlight %}
这样就可以用latex语法编写公式，并在网页中生成了。

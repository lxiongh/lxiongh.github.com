---
layout: post
title:  Markdown与Latex公式的完美结合
category : latex
tagline: 
tags : [MathJax, Latex]
---

Markdown是一个简洁方便的博客写作语言，但如果要写一篇拥有较多公式的博客时，Markdown此时就犯难了，因为Markdown原生并不支持latex公式。如果将latex公式一个个的转换为图片，然后再上传到博客，虽能解决问题，但这并非是一个好方法，公式多的话相当耗时耗力，也费存储空间。[MathJax](http://www.mathjax.org/)的出现解决了这个问题，只要在博客里插下相应的代码，就可以在线解释latex语法的公式，相当方便 (类似与Google提供的语法高亮，之前的博文也有提到)。

MathJax的安装
-----

在博客的模板文件里(i.e. `_layouts/default.html`)插入代码

    <script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

插入的上面那段代码，如果有`</head>`节点，就应该在`</head>`前，没有就放在文件的末尾。其中`TeX-AMS-MML_HTMLorMML`为配置文件，详细的配置信息可参考[Loading and Configuring MathJax](http://docs.mathjax.org/en/latest/configuration.html)。

MathJax的使用
-------

在markdown需要输入latex公式的地方插入如下的代码，

    <script type="math/tex">x+\sqrt{1-x^2}</script>

<script type="math/tex">x+\sqrt{1-x^2}</script>

或者

    <script type="math/tex; mode=display">
      \sum_{n=1}^\infty {1\over n^2} = {\pi^2\over 6}
    </script>

<script type="math/tex; mode=display">
  \sum_{n=1}^\infty {1\over n^2} = {\pi^2\over 6}
</script>

更加详细的说明，可以参见[The MathJax Processing Model](http://docs.mathjax.org/en/latest/model.html)
    




    


---
layout: post
title:  latex图片并排
category : latex
tagline:  
tags : [latex, image]
---


在[Markdown](http://lxiongh.github.io/github/2013/05/09/markdown/)语法中，图片不能并排插入，也不能调整大小。要想实现图片的并排，并插入latex语句，再用[pandoc](http://lxiongh.github.io/linux/2013/05/10/Pandoc/)文件格式转换工具将md格式转换为目标格式（如pdf），此外还需在latex的模板中加入包`\usepackage{graphicx}`, `\usepackage{subfigure}`

    \begin{figure}[H]\centering
    \includegraphics[width=.25\textwidth]{fig1.jpg}
    \includegraphics[width=.25\textwidth]{fig3.jpg}
    \includegraphics[width=.25\textwidth]{fig2.jpg}
    \end{figure}

但是这样设置之后，用pandoc将md档转换为pdf档时会出现错误

    error at ...
    \def\maxwidth{\ifdim\Gin@nat@width>\linewidth\linewidth
    \else\Gin@nat@width\fi}
    \makeatother
    \let\Oldincludegraphics\includegraphics
    \renewcommand{\includegraphics}[1]{\Oldincludegraphics[width=\maxwidth]{#1}}

如果删除最后一行，错误消失，但是图片无法根据边宽自动缩小。

    \renewcommand{\includegraphics}[1]{\Oldincludegraphics[width=\maxwidth]{#1}}

完美解决方案是，注释最后一行，添加相应代码。

     删除 \renewcommand{\includegraphics}[1]{\Oldincludegraphics[width=\maxwidth]{#1}}
     添加 
     {
     \catcode`\@=11\relax
     \gdef\includegraphics{\@ifnextchar[{\Oldincludegraphics}{\Oldincludegraphics[width=\maxwidth]}}
     }
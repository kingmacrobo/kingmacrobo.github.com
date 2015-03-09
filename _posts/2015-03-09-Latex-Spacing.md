---
layout: post
category: Latex
title: Latex中加入空格
tagline: by hombo
tags: [Latex]
---

Latex中的空格符" "在编译的过程中会被忽略，在做自己简历的过程中，遇到了两种需要加空格的情况：一种是在句子中间加空格，另一种是在行首加空格，下面记录一下这两种加空格的方法。

<!--more-->

###句子中的空格

方法很简单，就是用"\ "来表示一个空格，需要多少空格就加多少个"\ "，需要注意的是连续的两个‘\’表示的是回车换行（“\\”），下面举一个代码例子：

	\cvitem{2013.9--2016.7}{\ \ \ 中国科学院\ \ \ \ \ \ \ 计算技术研究所\ \ \ \ \ \ \ \ \ \ \ \ \ 硕士} 
	\cvitem{2009.9--2013.7}{\ \ \ 华中科技大学\ \ \ \ 计算机科学与技术学院\ \ \  \ 学士}


###行首的空格

行首不能直接用上面的方法来加入空格，因为也会被编译器忽略掉，在行首加入空格的方式是用`\hspace`，举个列子：

	\cvitem{2013.9--2016.7}{\ \ \ 中国科学院\ \ \ \ \ \ \ 计算技术研究所\ \ \ \ \ \ \ \ \ \ \ \ \ 硕士} 
	\cvitem{2009.9--2013.7}{\ \ \ 华中科技大学\ \ \ \ 计算机科学与技术学院\ \ \  \ 学士}
	\hspace{19ex}{GPA:  3.91\ \ \ \ \ \ \ \ \ 年级排名:  4/350} 

###其它加空格的方法

下面是一些加空格的方法的总结：

	\documentclass{article}
	\begin{document}
	
	\noindent
	There are a number of horizontal spacing macros for LaTeX:
	
	\noindent\begin{tabular}{lp{5cm}}
	  \verb|a\,b| & a\,b \\
	  \verb|$a\,b$| & $a\,b$ \\
	  \verb|a\thinspace b| & a\thinspace b \\
	  \verb|$a\thinspace b$| & $a\thinspace b$ \\
	  \verb|$a\!b$| & $a\!b$ \\
	  \verb|$a\mkern-\thinmuskip b$| & $a\mkern-\thinmuskip b$ \\
	  \verb|$a\>b$| & $a\>b$ \\
	  \verb|$a\mkern\medmuskip b$| & $a\mkern\medmuskip b$ \\
	  \verb|$a\;b$| & $a\;b$ \\
	  \verb|$a\mkern\thickmuskip b$| & $a\mkern\thickmuskip b$ \\
	  \verb|$a\:b$| & $a\:b$ \\
	  \verb|$a\mkern\medmuskip b$| & $a\mkern\medmuskip b$ \\
	  \verb|a\enspace b| & a\enspace b \\
	  \verb|$a\enspace b$| & $a\enspace b$ \\
	  \verb|a\quad b| & a\quad b \\
	  \verb|$a\quad b$| & $a\quad b$ \\
	  \verb|a\qquad b| & a\qquad b \\
	  \verb|$a\qquad b$| & $a\qquad b$ \\
	  \verb|a\hskip 1em b| & a\hskip 1em b \\
	  \verb|$a\hskip 1em b$| & $a\hskip 1em b$ \\
	  \verb|a\kern 1pc b| & a\kern 1pc b \\
	  \verb|$a\kern 1pc b$| & $a\kern 1pc b$ \\
	  \verb|a\hspace{35pt}b| & a\hspace{35pt}b \\
	  \verb|$a\hspace{35pt}b$| & $a\hspace{35pt}b$ \\
	  \verb|axyzb| & axyzb \\
	  \verb|a\hphantom{xyz}b| & a\hphantom{xyz}b \\
	  \verb|$axyzb$| & $axyzb$ \\
	  \verb|$a\hphantom{xyz}b$| & $a\hphantom{xyz}b$ \\
	  \verb|a\ b| & a\ b \\
	  \verb|$a\ b$| & $a\ b$ \\
	  \verb|a~b| & a~b \\
	  \verb|$a~b$| & $a~b$ \\
	  \verb|a\hfill b| & a\hfill b \\
	  \verb|$a\hfill b$| & $a\hfill b$
	\end{tabular}
	\end{document}
	
结果如下图：
![](http://i.stack.imgur.com/BQKUF.png)














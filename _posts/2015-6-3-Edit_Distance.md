---
layout: post
category: Algorithm
title: 编辑距离(Edit Distance)
tagline: by hombo
tags: [leetcode,algorithm]
---

编辑距离（Edit Distance），又称Levenshtein距离，是指两个字串之间，由一个转成另一个所需的最少编辑操作次数。许可的编辑操作包括将一个字符替换成另一个字符，插入一个字符，删除一个字符。

例如将kitten一字转成sitting：

	sitten （k→s）
	sittin （e→i）
	sitting （→g）

俄罗斯科学家Vladimir Levenshtein在1965年提出这个概念。

那么，给定两个word，如何快速的计算所两者的编辑距离呢？


<!--more-->

###动态规划求解编辑距离

利用动态规划可以将编辑距离问题快速求解，那么怎样去表示动态规划的状态呢？

首先我们可以从一个表格入手，竖着的表示word1，横着的表示word2：


	 	0	f	a	i	l	i	n	g
	0	 	 	 	 	 	 	 	 
	s	 	 	 	 	 	 	 	 
	a	 	 	 	 	 	 	 	 
	i	 	 	 	 	 	 	 	 
	l	 	 	 	 	 	 	 	 
	n	
	
这个表格中的每个点可以表示一种状态，例如点(i,j) 对应的是：`word1的前i个字符组成的字串` 和 `word2的前j个字符组成的字串` ，

如果我们将edit(i,j)表示这种状态的最优值，也就是这个两个字串的最小编辑次数，即编辑距离， 那么，我们就可以得出以下这些递推关系：

	当i = 0, j = 0时，edit(0,0) = 0, 表示什么两个空字符串的编辑距离为0.
	当i = 0, j > 0时，edit(0,j) = j, 表示i所在的字符串为空，而j所在的字符串长度为j，因此编辑距离是j。
	当i > 0, j = 0时，edit(i,0) = j, 表示j所在的字符串为空，而j所在的字符串长度为i，因此编辑距离是i。
	当i > 0, j > 0时，edit(i,j) = min{ edit(i-1,j)+1, edit(i,j-1)+1, edit(i-1,j-1)+val }, 其中，当word1[i] == word2[j]时，val = 0, 否则val = 1
	(从上下来，表明多了一个字符，就需要在上面的最优值上再加一，表明需要一次插入操作，从左过来也一样；从左上过来，两个字符串同时多了一个字符，如果多的这个字符相同，
     那当前最优值就和左上一样，否则就需要加一，表明需要一次替换操作)
     
一个例子如下：

		0	f	a	i	l	i	n	g
	0	0	1	2	3	4	5	6	7
	s	1	1	2	3	4	5	6	7
	a	2	2	1	2	3	4	5	6
	i	3	3	2	1	2	3	4	5
	l	4	4	3	2	1	2	3	4
	n	5	5	4	3	2	2	2	3
     
有了上面详细的递推关系，我们可以很容易的写出动态规划求解编辑距离的代码，这里引用leetcode上的对Edit Distance的解法，时间和空间复杂度都是O(mn)：

	// Edit Distance, DP solution
	// edit(i,j) means the minimum distance of substring (1,i) of word1 and substring (1,j) of word2.
	// the recurse formula is :
	//    edit(0,0) = 0
	//    edit(0,j) = j
	//    edit(i,0) = i
	//    edit(i,j) = min{edit(i-1,j)+1, edit(i,j-1)+1, edit(i-1,j-1) + value}, i >=1, j>=1, 
	//    if word.charAt(i) == word.charAt(j), value = 0, or value = 1.
	// Time and space are both O(n^2).
	
	public class Solution {
	    public int minDistance(String word1, String word2) {
	        int n = word1.length(), m = word2.length();
	        int[][] edit = new int[n+1][m+1];
	        edit[0][0] = 0;
	        for (int i = 1; i <= n; ++i) {
	            edit[i][0] = i;
	        }
	        for (int i = 1; i <= m; ++i) {
	            edit[0][i] = i;
	        }
	        for (int i = 1; i <= n; ++i)
	            for (int j = 1; j <= m; ++j) {
	                int up_left = Math.min(edit[i-1][j],edit[i][j-1]) + 1;
	                int diagonal = edit[i-1][j-1];
	                if (word1.charAt(i-1) != word2.charAt(j-1))
	                    diagonal++;
	                edit[i][j] = Math.min(up_left,diagonal);
	            }
	        return edit[n][m];
	    }
	}
	 	 	 	 	 	 	 
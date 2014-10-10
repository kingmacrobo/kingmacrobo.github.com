---
layout: post
category: leetcode
title: Leetcode Unique Binary Search Trees
tagline: by hombo
tags: [leetcode,algorithm, c++,dp]
---
这道题是一道关于BST树的问题，也就是二叉排序树，题目的意思是给出n个结点，每个结点的值从1到n，问这n个结点组成的二叉排序树有多少种？题目其实不难，用dp可以在O(n^n)下解决，但这里有一个更快的的方法——卡特兰数（Catalan number）。

<!--more-->

##原文题目：##

Given n, how many structurally unique BST's (binary search trees) that store values 1...n?

For example,

Given n = 3, there are a total of 5 unique BST's.

    1         3     3      2      1
    \       /     /      / \      \
    3     2     1      1   3      2
    /     /       \                 \
    2     1         2                 3

##分析:##

这道题是一道组合问题，如果测试数据足够强，复杂度不能超过O(n^n)才能AC，如果采用模拟二叉排序树的建立过程，即采用递归的方法将会超时。

如果我们仔细观察规律，可以发现这个问题可以用dp来解，假设现在n=0,n=1,n=2,....,n=k的解已经求出来，那么对于k+1来说，它的解是什么？我们可以想一下二叉排序数的性质，一个结点左边的儿子分支肯定都小于这个结点，右边的儿子分支肯定都大于这个结点，我们可以把n=k+1的结果看作下面这样组成：

    1         2          3          4                k         k+1
    \        / \        / \        / \     .....   /   \      /
    2~k+1   1  3~k+1  1~2 4~k+1  1~3 5~k+1      1~k-1  k+1  1~k
    
我们可以把儿子子树看成一个新的相同的问题，如果儿子子树有m个结点，那这个子树的组合情况就是n=m的解，由此，我们可以得到一个递推公式：

dp[n] = Σ dp[i-1]*dp[n-i]  (i = 1,2,3,....,n)

为了能让递推顺利，我们假设dp[0] = 1 .

如果上面的解递推公式理解了，那就可以很容易的写出代码：

	class Solution {
	public:
	    int numTrees(int n) {
	        vector<int> dp(n+1,0);
	        if(n<=1) return n;
	        dp[0] = 1;
	        for(int i = 1 ; i <= n ; ++i){
	            for(int j = 1 ; j <=i ; ++j) dp[i] += dp[j-1]*dp[i-j];
	        }
	        return dp[n];
	    }
	};
	
从代码中可以看出时间复杂度是O(n^n),由于用到了vector<int>来存储dp，空间复杂度就是O(n)；

如果到这里就认为这是最佳解法，你就错了！其实还有O(n)的解法！！那就是用[卡特兰数](http://en.wikipedia.org/wiki/Catalan_number)的变换公式。

我这里简单介绍一下卡特兰数：

卡特兰数是满足下面递推公式的数：

![](http://upload.wikimedia.org/math/2/f/1/2f17435a71394ce667ab694b27341560.png)

也可以用下面的递推公式表示：

![](http://upload.wikimedia.org/math/8/a/4/8a49332e4a46b3a2c7accec81160f5e3.png)

第一个递推公式和我们这道题的dp的递推一模一样，而第二个是卡特兰数的变换递推公式，如果我们用这个公式，显然更简单，而且可以在O(n)时间和O(1)空间下解决问题。代码很简单，就是用第二个递推公式，从n＝1开始，求到n。代码如下：

	class Solution {
	public:
	    int numTrees(int n) {
	        long long ans = 1;
	        for(int i = 1; i <= n; ++ i)
	            ans  = ans * 2 * (2 * i - 1) / (i + 1);
	        return (int) ans;
	    }
	};


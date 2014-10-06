---
layout: post
category: leetcode
title: Sum Root to Leaf Numbers
tagline: by hombo
tags: [algorithm,leetcode,c++,tree]
---
leetcode上比较简单的一道题了，一个二叉树，结点用0～9的数表示，让你求所有从根到叶的连起来的数的加和。

<!--more-->

##原文题目：##

Given a binary tree containing digits from `0-9` only, each `root-to-leaf` path could represent a number.

An example is the root-to-leaf path `1->2->3` which represents the number `123`.

Find the total sum of all `root-to-leaf` numbers.

For example,

	    1
	   / \
	  2   3

The root-to-leaf path `1->2` represents the number `12`.

The root-to-leaf path `1->3` represents the number `13`.

Return the `sum = 12 + 13 = 25`.

##分析：##

要想得到全部的root到leaf的值的总和，我们需要用一个全局的变量sum来保存加和的结果，并且需要父结点向儿子结点传递当前`前缀值`，做到这两点，用一次DFS就行了，只需要对树进行一次遍历即可，如果遍历到叶结点，就将这条路径值加到sum。

AC的代码如下：

	/**
	 * Definition for binary tree
	 * struct TreeNode {
	 *     int val;
	 *     TreeNode *left;
	 *     TreeNode *right;
	 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
	 * };
	 */
	class Solution {
	public:
	    int sum;
	    int sumNumbers(TreeNode *root) {
	        sum = 0;
	        if(root == NULL) return 0;
	        travel(root,0);
	        return sum;
	    }
	    void travel(TreeNode *node,int pre){
	        if(node->left == NULL && node->right == NULL)
	            sum += pre*10+node->val;
	        else{
	            if(node->left != NULL) travel(node->left,pre*10+node->val);
	            if(node->right != NULL) travel(node->right,pre*10+node->val);
	        }
	    }
	};



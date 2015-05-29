---
layout: post
category: leetcode
title: Maximum Subarray
tagline: by hombo
tags: [leetcode,algorithm,dp]
---

Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the array [−2,1,−3,4,−1,2,1,−5,4],

the contiguous subarray [4,−1,2,1] has the largest sum = 6.

非常经典的一道题--连续字段和。

<!--more-->

###解法一：ONE PASS

用一个`sum`保存当前连续的字段和，如果当前访问的值是一个`小于零`的数，我们就`当前的字段和sum`和最优值`max`进行比较, 进行一次最优值的更新。如果当前`sum+当前值`小于零，就没有必要更新sum值了，直接令`sum=0`， 也就是说前面连续的字段和已经小于零，给后面的过程增加不了`收益`，反之，如果`sum+当前值 >= 0`，就可以更新`sum = sum + 当前值`。

代码如下：

	// pass once 
	// We should be careful with the case when all the elements are negatives.
	// Time is O(n), space is O(1).
	public class Solution {
	    public int maxSubArray(int[] nums) {
	        int max = Integer.MIN_VALUE;
	        int sum = 0, biggest = Integer.MIN_VALUE, count = 0;;
	        for (int i = 0; i < nums.length; ++i) {
	            if (nums[i] < 0) {
	                max = Math.max(max,sum);
	                biggest = Math.max(nums[i],biggest);
	                count++;
	            }
	            sum += nums[i];
	            if (sum < 0) {
	                sum = 0;
	            }
	        }
	        if (count == nums.length)
	            return biggest;
	        return Math.max(max,sum);
	    }
	}
	
上面的代码还可以简化，但是需要在每个点都判断和更新max值，代码如下：

	// A simpler solution
	// Also one pass
	public class Solution {
	    public int maxSubArray(int[] nums) {
	        int curSum = nums[0], maxSum = nums[0];
	        for (int i = 1; i < nums.length; ++i) {
	            curSum = Math.max(curSum+nums[i], nums[i]);
	            maxSum = Math.max(curSum, maxSum);
	        }
	        return maxSum;
	    }
	}

###解法二：DP

其实本题也可以用dp来解决，令dp(i)表示从0到i，以i为结尾的最大连续字段和。
递推公式为：

	dp[i] = num[i] + max{dp[i-1],0}
	
根据上面的公式可以很容易求出每个点的dp[i]， 然后所有点的dp[i]的最大值就是解，代码如下：

	public int maxSubArray(int[] A) {
	        int n = A.length;
	        int[] dp = new int[n];//dp[i] means the maximum subarray ending with A[i];
	        dp[0] = A[0];
	        int max = dp[0];
	        for(int i = 1; i < n; i++){
	            dp[i] = A[i] + (dp[i - 1] > 0 ? dp[i - 1] : 0);
	            max = Math.max(max, dp[i]);
	        }
	        return max;
	} 

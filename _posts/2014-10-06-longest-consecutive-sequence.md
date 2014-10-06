---
layout: post
category: leetcode
title: Longest Consecutive Sequence
tagline: by hombo
tags: [leetcode,algorithm, c++,hash]
---

Given an unsorted array of integers, find the length of the longest consecutive elements sequence. For example,

Given `[100, 4, 200, 1, 3, 2]`,

The longest consecutive elements sequence is `[1, 2, 3, 4]`. Return its length: `4`.

Your algorithm should run in `O(n)` complexity.

此题可以用hashmap在`O(n)` 时间复杂度解决。

<!--more-->

一看是无需数组，又要求`O(n)`复杂度实现，很自然的可以想到用hashmap。

具体方法就是将数组全部hash了，然后在对数组的每个元素进行一边查找，

例如对于数组的第i个数，我们不断判断`num[i]+1`是否在hash表里，如果在说明数组存在比`num[i]`大1的数，然后继续判断`num[i]+2`......直到`num[i]+k`不再hash表里位置，在这个过程中需要记录长度。

只用上面的算法不能保证是`O(n)`的，看下面的例子

	数组是 `[7,6,5,4,3]`
	
	查找过程是： 
	
	7: `7`
	6: `6,7`
	5: `5,6,7`
	4: `4,5,6,7`
	3: `3,4,5,6,7`

显然复杂度最坏未为`O(n^2)`, 了保证算法是`O(n)`的，我们要用一些小技巧，让数组中已经被判断过的数不再去判断，其实很简单，就是加一个标示，表示这个数是否在前面的数里面判断过了，我们还可以将每次判断出来的长度存在这次判断最开始的那个数的hashmap中，这样下次如果加到这个数的时候，可以直接用这个长度值，而不需要往后遍历了，例如

	5: 5,6,7   把长度3存在hashmap : <5,3>
	4: 4,5,6,7 当4 上加到5的时候，可以从<5,3>直到5后面连续的长度是3，就不用再遍历了。
	
这样就可以保证算法是O(n)的了，空间复杂度是hash表大小。

AC代码如下：

	class Solution {
	public:
	    int longestConsecutive(vector<int> &num) {
	        unordered_map<int,int> hash;
	        int max = 0,count,j;
	        unordered_map<int,int>::iterator it;
	        for(int i = 0 ; i < num.size(); ++i) hash.insert({num[i],-1});
	        for(int i = 0 ; i < num.size(); ++i){
	            if(hash.find(num[i])->second!=-1) continue;
	            count = 1;
	            j = num[i]+1;
	            while((it=hash.find(j++))!=hash.end()){
	                if(it->second > 0){
	                    count += it->second;
	                    break;
	                }
	                it->second = 0;
	                ++count;
	            }
	            hash.find(num[i])->second = count;
	            if(max < count) max = count;
	        }
	        return max;
	    }
	    
	};

另外，还有一种算法是同时从num[i]的两边不断判断，并标记已经判断过的的数，这种方法可以省去一些长度的记录。




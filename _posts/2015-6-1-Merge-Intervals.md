---
layout: post
category: leetcode
title: Merge Intervals
tagline: by hombo
tags: [leetcode,algorithm]
---

Leetcode 上面有两道关于数轴区间重合和归并的问题：Merge Intervals 和 Insert Interval。两道题大同小异，可以用类似的方法求解。主要问题是如何区分两段区间是否重合，和采用哪种方式进行归并，要求效率要高。

<!--more-->

###如何判断数轴上两段区间是否重合？

猛一看此问题比较棘手，情况比较多，但是如果能换一个角度思考，问题就迎刃而解。我们可以采用逆向思维，判断两段区间重合也就是判断两段区间不重合的反命题，两段区间不重合非常容易判断，假设两区间：[a,b]和[c,d]，他们不重合的条件是

	b < c || a > d
	
那么我们可以很容易得出他们重合的条件是：

	!(b < c || a > d)
	
进一步，如果两段区间是按照开始端点从小到大排好序的，即有，a <= c，那么我们还可以把判断重合的条件进一步精简为：

	!(b < c)

这个条件就是我们后面解决这两道题目时所用到的。

###Merge Intervals

Given a collection of intervals, merge all overlapping intervals.

For example,

Given [1,3],[2,6],[8,10],[15,18],

return [1,6],[8,10],[15,18].

这道题目就是需要把所有重叠的区间归并成一个，然后给出最后所有不重叠的区间。

####O(n^2)解法

首先我们可以想到的是从第一区间开始，不断的往后找和它重叠的区间，然后合并，把合并的结果存到第一个区间中，并把其他区间删除，然后在从第一个区间开始重复找，如果找不到重叠的区间，就加入结果列表，并从第二个区间开始继续之前的操作，从而找到所有结果。

这种解法效率低，其实有更好的解法。

#### O(nlogn)解法

如果我们吧所有区间都按开始点从小到大排好序，那么我们就可以采用不断扩大区间结尾的方法来归并所有区间，这样我们只需要扫描一遍排好序后的列表即可，时间是O(n)， 但是我们的排序复杂度是O(nlogn)， 因此整体的复杂度是O(nlogn).
实现代码如下：

	public class Solution {
	    public List<Interval> merge(List<Interval> intervals) {
	        Collections.sort(intervals, new Comparator<Interval>(){
	            @Override
	            public int compare(Interval obj0, Interval obj1) {
	                return obj0.start - obj1.start;
	            }
	        });
	
	        List<Interval> ret = new ArrayList<>();
	        Interval prev = null;
	        for (Interval inter : intervals) {
	            if (  prev==null || inter.start>prev.end ) {
	                ret.add(inter);
	                prev = inter;
	            } else if (inter.end>prev.end) {
	                // Modify the element already in list
	                prev.end = inter.end;
	            }
	        }
	        return ret;
	    }
	}
	
其中利用Collections的sort函数，并实现了Comparator接口来自定义排序。

###Insert Intervals

Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

Example 1:
Given intervals [1,3],[6,9], insert and merge [2,5] in as [1,5],[6,9].

Example 2:
Given [1,2],[3,5],[6,7],[8,10],[12,16], insert and merge [4,9] in as [1,2],[3,10],[12,16].

This is because the new interval [4,9] overlaps with [3,5],[6,7],[8,10].

#### 解法：

这道题和Merge Intervals 大同小异，它已经实现了按起始点排序，我们就不需要在操作了，我们需要的是把newInterval按序插入到列表中，后面的操作就是Merge Intervals一模一样了，时间复杂度是O(n)，代码如下：

	public class Solution {
	    public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
	        List<Interval> result = new ArrayList<Interval>();
	        int i = 0, size = intervals.size();
	        for (; i < size; ++i) {
	            if (intervals.get(i).start >= newInterval.start) {
	                intervals.add(i,newInterval);
	                break;
	            }
	        }
	        if (i == size) 
	            intervals.add(newInterval);
	        Interval pre = null;
	        for (i = 0; i < intervals.size(); ++i) {
	            Interval cur = intervals.get(i);
	            if (pre == null || cur.start > pre.end) {
	                result.add(cur);
	                pre = cur;
	            }
	            else {
	                pre.end = Math.max(pre.end,cur.end);
	            }
	        }
	        return result;
	    }
	}
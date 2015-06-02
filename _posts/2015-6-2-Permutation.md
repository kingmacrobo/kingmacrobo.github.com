---
layout: post
category: leetcode
title: Permutation
tagline: by hombo
tags: [leetcode,algorithm]
---

leetcode上有四道关于排列的题，分别是：Next Permutation， Permutations， Permutations II 和 Permutation Sequence。对于Next Permutation和Permutation Sequence， 都是关于排列的字母序的，基本思想类似。对于Permutations和Permutations II，差别只是在于是否有重复的元素，有重复元素的实现会涉及到组合问题，比较棘手。


<!--more-->

###什么是排列的字母序
这里的字母序和字符串比较的字母序一样，例如123的排列按字母顺序为：

	123
	132
	213
	231
	312
	321
	
###Next Permutation

Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be in-place, do not allocate extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

	1,2,3 → 1,3,2
	3,2,1 → 1,2,3
	1,1,5 → 1,5,1
	
其实就是找出给出的排列在字母序中的下一个排列。

首先我们需要寻找规律，两个相邻的排列是怎么转换得到的？

	1,2,3 -> 1,3,2 交换2 和 3
	1,2,4,5,3 -> 1,2,5,3,4 交换4和5，然后交换3和4
	1,2,4,6,5,3 -> 1,2,5,3,4,6 交换4和6，然后对后面进行从小到大排序！
	1,2,4,7,6,5,3 -> 1,2,5,3,4,6,7 交换4和5，然后对后面从小到大排序！

至此，我们应该是能发现规律了：

	从右边开始往左找，找到第一个后面数大于前面数的位置，然后往后找到最后一个比前面数大的位置，交换两个数，然后将后面的所有数进行从小到大排序！
	
我们从理论上分析上面做法的正确性：

如果对于排列P，想找到它的相邻的下一个排列，那么由于是字母序的，所以我们要尽可能的在右边做交换，又由于想要增大字母序，必须将一个大的数和它左边比它小的数进行交换，因此我们从右边开始往左找，也就是说，这两个要交换的数尽量考右，而上面的找法，先找到了左边比右边小的位置，不放设左边为l，右边为r，然后再从r开始尽量往右找（尽量小），找到最后一个大于l的数m，交换m和l，这样将m换到l的位置后，不管l后面的数怎么变化，都比排列P要大，而我们想要的是大一个的排列，那我们就需要将l之后的排列的字母序设置为最小，也就是需要对l之后的数进行从小到大的排序。

思想就是这样，代码如下：

	public class Solution {
	    public void nextPermutation(int[] nums) {
	        int length = nums.length;
	        for (int i = length-1 ; i >= 0 ; --i) {
	            if (i > 0 && nums[i] > nums[i-1]) {
	                int j = i;
	                while (j < nums.length && nums[j] > nums[i-1]) ++j;
	                --j;
	                int temp = nums[j];
	                nums[j] = nums[i-1];
	                nums[i-1] = temp;
	                sort(nums,i);
	                return ;
	            }
	        }
	        sort(nums,0);
	    }
	    public void sort(int[] nums, int index) {
	        int start = index, end = nums.length -1;
	        while(start<end) {
	            int temp = nums[start];
	            nums[start++] = nums[end];
	            nums[end--] = temp;
	        }
	    }
	}

时间复杂度是O(n), 空间是O(1).


###Permutation Sequence

The set [1,2,3,…,n] contains a total of n! unique permutations.

By listing and labeling all of the permutations in order,

We get the following sequence (ie, for n = 3):

	"123"
	"132"
	"213"
	"231"
	"312"
	"321"

Given n and k, return the kth permutation sequence.

Note: Given n will be between 1 and 9 inclusive.

这道题是上一题的加强版，本题要求找到按字母序排序的第k个排列。

比较暴力的解法是：利用前面的Next Permutation，从第一个排列开始，不断的往后找，直到找到第k个排列。这样的时间复杂度很大为O(kn), 我们需要一种O(n)的解法！

我们可以利用不断定位的方式在O(n)时间内求解。

m个数的排列有m!种，如果 (m-1)! < k <= m!我们就可以直接定位到第k个排列是从第几个数开始分析的，也就是说前面有n-m个数我们是可以确定的，他们就是1,2,3,...,n-m.

定位到一个位置之后，我们可以算出k里面有多少个(m-1)!，假如有l个，然后选择后面的数中第l+1小的数放到此位置，然后令k = k - (m-1)! * l, 至此后面的问题就变成了一个子问题，我们可以用递归来解决，代码如下：

	// recursive method
	// Directly locate the position by comparing k with i!
	// Time complexity is O(n), space is O(n) since we used the recursive method.
	
	public class Solution {
		public String getPermutation(int n, int k) {
			StringBuilder sb = new StringBuilder();
			List<Integer> nums = new LinkedList<Integer>();
			for (int i = 1; i <= n; ++i)
				nums.add(i);
			deal(nums, k, sb);
			return sb.toString();
		}
	
		public void deal(List<Integer> nums, int k, StringBuilder sb) {
			if (k == 1) {
				for (Integer in : nums)
					sb.append(in);
				return;
			}
			int mul = 1, i;
			for (i = 1; i <= nums.size(); ++i) {
				mul *= i;
				if (mul >= k)
					break;
			}
			int size = nums.size();
			for (int w = 0; w < size - i; ++w) {
				sb.append(nums.get(0));
				nums.remove(0);
			}
			if (mul == k) {
				for (int j = nums.size() - 1; j >= 0; --j)
					sb.append(nums.get(j));
				return;
			}
			mul /= i;
			int cur = k / mul;
			if (k % mul == 0) {
				sb.append(nums.get(cur-1));
				nums.remove(cur-1);
				for (int w = nums.size()-1; w >=0; --w)
					sb.append(nums.get(w));
				return;
			}
			sb.append(nums.get(cur));
			nums.remove(cur);
			deal(nums, k - cur * mul, sb);
		}
	}
	
上面的方法显得有些臃肿，我们可以用迭代来优化它，代码如下：

	public String getPermutation(int n, int k) {
	        List<Integer> num = new LinkedList<Integer>();
	        for (int i = 1; i <= n; i++) num.add(i);
	        int[] fact = new int[n];  // factorial
	        fact[0] = 1;
	        for (int i = 1; i < n; i++) fact[i] = i*fact[i-1];
	        k = k-1;
	        StringBuilder sb = new StringBuilder();
	        for (int i = n; i > 0; i--){
	            int ind = k/fact[i-1];
	            k = k%fact[i-1];
	            sb.append(num.get(ind));
	            num.remove(ind);
	        }
	        return sb.toString();
	    }

上述代码中，令k = k - 1， 可以避免对k = m!情况的特殊处理，需要细细品味！

对于这种题，我们还可以引申一下：求出所有按字母序的排列？

时间复杂度会如何呢？

可以用递归求解，时间复杂度是O(n!).


###Permutations 和 Permutations II

两道题的区别主要是有没有重复的元素，题目大致如下：

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

For example,

[1,1,2] have the following unique permutations:

[1,1,2], [1,2,1], and [2,1,1].

####没有重复的分析

对于没有重复元素的情况，要产生全部排列，我们可以用子问题和迭代的方式：

如果我们已经知道了n-1个元素的全部排列，那么求n个元素的排列，就可以利用n-1个元素排列的结果，

方法是： 将第n个数分别插入到n-1个元素的每一个排列的每一个空隙中，每一种情况都唯一，不会重复，都是一个n个元素的排列。

这种方法非常简单，时间复杂度是O(Σk!), 其中k从1到n。 代码不再写出，下面分析有重复元素的情况。

####有重复元素的解决办法

如果有重复元素，我们就不能像前面的方法那样随意的插入了，但是我们还是可以利用这个思想--利用之前m个元素的排列结果。

首先我们需要将数组进行从小到大的排序，这样做的好处是让重复的元素都在一起，然后我们将数组看成一块一块的，每一块都是相同的元素，

如果此时知道之前m个元素的所有排列，而当前的块有k个a，我们所要做的就是利用组合的方法将k个a插入到之前m个元素排列的每一种排列中去，这就变成了一个组合问题，

那么如何插入呢?

假如之前有一个排列是`1 2 3 4` ， 现在有3个5要插入到里面， 做法就是求解将三个5插入到5个空位中的所有情况，

组合问题的解决十分简单，就是利用递归和回溯，可以见后面的代码。

这样按照相同块，不断的往后推，直到数组访问完，即可产生所有排列结果，总之，思想是迭代+组合。

此方法也可以用于求解没有重复元素的情况。

对于时间复杂度分析比较复杂，假设平均每块大小为m，有k块，则n = mk, 时间复杂度可以大致表示为O(n^m)(不太确定复杂度的计算)。

代码如下：

	public class Solution {
	    public List<List<Integer>> permuteUnique(int[] nums) {
	        List<List<Integer>> result = new ArrayList<List<Integer>>();
	        result.add(new ArrayList<Integer>());
	        Arrays.sort(nums);
	        for (int i = 0 ; i < nums.length; ++i) {
	            List<List<Integer>> temp = new ArrayList<List<Integer>>();
	            int count = 1;
	            int val = nums[i];
	            while (i+1 < nums.length && nums[i] == nums[i+1]) {
	                i++;
	                count++;
	            }
	            for (int j = 0 ; j < result.size(); ++j) {
	                List<Integer> item = result.get(j);
	                List<List<Integer>> new_items = new ArrayList<List<Integer>>();
	                ArrayList<Integer> list = new ArrayList<Integer>();
	                generate(item.size(),0,count,new_items,list);
	                for (int k = 0; k < new_items.size(); ++k) {
	                    ArrayList<Integer> locs = (ArrayList)new_items.get(k);
	                    List<Integer> one = new ArrayList<Integer>(item);
	                    for (int w = locs.size()-1; w >= 0 ; --w) {
	                        one.add(locs.get(w),val);
	                    }
	                    temp.add(one);
	                }
	            }
	            result = temp;
	        }
	        return result;
	    }
	    public void generate(int len, int index, int count, List<List<Integer>> new_items, ArrayList<Integer> list) {
	        for (int i = index; i <= len; ++i) {
	            list.add(i);
	            if (list.size() == count) {
	                new_items.add((ArrayList)list.clone());
	            }
	            else {
	                generate(len,i,count,new_items,list);
	            }
	            list.remove(list.size()-1);
	        }
	    }
	}
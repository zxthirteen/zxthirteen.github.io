---
layout: post
title: "Median of Two Sorted Arrays"
description: ""
category: "leetcode"
tags: [leetcode, interview, algorithm]
---
{% include JB/setup %}

There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).   

此题leetcode上的解法太太太复杂了，后来找一个相对简洁的版本：   
此题的解法要转化为从两个有序的数组中找第k小的数，因为原题要求如果m+n是偶数，那么要求出中间两个数中平均数，代码如下：   
	
	class Solution {
	public:
		double findMedianSortedArrays(int A[], int m, int B[], int n) {
			if ((m+n)%2 == 0) 
				return (find(A, m, B, n, (m+n)/2) + find(A, m, B, n, (m+n)/2+1)+0.0)/2;
			else 
				return find(A, m, B, n, (m+n)/2+1);
		}
		
		int find(int A[], int nA, int B[], int nB, int k) {
			if(nA == 0) return B[k-1];
			if(nB == 0) return A[k-1];
			if(k == 1) return min(A[0], B[0]);

			int ax = nA/2;
			int bx = nB/2;
			
			if (A[ax] > B[bx]) {
				if (ax + bx + 1 >= k) {
					find(A, ax, B, nB, k);
				} else {
					find(A, nA, B + bx + 1, nB - bx - 1, k - bx - 1);
				}
			} else {
				if (ax + bx + 1 >= k) {
					find(A, nA, B, bx, k);
				} else {
					find(A + ax + 1, nA - ax - 1, B, nB, k - ax - 1);
				}
			}
		}
	};
	
####这题比较难
最开始的想法是，在A中找到A的中位数，然后在B中找到第`k-Amid`个数，但是这样不行，原因是有可能B的数字个数比`k-Amid`要少；
后来参考别人的做法，也就是上述代码的解法，主要思路是通过比较A和B的中位数，每一次都把其中一个数组折半:   
	
	A: 0 1 2 3 4 5
	B: 0 1 2 
	
	比较a3和b1，如果a3>b1，那么两个数组前一部分的总数目有没有k大，
		如果>=k，那么从a3开始的后半部分可以去掉，从剩下的两个数组中找第k大的数
		如果<k，那么B的前一半就一定都是属于这前k个数的，可以把他们去掉（假设有x个），然后从剩下的两个数组里找第k-x小的数
	
但是在`ax + bx + 1 >= k`这一句中我最开始用`ax + bx >= k`但是这样的话在如下的特例就无法进行：
	
	A: 1 2
	B: 1
	k = 2
	
我开始的想法是因为ax和bx之前一共有`ax+bx`个数，但实际上，因为最后要从一个数组中删数，所以ax和bx中会有一个数被删除，另一个保留下来，所以可以考虑前`ax + bx + 1`个数，这样也同时解决了上面说的那个问题。   

###终止条件
1. k=1
2. m==0
3. n==0



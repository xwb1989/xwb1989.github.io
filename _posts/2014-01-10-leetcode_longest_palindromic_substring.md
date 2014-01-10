---
layout: post
title: LeetCode Longest Palindromic Substring
category: blog
---
#LeetCode Longest Palindromic Substring

[Problem](http://oj.leetcode.com/problems/longest-palindromic-substring/)

##O(n^2) Solution

The brute-force comes to `O(n^3)` and its easy to come up with the `O(n^2)` solution:

	public class Solution {
	    public String longestPalindrome(String s) {
	    	String max = "";
	    	String a = "";
	    	String b = "";
	        for (int i = 0; i < s.length(); i++) {
	        	String tmp;
	        	a = longest(s, i, i);
	        	b = longest(s, i, i+1);
	        	tmp = a.length() > b.length() ? a : b;
	        	max = max.length() > tmp.length() ? max : tmp;
	        }
	    	return max;
	    }
	    public String longest(String s, int l, int r) {
	    	while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
	    		l--;
	    		r++;
	    	}
	    	return s.substring(l+1, r);
	    }
	}
	
Besides, there is a sophisticated `O(n)` algorithm for this problem.

##O(n) Solution: Manacher’s Algorithm

See this [post](http://leetcode.com/2011/11/longest-palindromic-substring-part-ii.html).

##O(nlogn) Solution: Divide and Conquer
It can also be solved by divide and conquer. See this [post](http://leetcode.com/2011/11/longest-palindromic-substring-part-i.html).
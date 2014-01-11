---
layout: post
title: LeetCode Multiply Strings
category: blog
---
#LeetCode Multiply Strings

[Problem](http://oj.leetcode.com/problems/multiply-strings/)

I made several mistakes, including [this one](http://xwb1989.github.io/blog/2014/01/strings_are_different.html). But the most significant faults I made were failing to handle the running time. 

Again, it's my fault to not thoroughly understand how multiplication works and fail to simulate the procedure properly and decently thus the code became more complicated than it should be. 

Here is the final version:  

	    public String multiply(String num1, String num2) {
			boolean aNeg = false;
			boolean bNeg = false;
			if (num1.charAt(0) == '-') {
				aNeg = true;
				num1 = num1.substring(1);
			}
			if (num2.charAt(0) == '-') {
				bNeg = true;
				num1 = num2.substring(1);
			}
			int l1 = num1.length();
			int l2 = num2.length();
			int[] ans = new int[l1+l2];
			int k = ans.length - 1;
			int carry = 0;
			for (int i = l1-1; i >= 0; i--) {
				k = ans.length - 1 - (l1 - 1 - i);
				int aVal = num1.charAt(i)- '0';
				int j = l2 - 1;
				while (j >= 0 || carry != 0) {
					int bVal = j >= 0 ? num2.charAt(j) - '0' : 0;
					int kAns = ans[k];
					int product = aVal * bVal + kAns + carry;
					kAns = product % 10;
					carry = product / 10;
					ans[k--] = kAns;
					j--;
				}
			}
			String ret = "";
			int c = 0;
			while (c < ans.length && ans[c] == 0) {
				c++;
			};
			if (c == ans.length) {
				return "0";
			}
			while (c < ans.length) {
				ret += ans[c++];
			}
			if (aNeg != bNeg) {
				ret = "-" + ret;
			}
			return ret;
		}
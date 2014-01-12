---
layout: post
title: LeetCode Add Binary
category: blog
---
#LeetCode Add Binary

[Problem](http://oj.leetcode.com/problems/add-binary/)

##Can be as simple as possible
This problem does not worth a note. I put this one just to remind myself that when you found a solution could be very tedious and cumbersome, probably you should think of a more decent one. This problem is the example.

##Ugly but work version 1
        public String addBinary(String a, String b) {
			if (a.length() > b.length()) {
				String s = b;
				b = a;
				a = s;
			}
			if (a.isEmpty()) {
				return b;
			}
			char[] s1 = a.toCharArray();
			char[] s2 = b.toCharArray();
			char[] ret = new char[b.length()+1];
			Arrays.fill(ret, '0');
			int i = s1.length-1;
			int j = s2.length-1;
			int k = ret.length-1;
			for (; i >= 0 || j >= 0 || k >= 0; i--, j--, k--) {
				if (i >= 0) {
					if (s1[i] == '1' && s2[j] == '1' && ret[k] == '1') {
						ret[k] = '1';
						ret[k-1] = '1';
					} else if (s1[i] == '0' && s2[j] == '0' && ret[k] == '0') {
						ret[k] = '0';
					} else if ((s1[i] ^ s2[j] ^ ret[k]) == '0') {
						ret[k-1] = '1';
						ret[k] = '0';
					} else {
						ret[k] = '1';
					}
				} else if (j >= 0) {
					if (s2[j] == ret[k]) {
						if (s2[j] == '1') {
							ret[k] = '0';
							ret[k-1] = '1';
						} 
					} else {
						ret[k] = '1';
					}
				}
			}
			if (ret[0] == '0') {
				return new String(ret).substring(1);
			} else {
				return new String(ret);
			}
		}
		
I totally missed the nature of add operation.

##Wow
		public String addBinaryS(String a, String b) {
			char[] s1 = a.toCharArray();
			char[] s2 = b.toCharArray();
			String ans = "";
			int i = s1.length-1;
			int j = s2.length-1;
			int carry = 0;
			while (i >= 0 || j >= 0 || carry == 1) {
				int aVal = (i >= 0) ? s1[i] - '0' : 0;
				int bVal = (j >= 0) ? s2[j] - '0' : 0;
				int sum = aVal + bVal + carry;
				carry = sum / 2;
				sum = sum % 2;
				ans = (sum == 1 ? "1" : "0") + ans;
				i--;
				j--;
			}
			return ans;
		}
		
This solution perfectly utilize the nature of add operation and therefore simplify the logic.
	
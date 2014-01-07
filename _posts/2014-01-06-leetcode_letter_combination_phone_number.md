---
layout: post
title: LeetCode Letter Combination Phone Number
category: blog
---
#LeetCode Letter Combination Phone Number
[Problem](http://oj.leetcode.com/problems/letter-combinations-of-a-phone-number/)
##Every One Use DFS

How can some one be unable to come up with a non-DFS solution for this problem? You got a graph, you want to traverse every path, of course you should use DFS or BFS!

Therefore, I would show the DFS codes first, recursive and iterative.

##DFS-Recursive

    public void dfs(String digits, int i, String[] ref, String prev, ArrayList<String> res) {
			if (i >= digits.length()) {
				res.add(prev);
			} else {
				int digit = digits.charAt(i) - '0';
				String s = ref[digit];
				for (int j = 0; j < s.length(); j++) {
					String prevNew = prev + String.valueOf(s.charAt(j));
					dfs(digits, i+1, ref, prevNew, res);
				}
			}
		}
		
Straight forward! Intuitively! But I'm afraid of the `stack overflow error` which gives me the initiate to implement an ugly iterative version.

##DFS-Iterative


		public void dfs_iterative(String digits, String[] ref, ArrayList<String> res) {

			Stack<String> stack = new Stack<String>();
			if (digits.length() == 0) {
				res.add("");
			} else {
				int digit = digits.charAt(0) - '0';
				String s = ref[digit];
				for (int j = 0; j < s.length(); j++) {
					String prevNew = String.valueOf(s.charAt(j));
					stack.push(prevNew);
				}
				while (! stack.isEmpty()) {
					prev = stack.pop();
					if (prev.length() == digits.length()) {
						res.add(prev);
					} else {
						i = prev.length();
						digit = digits.charAt(i) - '0';
						s = ref[digit];
						for (int j = 0; j < s.length(); j++) {
							String prevNew = prev + String.valueOf(s.charAt(j));
							stack.push(prevNew);
						}
					}
				}
			}
		}

##From the Perspective of Set

Before doing the DFSs staff, I figure another solution inspired by the idea of "recursive definition of set":

1. `a` in set `S`;
* if `x` in set `S`, then `x+y` in set `S`.

###  

    public ArrayList<String> letterCombinations(String digits) {
		String[] ref = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv",     "wxyz"};
		ArrayList<String> tmp;
		ArrayList<String> res = new ArrayList<String>();
		if (digits.isEmpty()) {
			res.add("");
			return res;
		}
		for (int i = 0; i < digits.length(); i++) {
			int digit = charToInt(digits.charAt(i));
			tmp = new ArrayList<String>();
			if (i == 0) {
				for (int j = 0; j < ref[digit].length(); j++) {
					tmp.add(String.valueOf(ref[digit].charAt(j)));
				}
			} else {
				for (String s: res) {
					for (int j = 0; j < ref[digit].length(); j++) {
						String postfix = String.valueOf(ref[digit].charAt(j));
						tmp.add(s+postfix);
					}
				}
			}
			res = tmp;
		}
		return res;
	}
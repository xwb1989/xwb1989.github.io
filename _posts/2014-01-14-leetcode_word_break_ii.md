---
layout: post
title: LeetCode Word Break II
category: blog
---

#LeetCode Word Break II

[Problem](http://oj.leetcode.com/problems/word-break-ii/)

##Don't be naive!
This problem really push me to carefully think about dynamic programming technique. 

The naive, non-dynamic-programming version could definitely fail the test. The problem is, how to implement the dynamic programming? 

##Don't be naive again!

My first version of dynamic programming uses the string `s` as key and the array-list of valid strings that can be built on `s` as value. Then there comes the problem: when the input becomes large, the memo would consume too much memory and cause the program to crash.

In order to compress the memo, I change the value with the array-list of positions splitting the string `s` as value. But it fails again.

##Record `delta` instead of everything
 
Then I figure out that there are huge amount of duplicate elements in the memo, and if I can use a linked list, then I might be able to reuse the existed elements efficiently. 

However I don't want to implement a pointer-operation-available linked list from sketch. But inspired by the idea of linked list, I just realize that instead of storing the complete solution in memo, I can only store the `delta`, the change that would be added to  the solution of smaller problems.

Then naturally, it's obvious that I have converted this dynamic problem into a graph problem: each node is the `delta`, and the paths from the original state to the final state the solutions.

###Don't be naive again and again!

I use this `delta` dynamic programming technique to parse the string and dict and generate a graph-like memo, and then use `dfs` to construct the result. Wahoo! It seems perfect!
	
	public class Solution {
		ArrayList<String> ans = new ArrayList<String>();
		HashMap<Integer, ArrayList<Integer>> dp = new HashMap<Integer, ArrayList<Integer>>();
		public ArrayList<String> wordBreak(String s, Set<String> dict) {
			breakHelp(s, 0, dict);
			dfs(s, "", 0);
			
			return ans;
		}
		
		public void dfs(String s, String str, int i) {
			if (i == s.length()) {
				ans.add(str.trim());
			}
			if (dp.containsKey(i)) {
				for (int j : dp.get(i)) {
					String strNew = str + " " + s.substring(i, j);
					dfs(s, strNew, j);
				}
			}
			return;
		}

		int c = 0;
		public void breakHelp(String s, int i, Set<String> dict) {
			ArrayList<Integer> ret = new ArrayList<Integer>();
			if (i == s.length()) {
				return;
			}
			for (String word : dict) {
				if (s.startsWith(word, i)) {
					int k = i + word.length();
					ret.add(k);
					breakHelp(s, k, dict);
				}
			}
			if (!ret.isEmpty()) {
				dp.put(i, ret);
			}
		}
	}
	
However it fail again!!!!!It still cannot pass one of the test cases! 

##Weird test case
Having no choice by coding this problem for more than three days, I have to look at other people's solution and find a decent one. I make seldom modification and submit, it passes! I'm very confused: why both using dynamic programming, it can pass test but mine cannot? 

After testing and careful reading, I finally figure out the problem: actually we have the same performance on `valid` problems, which mean they have valid outputs. But for `invalid` problems, my code cannot detect the `invalidity` efficiently thus when the input is insanely large, my code will fail but if the input is invalid, the other one could efficiently return an empty output set.

It's like there is a maze and the exit has been blocked, my code will look up every possible route in the maze and check whether it work or not. But the other code could immediate tell us that it's impossible to get out because there is no exit! Though I could say the test case is a bit weird, but the idea of this code is really inspiring.


    public class Solution {
		ArrayList<String> ans = new ArrayList<String>();
		HashMap<String, HashSet<String>> dp = new HashMap<String, HashSet<String>>();
		public ArrayList<String> wordBreak(String s, Set<String> dict) {
			breakHelp(s, dict);
			ans.addAll(dp.get(s));
			return ans;
		}

		public void breakHelp(String s, Set<String> dict) {
			if (dp.containsKey(s)) {
				return;
			}
			HashSet<String> ret = new HashSet<String>();
			if (dict.contains(s)) {
				ret.add(s);
			}
			if (s.length() == 1) {
				dp.put(s, ret);
				return;
			}
			for (int i = 1; i < s.length(); i++) {
				String left = s.substring(0,i);
				String right = s.substring(i);
				if (left.length() == 0 || right.length() == 0) {
					continue;
				}
				if (dp.get(left) != null && dp.get(left).isEmpty() || 
						dp.get(right) != null && dp.get(right).isEmpty()) {
					continue;
				}
				if (!dp.containsKey(left)) {
					breakHelp(left, dict);
				}
				if (!dp.containsKey(right)) {
					breakHelp(right, dict); 
				}
				if (dp.get(left).isEmpty() || dp.get(right).isEmpty()) {
					continue;
				}
				for (String s1 : dp.get(left)) {
					for (String s2 : dp.get(right)) {
						ret.add(s1 + " " + s2);
					}
				}
			}
			dp.put(s, ret);
		}
	}


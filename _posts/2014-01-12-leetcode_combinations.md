---
layout: post
title: LeetCode Combinations
category: blog
---
#LeetCode Combinations

[Problem](http://oj.leetcode.com/problems/combinations/)

At the first glance, I thought the solution should be similar to that of [permutations](http://oj.leetcode.com/problems/permutations/) and I composed a solution based on the one of permutations.

The problem is that the test case this time is more strict and naive recursive implementation would be rejected by TLE.

So I have to accelerate it by dynamic programming. Instead of memorization, I use from-bottom-to-up to implement dynamic programming.

	public class Solution {
		public ArrayList<ArrayList<Integer>> combine(int n, int k) {
			ArrayList<ArrayList<Integer>> ret = new ArrayList<ArrayList<Integer>>();
			//Outer loop controls the length of combination in the result;
			for (int i = 1; i <= k; i++) {
				ArrayList<ArrayList<Integer>> tmp = new ArrayList<ArrayList<Integer>>();
				if (ret.isEmpty()) {
					for (int j = 1; j <= n; j++) {
						ArrayList<Integer> com = new ArrayList<Integer>();
						com.add(j);
						ret.add(com);
					}
					continue;
				}
				//Iteratively update the result;
				for (ArrayList<Integer> com : ret) {
					for (int j = 1; j <= n; j++) {
						//Make sure only the valid combinations are in the result: 
						//the ones organized from small to large.
						if (com.get(com.size()-1) >= j) {
							continue;
						}
						ArrayList<Integer> newCom = copy(com); 
						newCom.add(j);
						tmp.add(newCom);
					}
				}
				ret = tmp;
			}
			return ret;
		}
		
		//For deep copy the combination;
		public ArrayList<Integer> copy(ArrayList<Integer> com) {
			ArrayList<Integer> ret = new ArrayList<Integer>();
			for (int i : com) {
				ret.add(i);
			}
			return ret;
		}
	}
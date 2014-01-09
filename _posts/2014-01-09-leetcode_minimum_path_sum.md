---
layout: post
title: LeetCode Minimum Path Sum
category: blog
---
#LeetCode Minimum Path Sum
[Problem](http://oj.leetcode.com/problems/minimum-path-sum/)

##Naive Recursion

My first solution is straight forward and naive: find the min-sums of left and right slots and return the minimum one plus the current cost. This will cost `O((n*m)^2)`. And without surprise, it is rejected by TLE.

##Dynamic Programming

As I was rejected by TLE, it's obvious that I need some technique to boost the algorithm and dynamic programming is the choice.

    public class Solution {
		public int minPathSum(int[][] grid) {
			if (grid.length == 0) {
				return 0;
			} else {
				int[][] mem = new int[grid.length][grid[0].length];
				return minSum(grid, mem, 0, 0);
			}
		}

		public int minSum(int[][] grid, int[][] mem, int row, int col) {
			int cost = grid[row][col];
			int result = 0;
			if (mem[row][col] != 0) {
				return mem[row][col];
			}
			if (row == grid.length-1 && col == grid[0].length-1) {
				result = cost;
			} else if (row == grid.length-1) {
				result = cost + minSum(grid, mem, row, col+1);
			} else if (col == grid[0].length-1) {
				result = cost + minSum(grid, mem, row+1, col);
			} else {
				int down = -1;
				int right = -1;
				down = minSum(grid, mem, row+1, col);
				right = minSum(grid, mem, row, col+1);
				result = (down > right) ? right + cost : down + cost;
			}
			mem[row][col] = result;
			return result;
		}
	}
	
The implementation is naive as well and it works. The main cons of this solution is the dynamic programming section cost too much memory -- `O(n * m)`.

I found a better solution in the discussion of this problem. It also use dynamic programming but with more sophisticated implementation and shows that my understanding of dynamic programming is way more naive.

##Enhanced Dynamic Programming

[Here](http://oj.leetcode.com/discuss/816/minimum-path-sum-how-can-i-reduce-the-memory)

The main idea is that: 

* You are computing the intermediate results row by row;
* Thus you can only use one-row-length array to store the results during the computation of one row;
* Then reuse the space by overwriting better result in the next row computation.

This solution can reduce the memory usage to `O(n)`. 
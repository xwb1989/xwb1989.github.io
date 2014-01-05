---
layout: post
title: LeetCode OJ Candy
category: blog
---
#LeetCode OJ\: Candy

##Simpler Than I Expected

[The problem](http://oj.leetcode.com/problems/candy/) looks simple and you will find it not. You might use a complicated approach to conquer it, for example, I convert it into a graph problem, some one else used divide-and-conquer scheme. That seems absolutely over-kill for such an explicit problem with simple data organization. 

Yes, it's over-kill indeed. The `official` method is much simpler than I expected and I spent quite a while to think about the correctness of this simpler approach.

##My Approach: Graph Algorithm

	public class Solution {
		public int candy (int[] ratings) {
			ArrayList<ArrayList<Integer>> weakNeighbors = new ArrayList<ArrayList<Integer>>(); 
			//First Pass
			for (int i = 0; i < ratings.length; i++) {
				ArrayList<Integer> weaks = new ArrayList<Integer>();
				if (ratings.length == 1) {
					return 1;
				} else if (i == 0) {
					if (ratings[i] > ratings[i+1]) {
						weaks.add(i+1);
					}
				} else if (i == ratings.length - 1) {
					if (ratings[i] > ratings[i-1]) {
						weaks.add(i-1);
					}
				} else {
					if (ratings[i] > ratings[i-1]) {
						weaks.add(i-1);
					}
					if (ratings[i] > ratings[i+1]) {
						weaks.add(i+1);
					}
				}
				weakNeighbors.add(weaks);
			}

			//DFS
			boolean[] visited = new boolean[ratings.length];
			int[] shares = new int[ratings.length];
			Stack<Integer> stack = new Stack<Integer>();
			for (int i = 0; i < visited.length; i++) {
				if (! visited[i]) {
					dfs(weakNeighbors, ratings, visited, shares, i, stack);
				}
			}
			int result = 0;
			for (int i = 0; i < shares.length; i++) {
				result += shares[i];
			}
			return result;
		}

		public void dfs(ArrayList<ArrayList<Integer>> weakNeighbors, int[] ratings, boolean[] visited, int[] shares, int root, Stack<Integer> stack) {
			stack.push(root);
			while (! stack.isEmpty()) {
				int next = stack.pop();
				visited[next] = true;
				ArrayList<Integer> weaks = weakNeighbors.get(next);
				if (weaks.isEmpty()) {
					shares[next] = 1;
				} else {
					//If has unvisited neighbor, push it back to stack and push its neighbors
					boolean neighborVisited = true;
					for (int i : weaks) {
						if (! visited[i]) {
							neighborVisited = false;
						}
					}
					//Compute its share 
					if (neighborVisited) {
						int highest = 0;
						for (int i : weaks) {
							if (shares[i] > highest) {
								highest = shares[i];
							}
						}
						shares[next] = highest + 1;
					} else {
						visited[next] = false;
						stack.push(next);
						for (int i : weaks) {
							if (! visited[i]) {
								stack.push(i);
							}
						}
					}
				}
			}
		}
	}
	
It's complicated, but it works. The general idea is: 

1. In the first pass, build a graph relation between ratings: ratings are vertices and edges `u->v` indicating rating `u` is higher then rating `v` and of course `u` and `v` must be neighbors.
2. Use `dfs` algorithm to traverse the graph, compute the shares of each rating by following rules:
    * Ratings with no successor should have 1 candy;
    * Each ratings predecessor should have 1 more candy then itself.
3. Add the shares up

The idea is a bit complicated but straight forward. The main problem of my implementation is originally I used recursive `dfs` to traverse the graph and it sucks on some test cases: stack over flow error. Then I convert it into an iterative version and it becomes more complicated but works perfectly finally.

I don't like this solution though it's better than nothing. There is another much more decent and simpler solution online.

##Two Pass Iteration Solution

The general idea of this solution is:

1. Initialize all ratings to have 1 candy each; 
1. We have 2 pass of iteration of the ratings, one from left to right and one in reverse direction;
2. In the first iteration, if current rating is larger than its left, its candy should be one more than its left's;       
3. In the second iteration, if current rating is larger than its right, its candy should be one more than its right's;
4. That's all.

##The Correctness of Two Pass Iteration

This approach is so concise that I'm tended to double its correctness. Well its correct and following is my idea about its correctness:

1. In the first pass, if a rating is larger than its left, then it will has 1 more candy then its left;
2. In the second pass, if a rating is larger than its right, then it will has 1 more candy then its right;
3. 1, 2 have ensured that if a rating is larger than its neighbors, it will has more candy than its neighbors;
4. Since it only has **1** more candy then its neighbors, the candies it has should be the minimum: if it has less, it would not have **more** candies than its neighbors; 
5. Then the total number of candies should be the minimum.  

























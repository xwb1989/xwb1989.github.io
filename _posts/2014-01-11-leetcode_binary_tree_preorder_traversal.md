---
layout: post
title: LeetCode Binary Tree Preorder Traversal
category: blog
---
#LeetCode Binary Tree Preorder Traversal

[Problem](http://oj.leetcode.com/problems/binary-tree-preorder-traversal/)

##O(n) Solutions

`O(n)` solutions are trivial: either recursive one or iterative with stack one is `O(n)` running time but the space complexity is also `O(n)` where `n` is the number of nodes.

##O(nlogn) Time and O(1) Space

Use [morris traversal](http://www.geeksforgeeks.org/morris-traversal-for-preorder/) can traverse the tree pre orderly in O(1) space complexity. 

The algorithm is based on the idea of [threaded binary tree](http://en.wikipedia.org/wiki/Threaded_binary_tree). It needs to modify the tree during the traversal(then restore it when finish). Thus you need the write permit of the tree if you want to use this algorithm. 

Here is my `java` implementation: 

    /**
     * Definition for binary tree
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
	public class Solution {
		public ArrayList<Integer> preorderTraversal(TreeNode root) {
			ArrayList<Integer> ret = new ArrayList<Integer>();
			if (root == null) {
				return ret; 
			}
			while (root != null) {
				if (root.left == null) {
					ret.add(root.val);
					root = root.right;
				} else {
					TreeNode curr = root.left;
					//Find the in-order predecessor
					while (curr.right != null && curr.right != root) {
						curr = curr.right;
					}
					if (curr.right == null) {
						ret.add(root.val);
						curr.right = root;
						root = root.left;
					} else {
						curr.right = null;
						root = root.right;
					}
				}
			}
			return ret;
		}
	}

There are `O(n)` outer loops and in each loop the `find the in-order predecessor` could take `O(logn)`, thus in total it's `O(nlogn)`.
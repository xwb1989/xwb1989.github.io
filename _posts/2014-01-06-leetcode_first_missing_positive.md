---
layout: post
title: LeetCode First Missing Positive
category: blog
---
#LeetCode First Missing Positive

[Problem](http://oj.leetcode.com/problems/first-missing-positive/)

##All Roads Lead to Rome
You got tons of method to crack this problem, especially if there is no requirement of time and space. See this [article](http://www.geeksforgeeks.org/find-the-smallest-positive-number-missing-from-an-unsorted-array/) you will see some solutions and analysis.

##Kicked and Kicking
The `O(n)` solution given in the article above is of great wit but it might not be a general choice. My solution is not so intuitive, but should be more general.

		public int firstMissingPositive(int[] A) {
			if (A.length == 0) {
				return 1;
			}
			int j = A.length;
			for (int i = 0; i < j; i++) {
				int moveIn = A[i];
				int moveOut;
				while (moveIn > 0 && moveIn <= j && moveIn != A[moveIn-1]) {
					moveOut = A[moveIn-1];
					A[moveIn-1] = moveIn;
					moveIn = moveOut;
				}
			}
			int i;
			for (i = 0; i < A.length; i++) {
				if (A[i] != i+1) {
					return i+1;
				}
			}
			return i+1;
		}
		
The key operation is within the `while loop`, it swaps integers to their correct positions. It's actually a in place but not stable counting sort. To ensure the swap is correct, I got inspired by the collision resolve method in [cuckoo hashing](http://en.wikipedia.org/wiki/Cuckoo_hashing):

1. Start from slot 0;
* Swap the integer to its right slot, kick out the original integer at that slot;
* If the kick-out integer is valid, swap it to its right place;
* Repeat 1 and 2 until the kick-out integer is invalid or this has already been at its right slot;
* Move to next slot.

When the `for loop` ends, the array should maintain following properties:

1. If the integer is valid, it should be at its correct position;
2. If the integer is missing, its position must be occupied by an invalid integer;

Thus in the second `for loop`:

1. If you overcome a invalid integer, the should-be owner of the slot should be the result;
2. If you do not overcome a invalid integer, it means all integers are continuous and valid. The result should be next smallest integer not in the array.

##Running Time
The running time of all this kicked-and-kicking is `O(n)`:

Every integers could only be accessed at most twice:

* It's not in correct slot, it kicks other one out and find its position;

Or

* It's been kicked out by another integer and it kicks out some one else then find its position.

Thus the running time is `O(n)`.

##More Beautiful Solution

I found an [more beautiful solution](http://tianrunhe.wordpress.com/2012/07/15/finding-the-1st-missing-positive-int-in-an-array-first-missing-positive/) that has a similar idea as mine but with more elegant and intuitive implementation. 

To save your life, its key idea can be concluded as:

1. Iterate from slot 0;
* If the integer is not at correct slot(call it slot `i`), swap it with the integer at slot `i`;
* If the swap-back integer(which should be matched to slot `j`) is not matched with the slot either, continue swap it with the integer at slot `j`;
* Repeat 2 and 3 until the swap-back integer is invalid or matched the slot;
* Move to next slot.

The procedures above maintain following invariant during their execution:

1. If the slot has matched integer, after it has been passed, it must contains that integer;
2. Else it must contain an invalid integer. 

With these two properties, things are almost done.
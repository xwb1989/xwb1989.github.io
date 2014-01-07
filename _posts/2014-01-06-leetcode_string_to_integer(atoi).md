---
layout: post
title: LeetCode String to Integer(atoi)
category: blog
---
#LeetCode String to Integer(atoi)
[Problem]("http://oj.leetcode.com/problems/string-to-integer-atoi/")
##Main Idea

* Get rid of spaces;
* Get rid of alphabet letters;
* Negative or Positive;
* From most significant bit to least significant bit;

##Getcha

* The integer represented by the string might exceed the range of data type `int`;
* Need to return `Integer.MIN_VALUE`(-2147483648) or `Integer.MAX_VALUE`(2147483647) if necessary;
* When overflow happens, there might be two condition:
    * the sign flips once or multiple times;
    * if flip single once, its easy to figure out;
    * else, need some computation.
    
The understanding of how `int` overflow is handled is the only challenge of this problem.

##First Version

        public int atoi(String str) {
			boolean isNegative = false;

			int result = 0;
			int i = 0;
			if (str.isEmpty()) {
				return 0;
			}

			//Skip blanks
			while (str.charAt(i) == ' ') {
				i++;
			}
			//Parse
			for (; i < str.length(); i++) {
				int oldResult = result;
				if (str.charAt(i) == '+') {
					i++;
				}
				if (str.charAt(i) == '-') {
					isNegative = true;
					i++;
				}
				if (str.charAt(i) > '9' || str.charAt(i) < '0') {
					break;
				}

				if (isNegative) {
					result = result * 10 - (str.charAt(i) - '0'); 
					if (result > 0 || oldResult != (result + (str.charAt(i) - '0')) / 10) {
                        return Integer.MIN_VALUE;
					}
				} else {
					result = result * 10 + (str.charAt(i) - '0'); 
					if (result < 0 || oldResult != (result - (str.charAt(i) - '0')) / 10) {
                        return Integer.MAX_VALUE;
					}
				}
			}
			return result;
		}
		
The organization is complicated and redundant. The overflow-handler scheme is not decent enough.

##Second Version

Check the `result` every time you are gonna change it.

        public int atoiNew (String str) {
			boolean isNegative = false;

			int result = 0;
			int i = 0;
			if (str.isEmpty()) {
				return 0;
			}

			//Skip blanks
			while (str.charAt(i) == ' ') {
				i++;
			}
			//Parse
			for (; i < str.length(); i++) {
				if (str.charAt(i) == '+') {
					i++;
				}
				if (str.charAt(i) == '-') {
					isNegative = true;
					i++;
				}
				if (str.charAt(i) > '9' || str.charAt(i) < '0') {
					break;
				}

				if (isNegative) {
					if (Integer.MIN_VALUE / 10 > result) {
						return Integer.MIN_VALUE;
					} else {
						result *= 10;
					}
					if (Integer.MIN_VALUE + (str.charAt(i) - '0') > result) {
						return Integer.MIN_VALUE;
					} else {
						result -= (str.charAt(i) - '0');
					}
				} else {
					if (Integer.MAX_VALUE / 10 < result) {
						return Integer.MAX_VALUE;
					} else {
						result *= 10;
					}
					if (Integer.MAX_VALUE -(str.charAt(i) - '0') < result) {
						return Integer.MAX_VALUE;
					} else {
						result += (str.charAt(i) - '0');
					}
				}
			}
			return result;
		}
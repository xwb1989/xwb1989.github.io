---
layout: post
title: LeetCode Wildcard Matching
category: blog
---
#LeetCode Wildcard Matching

[Problem](http://oj.leetcode.com/problems/wildcard-matching/)

My solution is simple:

* Split the pattern into parts by `*`;
* Match the head and tail;
* Match the inside parts;

The challenge is the numerous corner cases.

	public class Solution {
	    public boolean isMatch(String s, String p) {
	    	if (s.isEmpty() && p.isEmpty()) {
	    		return true;
	    	} else if (p.isEmpty()) {
	    		return false;
	    	}
	    	boolean tail = p.charAt(p.length()-1) == '*';

	    	String[] ps = p.split("\\*");
	    	int pos = 0;
	        for (int i = 0; i < ps.length; i++) {
	        	//Skip *
	        	if (ps[i].equals("")) {
	        		continue;
	        	}
	        	if (i == ps.length-1 && !tail) {
	        		//Match the tail if the tail is not *
	        		int newPos = s.length() - ps[i].length();
	        		if (newPos < pos || newPos >= s.length()) {
	        			return false;
	        		}
	        		pos = isPartialMatch(s, newPos, ps[i]);
	        	} else {
	        		//Match inside parts
	        		pos = isPartialMatch(s, pos, ps[i]);
	        	}
	        	//If unmatched partially, return false;
	        	if (pos == -1) {
	        		return false;
	        	}
	        	//Not start with * and the head not matched, return false;
	        	if (i == 0 && pos > 0) {
	        		return false;
	        	}
	        	//March to next partial match
	        	pos += ps[i].length();
	        }
	        //If not end with * and the tail is not matched, return false;
	        if (pos < s.length() && !tail) {
	        	return false;
	        }
	    	return true;
	    }
	    
	    public int isPartialMatch(String s, int i, String p) {
	    	if (i >= s.length()) {
	    		return -1;
	    	}
	    	int n = s.length() - p.length();
	    	int l = p.length();
	    	for(; i <= n; i++) {
	    		boolean flag = true;
	    		for (int j = 0; j < l; j++) {
	    			if (s.charAt(i+j) != p.charAt(j) && p.charAt(j) != '?') {
	    				flag = false;
	    				break;
	    			}
	    		}
	    		if (flag) {
	    			return i;
	    		}
	    	}
	    	return -1;
	    }
	}
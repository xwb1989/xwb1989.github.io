---
layout: post
title: LeetCode Anagrams
category: blog
---
#LeetCode Anagrams

[Problem](http://oj.leetcode.com/problems/anagrams/)

##Are they anagrams?
To determine whether two strings are anagrams is the basis of this problem. We got several methods:

* Use hashtable or array(length 26 is enough)record the frequency of characters of two strings and see whether they share the same character frequency.
* Sort the character, form new string, compare new string.

##Running time
The main challenge(or main ambiguity) of this problem is to solve the problem with the (unstated) running time.

At first, I use nested loops to traverse the array and resulted in `O(n^2)` running time, it fails.

It admit that `O(n^2)` is not good enough. Thus I compose a more sophisticated algorithm that can run in lower time complexity in average case.

##O(kn) in average

        public ArrayList<String> anagrams(String[] strs) {
			ArrayList<String> res = new ArrayList<String>();
			boolean[] invalid = new boolean[strs.length];
			int i, j;
			i = j = 0;
			while (j < strs.length) {
				char[] head = strs[i].toCharArray();
				Arrays.sort(head);
				int old = i;
				while (j < strs.length) {
					char[] body = strs[j].toCharArray();
					Arrays.sort(body);
					if (arrayEqual(head, body)) {
						String tmp = strs[j];
						strs[j] = strs[i];
						strs[i] = tmp;
						j++;
						i++;
					} else {
						j++;
					}
				}
				if (old == i-1) {
					invalid[old] = true;
				}
				j = i;
			}
			for (int k = 0; k < strs.length; k++) {
				if (!invalid[k]) {
					res.add(strs[k]);
				}
			}

			return res;
		}
		public boolean arrayEqual(char[] a, char[] b) {
			if (a.length != b.length) {
				return false;
			}
			int i = 0;
			while (i < a.length && a[i] == b[i++]);
			return i == a.length;
		}
	
It runs in `O(nk)` where `k` is the kind of anagrams in the input. This clear that in the worst case where every word is not anagram with others, the running time still is `O(n^2)`. I was hoping this could pass the tests but it did not.

Then, I got no choice but ask for the most dull, boring approach: use hash table to store anagrams with `identity`(the string formed by `sorted` characters) as key.

##Hash works and it's boring
		public ArrayList<String> anagrams(String[] strs) {
			HashMap<String, ArrayList<String>> hash = new HashMap<String, ArrayList<String>>();
			ArrayList<String> res = new ArrayList<String>();
			for (int i = 0; i < strs.length; i++) {
				String str = strs[i];
				char[] chars = str.toCharArray();
				Arrays.sort(chars);
				String identity = new String(chars);
				if (hash.containsKey(identity)) {
					hash.get(identity).add(str);
				} else {
					ArrayList<String> val = new ArrayList<String>();
					val.add(str);
					hash.put(identity, val);
				}
			}
			for (ArrayList<String> val : hash.values()) {
				if (val.size() > 1) {
					for (String s : val) {
						res.add(s);
					}
				}
			}
			return null;
		}

I was hoping the solution could be more sophisticated. However it disappointed me.
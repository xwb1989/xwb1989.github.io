---
layout: post
title: Strings Are Different
category: blog
---
#Strings Are Different

Some strings are different, even though they look like the same. See the following codes:


    public class Strings {
    	public static void main(String[] args) {
    		String s1 = "42";
    		char[] a = new char[100];
    		a[99] = '2';
    		a[98] = '4';
    		String s2 = new String(a);
    		String s3 = s2.substring(98);
    		System.out.println("s1 = " + s1);
    		System.out.println("s2 = " + s2);
    		System.out.println("s3 = " + s3);
    		System.out.println("s1.length() = " + s1.length());
    		System.out.println("s2.length() = " + s2.length());
    		System.out.println("s3.length() = " + s3.length());
    		System.out.println("s1 == s2 = " + s1.equals(s2));
    		System.out.println("s1 == s3 = " + s1.equals(s3));
    	}
    }
    
And the output: 

    s1 = 42
    s2 = 42
    s3 = 42
    s1.length() = 2
    s2.length() = 100
    s3.length() = 2
    s1 == s2 = false
    s1 == s3 = true

The reason is that in `s2` there are unprintable `ascii` characters like `NUL`.
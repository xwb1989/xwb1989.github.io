---
layout: post
title: Divide and Modulus on Negative Integer
category: blog
---
#Divide and Modulus on Negative Integer 

##They Are Different!
Don't worry I'm not yelling at the obvious fact that divdd and modulus are different operations. What I'm tented to state is that they are different implementations in Java and Python. Different but both coronet and need some notation otherwise you will make mistakes.

##On Negative Integer
They function the same on positive integers, but not on negative ones. First of all, let's make clear the definition of divide and modulus:

100 = **9** * 11 + *1*

As to the equation above, the result of 100 divided by 11 is 9 and that of 100 modulus by 11 is 1. The solutions pair of divide and modulus are unique for positive integers. But as to negative integers, things become different.

* -100 = (**-9**) * 11 + (*-1*)
* -100 = (**-10**) * 11 + (*10*)

We can see that we have 2 valid solution: (-9, -1) and (-10, 10). Which one we will see on our computer? Python and Java make different choice.
    
    public class ModulusAndDivide {
	    public static void main(String[] args) {
            System.out.println(-100 / 11);
            System.out.println(-100 % 11);
    	}
    }
    
It will give us the output:
    
    -9
    -1

But in Python
    
    print(-100/11)
    print(-100%11)
    
It will give us
    
    -10
    10
    
##Be careful
This issue is trivial but worth-notice especially when you are preparing coding interviews. Be careful and keep it in mind. 
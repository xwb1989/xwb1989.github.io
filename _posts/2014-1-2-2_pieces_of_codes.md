---
layout: post
title: 2 Pieces of Codes
category: blog
---
#2 Pieces of Codes

##Java
###Source
    public class Closure {
        static in a = 10;
        
        public static void main(String[] args) {
            bar();
            foo();
        }
        
        public static void bar() {
            System.out.println("a = " + a);
        }
        
        public static void foo() {
            a = 5;
            bar();
        }    
    }
###Output
    a = 10
    a = 5
    
##Python
###Source
  
    a = 10
    
    def bar():
        print("a = %i" %(a))
        
    def foo():
        a = 5
        bar()
  
    if __name__ == "__main__":
        bar()
        foo()

       
###Output
    a = 10
    a = 10
---
layout: post
title: Lexical Scope, Dynamic Scope and Closure
category: blog
---

#Lexical Scope, Dynamic Scope and Closure

这事其实特简单。学过课程，看过文章，翻过维基，但我还是搞错了。我脑残，所以记一下。

##你开玩笑的吧

Lexical Scope与Dynamic Scope的区别浅显易懂，但我之前不知为何老将Lexical Scope与Closure混而一谈，并闹出了各种体位的笑话：

* Java作为现代编程语言当然是使用Lexical Scope，但为何人们说他木有闭包呢...
* Python对Closure的支持并不好，所以并不是完全的lexical scope...好歹我也是上过Programming Languages这门课，甚至还自己实现过closure，所以
* 诸如此类...

##Lexical Scope与Dynamic Scope
简单来说，这两两者的区别在于：

* Lexical Scope(Static scope)：函数中有用到外部变量时，函数会从它**被定义的环境**中找它所用的变量的值；
* Dynamic Scope: 函数中有用到外部变量时，函数会先从它**被调用的环境**中找它所用到的变量的值。

举个例子，一个用python实现会看上去有点纠结的例子：

    a = 10
    def bar():
        print("a = %i" %(a))
    def foo():
        a = 5
        bar()

    if __name__ == "__main__":
        bar()
        foo()
函数`bar()`引用了外部变量`a`，在`bar()`被定义的环境中，`a`的值是`10`。在函数`foo()`中，又重新定义了`a = 5`。

需要注意，在python中，当在inner scope中给变量赋值时，默认有限会在该scope中声明一个新的变量，哪怕该变量名跟outer scope中的变量名有重复，这也导致了python中的一个经典诡异异常，也是人们为何说python对闭包的支持并不完善。

由于在outer scope中的`a`的值还是`10`，所以虽然在`foo()`调用`bar()`的时候inner scope(`foo()`)中的`a`是`5`，`bar()`依然会引用outer scope中的`a`。所以结果是：

    a = 10
    a = 10

那如果是Dynamic Scope呢？如果依然是上面这段代码，`bar()`在被调用的时候，它会优先引用它被调用的环境中的变量，也就是`a = 5`，所以结果就会是：

    a = 10
    a = 5
    
##Closure
当比而已，闭包的概念就难理解不少。上文提到Python中的诡异异常，是这样的：

    a = 10

    def foo():
        a += 1
        print("a = %i" %(a))

    if __name__ == "__main__":
        foo()

运行这段代码的时候，会抛出异常：
    
    Traceback (most recent call last):
      File "badclosure.py", line 8, in <module>
        foo()
      File "badclosure.py", line 4, in foo
        a += 1
    UnboundLocalError: local variable 'a' referenced before assignment
    
这很奇怪，因为直观来看，`foo()`的作用是将外部变量的数值`+1`，为何会说`local variable 'a' referenced before assignment`呢？

原因就是之前提到的，`a += 1`其实是`a = a + 1`，python在碰到赋值时，会在inner scope中声明`a`，这样外部的`a`就被shadowed了。由于`a + 1`需要引用`a`，但此时`a`只是被声明还未被赋值，所以就会出现错误。

这个跟closure有什么关系呢？其实我也不知道……

##Closure?
那到底什么是closure?在我的理解，closure其实可以被看作一种数据结构，可以被当作参数传递给别的函数，他包括了

* 函数体（不需要函数名，可以是**匿名函数**噢！）
* 以及该函数体被定义时，这个函数体所引用的外部变量（也就是自由变量）

当把函数当作参数的时候，传的其实不仅仅是这个函数，而是由函数体和自由变量所组成的**闭包closure**！

明白了吗？不明白？没事，你只要记得**Java (至少目前！)没有闭包**就够了（其实Java 目前用了匿名类的办法来模拟闭包，不大好，为啥呢？其实我也不懂，但你只需要记得，匿名类只能引用`final`前缀的外部变量。这怎么行！Java果然是烂语言！）...









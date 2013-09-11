---
layout: post
title: 没有想清楚就不要敲键盘
category: blog
---
#没有想清楚就不要敲键盘

##想清楚了才动手

今天想实现`Prim's Algorithm`，结果上来就犯难了：用怎样的数据结构来处理图呢？在错误的尝试和不那么好的尝试（用`2-D Array`来表示低密度的图太浪费了）后，终于想清楚了，还是要用最简单的`array of (node, neighbors)`最好。

解决图的表达问题之后，又设计的算法的具体实现——一开始什么都没想就在键盘上乱敲，结果自然是毫无意义。后来在上Liuba的课的时候在笔记本上理清了下算法的思路，才算是大概清楚要怎么写了。

##注意算法的细节：哪些数据变化哪些没有变

虽然算法的大概弄清楚了，但在实现的过程中，还是有很多地方没有注意到。比如说，当有新的`vertex`被找到后，`frontier`中会有部分`edges`需要被移除——否则会被重复算入到结果中。一开始我并没有注意到这一点，导致了程序总是无法得出正确的答案——连将所有的`vertice`找到都做不到，更不谈找到的`MST`了。当然，我觉得这也跟我的实现方法有关，换一个办法，可能就不需要考虑这个问题了。

__2013-09-11__更新：
我用`Python`实现了一个`Vertex`类来表示节点，但由于没有弄清楚`Python`的类实现细节，犯了大错误，浪费了很多时间。我原本的代码是这样的：

{%    highlight python    %}
class Vertex:
    found = False
    neighbor = []

{%    endhighlight    %}

结果，在这样的类设计中，`found`和`neighbor`都成了类的状态，而非对象的属性——所有由这个类建立的对象都会共享这两个值——结果自然是：一个`vertex`被找到后，所有的`vertex`都会被标记为`vertex.found = True`，同时，所有的`vertex`都会在别的`vertex.neighbor`里。太糟糕了！

正确的设计应该是这样的：

{%    highlight python    %}
class Vertex:
    def __init__(self):
        self.found = False
        self.neighbor = []

{%    endhighlight    %}
这样，每个`Vertex`对象才会都有自己独立的`found`跟`neighbor`。

##总结
简而言之，无论看上去多么简单的问题，都应该先想清楚了才动手。不单想清楚大概的思路，还要想清楚算法涉及的数据到底是如何变化的，并在自己的实现中考虑到这点。
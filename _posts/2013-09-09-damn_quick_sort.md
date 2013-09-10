---
layout: post 
title: 总是写不对的快排
category: blog
---

#总是写不对的快排

##Exam 0错误的partition

虽然Exam 0班上的平均分是57，我拿了90，不过这个不算入期末成绩，也没啥意义。比较蛋疼的是，过了这么久，我还是没有办法轻松地把快排写对——何况Exam 0的最后一题还只是要实现快排的`partition`这一部分。课后拿java写了下，居然还是花费了不少功夫——比如说在递归函数中忘记加入`base condition`。实在是太令人沮丧了。

##一错再错
今天看coursera的alto 2，作业中又需要实现一个排序算法——于是理所当然地，我又写了一遍快排。受到`C`中的

    void qsort(void *base, size_t items, size_t size, int (\*compar)(const void \*, const void\*))
    
的启发，我打算写一个`generic`的快排——不同与`python`本身的快排只能接受有限的数据类型，我想能够像`C`一样，通过传入`compare`函数，从而可以接受不同的数据类型，进行自定义的排序。

于是，我设计的接口是
{% highlight python %}
def q_sort(data, l, r, rule): 
     if l >= r: 
         return 
     else: 
         pivot = data[r] 
         i = j = l 
         while(j < r): 
             if rule(data[j], pivot): 
                 data[i], data[j] = data[j], data[i] 
                 i += 1 
             j += 1 
         data[r], data[i] = data[i], data[r] 
         q_sort(data, l, i-1, rule) 
         q_sort(data, i, r, rule) 
         return 
{% endhighlight %}

其中`rule`是判断顺序的函数，结果岔子就出在这里：没有仔细考虑重复元素的处理（跟`pivot`同样的元素一样要`swap`），导致了无穷递归。一开始我还以为是`Pthyon`的递归层数限制（通常是1000），但在设置了`sys.setrecursionlimit()`之后依然无法正常运行——结果花了几个小时才看出来真正问题在什么地方。

##总结
有两个方面可以总结。

* 一是自己对算法考虑得不够仔细，这是问题的根源；

* 二则是自己对debug还太缺乏经验和能力。虽然之前在coursera上过一门课降到了`unit test`，但自己并没有深入了解，也至今不会使用什么测试工具，这直接导致了debug的效率及其低下——只能依赖`print`来乱枪打鸟。
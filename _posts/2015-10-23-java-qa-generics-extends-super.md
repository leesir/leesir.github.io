---
layout: post
title: "泛型中&lt;? super T>和&lt;? extends T>的区别"
description: ""
category: [译文]
tags: [Q&A]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java)

　　我们会经常发现List&lt;? super T>、Set&lt;? extends T>的声明，是什么意思呢？&lt;? super T>表示包括T在内的任何T的父类，&lt;? extends T>表示包括T在内的任何T的子类，下面我们详细分析一下两种通配符具体的区别。

# extends

　　List&lt;? extends Number> foo3的通配符声明，意味着以下的赋值是合法的：

{% highlight java %}
// Number "extends" Number (in this context)
List<? extends Number> foo3 = new ArrayList<Number>();  
// Integer extends Number
List<? extends Number> foo3 = new ArrayList<Integer>(); 
// Double extends Number
List<? extends Number> foo3 = new ArrayList<Double>();  
{% endhighlight %}

<!-- more -->

　　读取操作

　　通过以上给定的赋值语句，你一定能从foo3列表中读取到的元素的类型是什么呢？

* 你可以读取到Number，因为以上的列表要么包含Number元素，要么包含Number的类元素。
* 你不能保证读取到Integer，因为foo3可能指向的是List&lt;Double>。
* 你不能保证读取到Double，因为foo3可能指向的是List&lt;Integer>。

　　写入操作

　　通过以上给定的赋值语句，你能把一个什么类型的元素合法地插入到foo3中呢？

* 你不能插入一个Integer元素，因为foo3可能指向List&lt;Double>。
* 你不能插入一个Double元素，因为foo3可能指向List&lt;Integer>。
* 你不能插入一个Number元素，因为foo3可能指向List&lt;Integer>。
* 你不能往List&lt;? extends T>中插入任何类型的对象，因为你不能保证列表实际指向的类型是什么，你并不能保证列表中实际存储什么类型的对象。唯一可以保证的是，你可以从中读取到T或者T的子类。

# super

　　现在考虑一下List&lt;? super T>。List&lt;? super Integer> foo3的通配符声明，意味着以下赋值是合法的：

{% highlight java %}
// Integer is a "superclass" of Integer (in this context)
List<? super Integer> foo3 = new ArrayList<Integer>();  
// Number is a superclass of Integer
List<? super Integer> foo3 = new ArrayList<Number>();   
// Object is a superclass of Integer
List<? super Integer> foo3 = new ArrayList<Object>();   
{% endhighlight %}

　　读取操作

　　通过以上给定的赋值语句，你一定能从foo3列表中读取到的元素的类型是什么呢？

* 你不能保证读取到Integer，因为foo3可能指向List&lt;Number>或者List&lt;Object>。
* 你不能保证读取到Number，因为foo3可能指向List&lt;Object>。
* 唯一可以保证的是，你可以读取到Object或者Object子类的对象（你并不知道具体的子类是什么）。

　　写入操作

　　通过以上给定的赋值语句，你能把一个什么类型的元素合法地插入到foo3中呢？

* 你可以插入Integer对象，因为上述声明的列表都支持Integer。
* 你可以插入Integer的子类的对象，因为Integer的子类同时也是Integer，原因同上。
* 你不能插入Double对象，因为foo3可能指向ArrayList&lt;Integer>。
* 你不能插入Number对象，因为foo3可能指向ArrayList&lt;Integer>。
* 你不能插入Object对象，因为foo3可能指向ArrayList&lt;Integer>。

# PECS

请记住PECS原则：生产者（Producer）使用extends，消费者（Consumer）使用super。

1.	生产者使用extends
如果你需要一个列表提供T类型的元素（即你想从列表中读取T类型的元素），你需要把这个列表声明成&lt;? extends T>，比如List&lt;? extends Integer>，因此你不能往该列表中添加任何元素。
2.	消费者使用super
如果需要一个列表使用T类型的元素（即你想把T类型的元素加入到列表中），你需要把这个列表声明成&lt;? super T>，比如List&lt;? super Integer>，因此你不能保证从中读取到的元素的类型。
3.	即是生产者，也是消费者
如果一个列表即要生产，又要消费，你不能使用泛型通配符声明列表，比如List&lt;Integer>。

# 例子

　　请参考java.util.Collections里的copy方法：

![collections_copy_code]({{ site.baseurl }}/images/post_2015_10_23_image1.png)

　　我们可以从Java开发团队的代码中获得到一些启发，copy方法中使用到了PECS原则，实现了对参数的保护。
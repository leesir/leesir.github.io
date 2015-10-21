---
layout: post
title: "Function接口 – Java8中java.util.function包下的函数式接口"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2013/04/function-interface-a-functional-interface-in-the-java-util-function-package-in-java-8.html) 作者: [Mohamed Sanaulla](http://www.javacodegeeks.com/author/Mohamed-Sanaulla/)

　　早先我写了一篇[《函数式接口》](http://blog.sanaulla.info/2013/03/21/introduction-to-functional-interfaces-a-concept-recreated-in-java-8/)，探讨了Java8中函数式接口的用法。如果你正在浏览Java8的API，你会发现java.util.function中 Function, Supplier, Consumer, Predicate和其他函数式接口广泛用在支持lambda表达式的API中。这些接口有一个抽象方法，会被lambda表达式的定义所覆盖。在这篇文章中，我会简单描述Function接口，该接口目前已发布在java.util.function中。

　　Function接口的主要方法：
{% highlight java %}
R apply(T t) //将Function对象应用到输入的参数上，然后返回计算结果。

default <V> Function<T,V> //将两个Function整合，并返回一个能够执行两个Function对象功能的Function对象。
{% endhighlight %}

<!-- more -->

　　译者注：Function接口中除了apply()之外全部接口如下：
{% highlight java %}
default <V> Function<T,V> andThen(Function<? super R,? extends V> after) //返回一个先执行当前函数对象apply方法再执行after函数对象apply方法的函数对象。

default <V> Function<T,V> compose(Function<? super V,? extends T> before) //返回一个先执行before函数对象apply方法再执行当前函数对象apply方法的函数对象。

static <T> Function<T,T> identity() //返回一个执行了apply()方法之后只会返回输入参数的函数对象。
{% endhighlight %}
　　本章节将会通过创建接受Function接口和参数并调用相应方法的例子探讨apply方法的使用。我们同样能够看到API的调用者如何利用lambda表达式替代接口的实现。除了传递lambda表达式之外，API使用者同样可以传递方法的引用，但这样的例子不在本篇文章中。

　　如果你想把接受一些输入参数并将对输入参数处理过后的结果返回的功能封装到一个方法内，Function接口是一个不错的选择。输入的参数类型和输出的结果类型可以一致或者不一致。一起来看看接受Function接口实现作为参数的方法的例子：

    {% highlight java %} 
public class FunctionDemo {

    //API which accepts an implementation of

    //Function interface

    static void modifyTheValue(int valueToBeOperated, Function<Integer, Integer> function){

        int newValue = function.apply(valueToBeOperated);

        /*     
         * Do some operations using the new value.     
         */

        System.out.println(newValue);

    }

}
    {% endhighlight %}
	
　　下面是调用上述方法的例子：

    {% highlight java %} 
public static void main(String[] args) {

    int incr = 20;  int myNumber = 10;

    modifyTheValue(myNumber, val-> val + incr);

    myNumber = 15;  modifyTheValue(myNumber, val-> val * 10);

    modifyTheValue(myNumber, val-> val - 100);

    modifyTheValue(myNumber, val-> "somestring".length() + val - 100);

}
    {% endhighlight %}
	
　　你可以看到，接受1个参数并返回执行结果的lambda表达式创建在例子中。这个例子的输入如下：

    {% highlight java %} 
30

150

-85

-75
    {% endhighlight %}
---
layout: post
title: "Java中函数式编程的谓词函数(Predicates)第一部分"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2012/05/functional-style-in-java-with.html) 作者: [Cyrille Martraire](http://www.javacodegeeks.com/author/Cyrille-Martraire/)

　　你一直在听说函数式编程将称霸整个编程届，而自己仍然沉浸在普通的Java里？请不要担心，因为你已经在日常Java代码中加入了函数式编程的特性。此外，函数式编程很有趣，能够帮你节省多行代码并且降低错误率。

#什么是谓词函数？

　　许久之前，那时我还在用Java 1.4进行编码，当我第一次发现Apache Commons Collections，便爱上了谓词函数。Apache Commons Collections里的谓词函数仅仅只是一个只有一个方法的接口：

    {% highlight java %} 
evaluate(Object object): boolean
    {% endhighlight %}
	
　　这就是谓词函数，输入一个对象，返回true或者false。最近诞生了类似Apache Commons Collections的持有Apache 2.0许可的Google Guava。在Google Guava中，定义了Predicate接口，该接口包含一个带有泛型参数的方法：
	
	{% highlight java %} 
apply(T input): boolean
    {% endhighlight %}
	
　　如果想在程序中使用谓词函数，只需要利用自己的逻辑实现该接口即可。
	
<!-- more -->

#一个简单的例子

　　先举一个例子，假设你有一个订单列表，每个订单用PurchaseOrder表示，PurchaseOrder中包含日期，顾客和状态。不同的用例会要求你有不同的输出，比如获取某个顾客所有、等待发货、已发货、已交付或者过去一个小时内完成的订单。当然你可以在循环中使用if判断实现这些功能：

	{% highlight java %} 
//List<PurchaseOrder> orders...
public List<PurchaseOrder> listOrdersByCustomer(Customer customer) {
    final List<PurchaseOrder> selection = new ArrayList<PurchaseOrder>();
    for (PurchaseOrder order : orders) {
        if (order.getCustomer().equals(customer)) {
            selection.add(order);
        }
    }
    return selection;
}
    {% endhighlight %}
	
　　以上是获取某个顾客所有订单的代码。不同的功能需要编写多个类似的循环：

	{% highlight java %} 
public List<PurchaseOrder> listRecentOrders(Date fromDate) {
        final List<PurchaseOrder> selection = new ArrayList<PurchaseOrder>();
        for (PurchaseOrder order : orders) {
            if (order.getDate().after(fromDate)) {
                selection.add(order);
            }
        }
        return selection;
}
    {% endhighlight %}
	
　　这些重复代码非常明显：除了if的判断条件之外没有任何差异（译者注：方法参数可归为判断条件）。采用谓词函数的思想在于，利用传入到函数内的谓词的调用替代if语句块里的硬编码的判断条件。这意味着，你只需编写一遍带有谓词函数作为参数的方法，就可以覆盖所有的甚至你还不知道的测试用例：

	{% highlight java %} 
public List<PurchaseOrder> listOrders(Predicate<PurchaseOrder> condition) {
    final List<PurchaseOrder> selection = new ArrayList<PurchaseOrder>();
    for (PurchaseOrder order : orders) {
        if (condition.apply(order)) {
            selection.add(order);
        }
    }
    return selection;
}
    {% endhighlight %}
	
　　如果需要考虑到复用，可以把谓词函数声明成一个单独的类，否则可以把谓词声明成匿名类：

	{% highlight java %} 
final Customer customer = new Customer("BruceWaineCorp");
final Predicate<PurchaseOrder> condition = new Predicate<PurchaseOrder>() {
    public boolean apply(PurchaseOrder order) {
        return order.getCustomer().equals(customer);
    }
};
    {% endhighlight %}
	
　　如果你的使用过真正意义上的函数式语言（Scala, Clojure, Haskell等）的朋友看到这些代码，可能会觉得在处理通用功能时代码显得非常冗余。然而我们已经习惯于Java冗长的语法，并且我们有强大的工具（自动补齐、重构）帮助我们适应它，这使得我们的Java项目无法一夜之间转变成其他语言的项目。

#谓词函数是集合类的好朋友

　　回到之前的例子，我们写了一个覆盖了所有用例的循环，我们为共性的抽离感到开心，但是你的朋友依然会嘲笑你。幸运的是，Apache或者Google的API都提供了你想要的东西，还特别提供了一个类似java.util.Collections的命名为Collections2的类（名字不是很新颖）。

　　这个类提供给了与我们先前编写的代码功能类似的filter()函数，所以我们可以把方法重构成无循环的版本：

	{% highlight java %} 
public Collection<PurchaseOrder> selectOrders(Predicate<PurchaseOrder> condition) {
    return Collections2.filter(orders, condition);
}
    {% endhighlight %}

　　实际上，这个方法返回了一个过滤后的视图：

　　返回的集合是未经过滤的集合（输入的集合）的真实缩影（译者注：先前版本的函数返回的集合是输入集合的一个子集的拷贝），更改其中一个集合会影响另一个集合。

　　这意味着这种方式将使用更少的内存，因为不会把原始集合的内容拷贝到返回的集合中。

　　在一个类似的场景中，我们可以要求返回在给定的迭代器之上过滤好的只符合谓词函数的元素的迭代器（装饰模式）。

	{% highlight java %} 
Iterator filteredIterator = Iterators.filter(unfilteredIterator, condition);
    {% endhighlight %}
	
　　从Java 5开始，Iterable接口和循环使用起来非常方便，所以我们更倾向于使用以下写法：

	{% highlight java %} 
public Iterable<PurchaseOrder> selectOrders(Predicate<PurchaseOrder> condition) {
    return Iterables.filter(orders, condition);
}
// you can directly use it in a foreach loop, and it reads well:
for (PurchaseOrder order : orders.selectOrders(condition)) {
    //...
}
    {% endhighlight %}
	
#现成的谓词函数

　　为了使用谓词，我们必须声明自己的谓词接口，或者为应用程序中使用到的谓词参数都声明一个类。这是可行的，然而从类似Guava以及Commons的API中使用标准谓词接口的好处是：你可以结合这类API提供的大量优秀组件实现你自己的谓词函数。

　　如果你需要的是判断一个对象是否为空或者不为空的条件，你不需要自己实现一个谓词函数，只需要使用现成的谓词就可以了：

	{% highlight java %} 
// gives you a predicate that checks if an integer is zeroPredicate
<Integer> isZero = Predicates.equalTo(0);
// gives a predicate that checks for non null objects
Predicate<String> isNotNull = Predicates.notNull();
// gives a predicate that checks for objects that are instanceof the given Class
Predicate<Object> isString = Predicates.instanceOf(String.class);
    {% endhighlight %}
	
　　对于给定的谓词，你可以反转它（返回相反的返回值，比如true变成false）：

	{% highlight java %} 
Predicates.not(predicate);
    {% endhighlight %}
	
　　利用AND，OR操作结合多个谓词：

	{% highlight java %} 
Predicates.and(predicate1, predicate2);
Predicates.or(predicate1, predicate2);
// gives you a predicate that checks for either zero or null
Predicate<Integer> isNullOrZero = Predicates.or(isZero, Predicates.isNull());
    {% endhighlight %}
	
　　当然你也可以拥有返回固定值（true或者false）的特殊谓词（译者注：只返回true即为逻辑学中的永真式，反之为永假式）。这些谓词非常有用，我们可以在之后的例子中证明：

	{% highlight java %} 
Predicates.alwaysTrue();
Predicates.alwaysFalse();
    {% endhighlight %}
	
#如何定位谓词

　　起先，我经常编写匿名的谓词类，后来这些谓词总是频繁使用，所以我会将匿名的谓词升级成实体类、内部类等。

　　顺便提一下，如何定位谓词呢？请参考Robert C. Martin的文章 [Common Closure Principle (CCP)](http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf)中提到的一段话 :

　　一起变化的类，属于一个整体。

　　因为谓词总是对一个特定类型的对象进行操作，我喜欢将谓词重新定位为谓词操作的参数的类型。比如，类CustomerOrderPredicate，PendingOrderPredicate 和RecentOrderPredicate 应该被防止在同一个包下或者子包下（如果你有很多包），而不是把代码写到这些谓词所操作的主体PurchaseOrder里。另一个选择是，将谓词声明成它们要操作的主体类型的内部类。显然，谓词与主体对象是非常耦合的。

#资源

　　这里有本篇文章的例子源代码：[cyriux_predicates_part1 (zip)](http://cyrille.martraire.com/wp-content/uploads/2010/11/cyriux_predicates_part1.zip)

　　在下一小节，我们着重观察谓词函数如何简化测试、谓词如何与域驱动设计里的标准相联系，以及一些能让你高效利用谓词函数的额外知识。

#参考文献

[A touch of functional style in plain Java with predicates – Part 1](http://cyrille.martraire.com/2010/11/a-touch-of-functional-style-in-plain-java-with-predicates-part-1/) from our [JCG partner](http://www.javacodegeeks.com/p/jcg.html) Cyrille Martraire at the [Cyrille Martraire’s blog](http://cyrille.martraire.com/)
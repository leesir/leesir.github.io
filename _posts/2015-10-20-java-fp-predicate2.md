---
layout: post
title: "Java中函数式编程的谓词函数(Predicates)第二部分"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2012/05/functional-style-in-java-with_23.html) 作者: [Cyrille Martraire](http://www.javacodegeeks.com/author/Cyrille-Martraire/)

　　在上一篇文章中我们介绍了谓词函数。通过一个简单的只带一个返回值是true或者false的函数的接口，把函数式编程语言的优势带入到了类似Java的面向对象编程语言中。这一小节，我们将会介绍一些高级特性，方便你高效利用谓词函数。

#测试

　　在测试代码中使用谓词的优势尤为明显。当你需要测试一个混合了数据结构与某些条件逻辑的方法时，通过使用谓词，你可以先单独测试数据结构，再测试条件逻辑。

　　第一步，先利用永真谓词或者永假谓词屏蔽用于判断的逻辑，将注意力集中在测试数据结构上：

    {% highlight java %} 
// check with the always-true predicate

final Iterable<PurchaseOrder> all = orders.selectOrders(Predicates.<PurchaseOrder> alwaysTrue());

assertEquals(2, Iterables.size(all)); 

// check with the always-false predicat

eassertTrue(Iterables.isEmpty(orders.selectOrders(Predicates.<PurchaseOrder> alwaysFalse())));
    {% endhighlight %}
	
　　第二步，你可以分开测试每个谓词了：
	
	{% highlight java %} 
final CustomerPredicate isForCustomer1 = new CustomerPredicate(CUSTOMER_1);

assertTrue(isForCustomer1.apply(ORDER_1)); // ORDER_1 is for CUSTOMER_1

assertFalse(isForCustomer1.apply(ORDER_2)); // ORDER_2 is for CUSTOMER_2
    {% endhighlight %}
	
　　例子简单，却见微知著。为了测试更加复杂的逻辑，你可能需要构建模拟谓词应对测试不够充分的情况。比如，一个第一次返回true，之后都返回false的谓词。通过严格分离关注点，谓词可以大大简化你的测试计划。

　　如果你想测试驱动开发（TDD），谓词是一个不错的选择，如果你能通过测试的方式影响你的设计，之后你便会发现谓词很容易就融入到你的设计中。
	
<!-- more -->

#向你所在的团队推广谓词函数

　　在我工作过的项目组中，一开始团队总是不熟悉谓词函数。然而谓词的概念是非常有趣的，也很容易让其他团队成员所接收。事实上，我并没有肆意宣传，谓词的思想在我往同事的代码里添加了代码之后便悄悄传播开来，对此我很惊讶。我猜使用谓词函数的好处不言自明，一些比较成熟的类似Apache和Google的API同样证明了谓词是一个比较严肃的话题，而如今正是函数式编程最火爆的时期，谓词函数这类函数式语言的特性更加容易推销了。

#简单的优化

　　为了防止谓词在线程间共享数据，尽可能将谓词设计成不可变和无状态是常规优化手段之一。这项优化可以使整个进程共享一个谓词实例（单例，比如一个谓词常量）。如果有需要，可以将编译期间无法枚举出来的最常用的谓词在运行时缓存起来（同往常一样，只有在性能分析报告中真正要求了缓存之后，你才需要做这项工作）。

　　如果可以的话，尽可能将计算所需的资源提前在构造函数中加载好（这是线程安全的操作），否则使用懒加载的方式。

　　谓词应当是“无副作用的”，换句话说，是只读的：谓词的执行不应该修改系统内的任何状态。某些比如基于计数的分页谓词必须维护自己的内部状态，但是它们依然不能更改所依赖的系统状态。除非内部状态可以在每次调用之间被重置，其他情况下这些状态是不能被公用的。

#细粒度接口：让你的谓词函数拥有更多的使用者

　　在大型应用程序中，你发现自己经常为完全不同的主体类型编写了引用类似Customer的通用属性的相似谓词。比如，在管理员页面，你想过滤出某个客户的日志，在CRM页面，你想过滤出某个客户的吐槽。对于每个特定类型X，你同样需要一个相关联的谓词CustomerXPredicate进行客户级别的过滤。因为每个类型在某些方面都与Customer关联，我们可以将Customer抽离出来，把它声明在只有一个方法的CustomerSpecific 接口里：

	{% highlight java %} 
public interface CustomerSpecific {   

    Customer getCustomer();

}
    {% endhighlight %}
	
　　这个接口除了不能重用实现类，细粒度的特点让我想起在某些语言特征。接口使得getCustomer() 方法能够做到无视类型调用，所以同样可以将其看做是在静态类型语言中引进动态类型的方式。

　　一旦我们定义了CustomerSpecific接口，我们可以通过实现它定义我们的谓词，不需要像之前一样根据主体类型定位谓词。这项优化在一个大型项目中仅仅只是影响了一小部分的谓词类。在这种情况下，CustomerPredicate通过实现CustomerSpecific进行重定位，并且泛型类型也是CustomerSpecific。

	{% highlight java %} 
public final class CustomerPredicate implements Predicate<CustomerSpecific>, CustomerSpecific {  

    private final Customer customer;  // valued constructor omitted for clarity  

    public Customer getCustomer() {    

        return customer;

    }  

    public boolean apply(CustomerSpecific specific) {   
 
        return specific.getCustomer().equals(customer);  

    }

}
    {% endhighlight %}
	
　　注意到例子中的谓词可以实现CustomerSpecific，所以同样可以针对自身做evaluate计算（译者注：即调用谓词的apply或者evaluate等方法）。当使用这类特征模拟（译者注：在静态语言中模拟动态语言特有的特性）的接口时，需要特别注意对泛型的设计，比如在类PurchaseOrders中接收Predicate<PurchaseOrder>作为参数的方法，需要稍作修改，使该方法能够接收PurchaseOrder任何父类的谓词：

	{% highlight java %} 
public Iterable<PurchaseOrder> selectOrders(Predicate<? super PurchaseOrder> condition) { 
  
    return Iterables.filter(orders, condition);

}
    {% endhighlight %}
	
#域驱动设计(Domain-Driven Design)规范

　　Eric Evans和Martin Fowler一起写了《模式规范》（译者注：文章链接已经不存在），很明显文章描述的就是谓词。实际上“谓词”是逻辑编程中的名词，模式规范旨在说明我们如何把逻辑编程中的强大功能应用到面向对象编程中。

　　在域驱动设计一书中，Eric Evans详细说明并给出了描述域模型的规范的例子。如同书中描述的一样，Policy pattern无非就是Strategy pattern（译者注：策略模式），也即在某种程度上，Specification pattern（译者注：规格模式）会选择带有额外意图能够标识业务规则的不同版本的谓词指派给域切面。

　　Specification pattern建议的方法名为：isSatisfiedBy(T): boolean，该方法强调关注域的约束。正如我们前面看到的谓词，于 Interpreter pattern中类似，封装在Specification对象中的原子业务逻辑可以通过布尔逻辑重组（or, and, not, any, all）。这本书还描述了如数据库查询优化、归类等高级技术。

#查询优化

　　接下来的内容是一些优化的技巧，我并不确定你是否会在工作中用到。确实，谓词在过滤数据集的时候显得有点笨拙：谓词必须为集合中每个元素都做evaluate操作，当集合非常庞大的情况下，可能会出现性能问题。对于一个给定的谓词，如果数据都存储在数据库中，通过此谓词一个接一个地获取庞大集合中的元素并不是一个非常好的主意。

　　当你遇到了性能问题，你开始分析并且找到了性能瓶颈。如果现在调用谓词过滤数据结构中每个元素是瓶颈的话，你该如何修复这个问题呢？

　　删除所有谓词，把代码还原成硬编码的、更容易出错、重复率高以及测试难度较高的形式是一种优化手段。但只要我能找到更好的可选方案（总是能找到许多），我会拒绝此项优化。

　　首先，仔细观察代码是如何使用的。根据域驱动设计的思想，当出现问题时，应当系统审查域对象。

　　通常在系统中，总是会使用一些清晰的模式。经统计，这些模式为优化提供了许多可能。比如我们的PurchaseOrders 类，在我们假设的例子中，由于业务上的关系，获取等待处理的订单将会比其他情况多上许多。

#Friend Complicity

　　基于不同的使用模式，你可能会编写可选实现类进行某些特定的优化。在我们的例子中，用户的待处理订单经常会被查询，我们为此编写一个可选实现类FastPurchaseOrder，该类利用一些预先计算好的数据结构提高查询待处理订单的速度。

　　现在，为了从这个可选优化实现中受益，你也许会修改接口，增加一个专用方法，比如selectPendingOrders()。在此之前，接口中只有一个泛型方法，添加额外方法在某些方面是正确的，但同样会引入一些问题：你必须在其他实现类中也实现这些方法，虽然方法只适用于部分场合而稍显不合理。

　　为了让原先只接受一个谓词参数的方法在内部自行优化的技巧之一，便是让方法与谓词耦合起来。我借鉴了C++中friend关键字，把这种方式成为“Friend Complicity”。

	{% highlight java %} 
/** Optimization method: pre-computed list of pending orders */

private Iterable<PurchaseOrder> selectPendingOrders() {

    // ... optimized stuff...

}

public Iterable<PurchaseOrder> selectOrders(Predicate<? super PurchaseOrder> condition) {

    // internal complicity here: recognize friend class to enable optimization

    if (condition instanceof PendingOrderPredicate) {

        return selectPendingOrders();// faster way

    }

    // otherwise, back to the usual case

    return Iterables.filter(orders, condition);

}
    {% endhighlight %}
	
　　很明显，这种方式提高了不同类之间的耦合度，这些类理应互不依赖。同时，只能在直接提供“friend”谓词的情况下提高性能，不能带有装饰或者组合模式。

　　Friend Complicity最重要的一点是，确保这些特定方法不做任何妥协，需要在任何时候都遵循接口的规范（即使在不需要性能提升的情况下）。同时，请记住，将来或许有一天你可能会将实现变更会未优化的实现版本。

#SQL-compromised

　　如果订单存储在数据库中，可以通过SQL快速查询。顺便提一下，你可能已经注意到谓词正是你的SQL中where子句后的查询条件。

　　一个简单的依旧使用谓词提升性能的方式是，实现额外的接口SqlAware，该接口带有一个SQLasSQL()方法，方法返回谓词实际执行时操作数据库的SQL。当对数据库使用这类谓词时，谓词会直接使用SQL查询并返回结果，不会执行常规evaluate或者apply 方法。

　　我把这种谓词入侵数据库底层细节的方法称为SQL妥协，通常情况下谓词不应该这么做。

#归类

　　归类是一种描述了概念间包含关系的逻辑概念。比如，红，绿，黄，包含于术语“色彩”中。谓词间的归类可以成为代码中非常强大的实现工具。

　　我们来举一个广播股票价格的应用程序的例子。在注册的时候，我们必须声明对哪类股票的观察比较感兴趣。我们只需要简单传递当操作到我们感兴趣的股票时返回true的股票类谓词：

	{% highlight java %} 
public final class StockPredicate implements Predicate<String> {

    private final Set<String> tickers;

    // Constructors omitted for clarity

    public boolean apply(String ticker) {

        return tickers.contains(ticker);

    }

}
    {% endhighlight %}
	
　　现在我们假设应用程序已经可以利用消息主题广播标准的股票代码集合，每一个主题都拥有相应的谓词。如果检测到我们将要使用的谓词，是被包含于或者被归类为某个标准谓词，我们就可以订阅它。在我们的例子中，归类是一个非常简单的工作，只需要在我们的谓词中添加额外的方法即可：

	{% highlight java %} 
public boolean encompasses(StockPredicate predicate) {

    return tickers.containsAll(predicate.tickers);

}
    {% endhighlight %}
	
　　归类操作主要关注谓词间的包含关系。就如例子中一样，基于集合设计的谓词非常容易实现归类操作，基于内部数字和日期实现的谓词也同样如此。否则，你可能需要求助于“Friend Complicity”，需要了解其他谓词以便判定谓词间的包含关系。

　　总之，在通常情况下，归类是难以实现的。但即便只是实现了部分归类，也能带来巨大的价值。所以说归类是一个很重要的工具。

#总结

　　谓词很有趣，能够提升代码质量，改进思维模式。

　　源代码路径： [cyriux_predicates_part2.zip (fixed broken link)](http://cyrille.martraire.com/wp-content/uploads/2010/11/cyriux_predicates_part2.zip)

#参考文献

　　[A touch of functional style in plain Java with predicates – Part 2](http://cyrille.martraire.com/2010/11/a-touch-of-functional-style-in-plain-java-with-predicates-%E2%80%93-part-2/) from our [JCG partner](http://www.javacodegeeks.com/p/jcg.html) Cyrille Martraire at the [Cyrille Martraire’s blog](http://cyrille.martraire.com/).
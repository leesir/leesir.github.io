---
layout: post
title: "包装类型在条件运算中的使用不当造成的空指针异常"
description: ""
category: [Summary]
tags: [Summary]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

虽然已有几年开发经验，但是工作中发现，这程序中的坑啊，踩的也真是不亦乐乎，很是惭愧。

即使是最最基础的Java语言本身的特性，也有点儿说不清道不明了。大家都曾努力学习过，都正在努力地工作，为什么一些错误总是一而再再而三的犯呢？我想这不是个人基础的问题，而是没有认真总结与回顾。

所以，即日起，小弟我会把工作上学习中遇到的问题都记录下来，可能有些问题会很白痴，然而不惧贻笑大方方能进步。

<!-- more -->

##进入正题

这第一个问题便是：

```
包装类型在条件运算中的使用不当造成的空指针异常。
```

我们曾在教科书上学到过，条件表达式

{% highlight java %}
b ? x : y;
{% endhighlight %}

等价于

{% highlight java %}
if (b) {
  x;
} else {
  y;
}
{% endhighlight %}
所以

{% highlight java %}
int a = b ? x : y
{% endhighlight %}

等价于

{% highlight java %}
int a;
if (b) {
  a = x;
} else {
  a = y;
}
{% endhighlight %}

接下来介绍我的发现。

{% highlight java %}
private static void testBoxedTypeWithIf() {
    InnerClass innerClass = new InnerClass();
    Long idValue;
    if (flag) {
        idValue = innerClass.getId();
    } else {
        idValue = 0L;
    }
    System.out.println(idValue);
}
{% endhighlight %}
flag的值为true，所以程序毫无疑问会打印出null。InnerClass的简化定义如下：

{% highlight java %}
private static class InnerClass {
    private Long id;
    
    Long getId() {
        return this.id;
    }
}
{% endhighlight %}

根据if判断和条件运算符的等价关系，我在程序中用了如下的方式实现逻辑：

{% highlight java %}
private static void testBoxedTypeWithConditionalOperation() {
    InnerClass innerClass = new InnerClass();
    Long idValue = flag ? innerClass.getId() : 0L;
    System.out.println(idValue);
}
{% endhighlight %}
如果使用一样的测试用例，肯定有人会认为这段程序也输出null，至少之前的我是这么认为的。这段程序运行的结果是抛出空指针异常，我们来看看testBoxedTypeWithConditionalOperation方法和testBoxedTypeWithIf方法的反编译代码:

{% highlight java %}
private static void testBoxedTypeWithConditionalOperation() {
    InnerClass innerClass = new InnerClass(null);
    Long idValue = Long.valueOf(flag ? innerClass.getId().longValue() : 0L);
    System.out.println(idValue);
}

private static void testBoxedTypeWithIf() {
    InnerClass innerClass = new InnerClass(null);
    Long idValue;
    Long idValue;
    if (flag)
      idValue = innerClass.getId();
    else {
      idValue = Long.valueOf(0L);
    }
    System.out.println(idValue);
}
{% endhighlight %}
可以看到，testBoxedTypeWithConditionalOperation中将条件运算符中的包装类型解包装了，在testBoxedTypeWithIf中将原始类型包装了。

在我以往的认知中，包装类型与原始类型的自动包装与解包装的操作，就像在testBoxedTypeWithIf中，是在这些变量之间有直接的比较、复制或者计算（如加减乘除）的时候。然而在条件运算符中，即使看似没有直接的关系，编译器依然对包装类型做了解包装的操作。

所以，在包装类型与条件运算符混用的时候，要么用if判断替代条件运算符，要么在条件运算符的判断条件中先对包装类型是否为空做判断，做法如下：

{% highlight java %}
private static void testBoxedTypeWithCorrectConditionalOperation() {
    InnerClass innerClass = new InnerClass();
    Long idValue = flag && innerClass.getId() != null ? innerClass.getId() : 0L;
    System.out.println(idValue);
}
{% endhighlight %}
对于本例中用的测试用例，即flag == true，testBoxedTypeWithCorrectConditionalOperation将输出0。

通过反编译代码发现，条件运算符与if判断只是在程序的执行路径上保持一致，具体的执行细节，因不同编程语言而异。而今Java前端与后端编译器越来越聪明，对程序员来说不见得是一件好事儿。
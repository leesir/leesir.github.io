---
layout: post
title: "Java语法糖-泛型-泛型边界与通配符"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 有界泛型

&#160; &#160; &#160; &#160;当我们需要一个工具类只用于做数值计算，或者只用于做集合的遍历，方法只希望接收Number或者Collection，不希望接受其他类型，否则就得写一大堆instanceof的if判断。
为了解决这个问题，我们需要定义泛型的上边界，即方法所能接收的层级最高的类型。

&#160; &#160; &#160; &#160;在Java中，用extends关键字可以实现有界泛型的定义。格式为：<T extends UpperBoundClass>，其中UpperBoundClass为上边界类型。代码实例如下所示：

{% highlight java %}
public static <T extends Number> void computeNumber(T number1, T number2) {
    //compute
}

public static <T extends Collection> void iter(T collection) {
    //iterate
}
{% endhighlight %}

{:.center}
代码清单1

<br>

&#160; &#160; &#160; &#160;在使用上述工具方法的时候，可以这么使用：

{% highlight java %}
//compile success
Integer number1 = 0;
BigDecimal number2 = new BigDecimal("2");
computeNumber(number1, number2);
iter(new ArrayList<>(Arrays.asList("1", "2", "a")));
iter(new HashSet<>(Arrays.asList("1", "2", "a")));

//compile fail: Wrong 1st argument type
computeNumber("string1", number2);
{% endhighlight %}

{:.center}
代码清单2

<br>

&#160; &#160; &#160; &#160;可见，只要定义了边界，泛型方法就可以同时处理任意子类。而当传入不属于边界类型的子类时，将会出现编译时的参数类型错误。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
[3] [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3)[EB/OL].https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3，2019-07-04.<br>
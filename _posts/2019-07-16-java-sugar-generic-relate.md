---
layout: post
title: "Java语法糖-泛型-类关系"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;在考虑泛型类之间关系之前，我们需要熟悉以下术语，用于描述程序设计语言的类型关系：

> 协变（Covariance）：使你能够使用比原始指定的类型派生程度更大的类型。你可以向List<Derived>类型的变量分配List<Of Derived>。
>
> 逆变（Contravariance）：使你能够使用比原始指定的类型更泛型（派生程度更小）的类型。你可以向List<Base>类型的变量分配List<Of Base>
>
> 不变（Invariance）：这意味着，你只能使用原始指定的类型。固定泛型类型参数既不是协变类型，也不是逆变类型。你无法向List<Base>类型的变量分配List(Of Base)。

<br>

## 1. 数组

&#160; &#160; &#160; &#160;我们早已习惯类的继承关系，对于一个父类B，不管是在赋值语句，还是在容器中，或者是在方法参数上，我们总能B的子类型完成业务逻辑。比如：

{% highlight java %}
//compile success
public static void main(String[] args) {
    Object obj = "";
    funcArg(1);
    List<Object> list = new ArrayList<>();
    list.add(0.5);
}

public static void funcArg(Object param) {}
{% endhighlight %}

{:.center}
代码清单1

<br>

&#160; &#160; &#160; &#160;对于数组来说，考虑以下代码：

{% highlight java %}
//compile success
Number[] integerArray = new Integer[]{1, 2, 3};
Number[] bigDecimalArray = new BigDecimal[]{new BigDecimal("")};
Number[] numberArray = {1, 0.5, new BigDecimal("")};
{% endhighlight %}

{:.center}
代码清单2

<br>

&#160; &#160; &#160; &#160;从代码清单2中可以发现，在数组的赋值中，依然遵循普通类的赋值关系，可以将子类型数组赋值给父类数组，这样的关系称为协变。

<br>

## 2. 不变

&#160; &#160; &#160; &#160;但是在泛型中，type argument的类型关系和泛型类的类型关系并不直观。考虑类型List&lt;Number &gt;，在赋值时，我们能给它传递什么呢？考虑如下代码：

{% highlight java %}
//compile success
List<Number> numberList = new ArrayList<>();

//compile fail: require Number, found Integer, type mismatch.
numberList = new ArrayList<Integer>();
{% endhighlight %}

{:.center}
代码清单3

<br>

&#160; &#160; &#160; &#160;在代码清单3中可以看到，即使type argument存在关系，比如Integer是Number的子类，并不意味着泛型类之间存在关系。
实际上，确定类型的泛型类是不变的，List &lt;Number &gt;和List&lt;Integer&gt;无直接关系，仅存在一个共同父类Object，如下图所示：

<br>

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_07_16_image1.gif)

{:.center}
[图片来源](https://docs.oracle.com/javase/tutorial/java/generics/inheritance.html)

<br>

&#160; &#160; &#160; &#160;确定类型的泛型类要想建立关系，和普通类一样，必须通过extends或者implements显示地定义类层级。比如JDK集合框架中的例子，ArrayList&lt;String &gt;是Iterable&lt;String &gt;的子类。

&#160; &#160; &#160; &#160;除了与父类拥有一样的type parameter之外，子类还可以额外新增不同的type parameter，考虑官方教程的代码例子：

{% highlight java %}
//compile success
interface PayloadList<E,P> extends List<E> {
    void setPayload(int index, P val);
}
{% endhighlight %}

{:.center}
代码清单4

<br>

&#160; &#160; &#160; &#160;在代码清单4中，子类PayloadList新增了type parameter P，对于任意的P，比如PayloadList&lt;String,String&gt;，PayloadList&lt;String,Number&gt;，PayloadList&lt;String,Integer&gt;，
都同属于List&lt;String &gt;的子类。类关系如下图所示：

<br>

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_07_16_image2.gif)

{:.center}
[图片来源](https://docs.oracle.com/javase/tutorial/java/generics/inheritance.html)

<br>

## 3. 有界泛型和通配符



<br>

## 附件

&#160; &#160; &#160; &#160;本博文所展示的源代码[由此获得](https://github.com/leesir/blog_code/tree/master/src/generic/boundandWildcard)。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
[3] [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3)[EB/OL].https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3，2019-07-04.<br>
[4] [泛型中的协变和逆变](https://docs.microsoft.com/zh-cn/dotnet/standard/generics/covariance-and-contravariance)[EB/OL].https://docs.microsoft.com/zh-cn/dotnet/standard/generics/covariance-and-contravariance，2019-07-16.<br>
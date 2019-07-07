---
layout: post
title: "Java语法糖-泛型-泛型边界"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 1. 有界泛型

#### 1.1 有界泛型的定义

&#160; &#160; &#160; &#160;当我们需要一个工具类只用于做数值计算，或者只用于做集合的遍历，方法只希望接收Number或者Collection，不希望接受其他类型，否则就得写一大堆instanceof的if判断。
为了解决这个问题，我们需要定义泛型的上边界，即方法所能接收的层级最高的类型。

&#160; &#160; &#160; &#160;在Java中，用extends关键字可以实现有界泛型的定义。格式为：&lt;T extends UpperBoundClass&gt;，其中UpperBoundClass为上边界类型。代码实例如下所示：

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

&#160; &#160; &#160; &#160;在使用代码清单1的工具方法的时，可以这么使用：

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

<!-- more -->

<br>

&#160; &#160; &#160; &#160;可见，只要定义了边界，泛型方法就只会接收符合条件的类型。而当传入不属于边界类型的子类时，将会出现编译时的参数类型错误。

<br>

#### 1.2 对边界类型的访问

&#160; &#160; &#160; &#160;除了可以限制类型之外，有界范型的定义还允许直接访问边界类型，比如：

{% highlight java %}
public static <T extends Collection> void iter(T collection) {
    //iterate
    if (!collection.isEmpty()) {
        for (Object ele : collection) {
            System.out.println(ele);
        }
    }
}
{% endhighlight %}

{:.center}
代码清单3

<br>

&#160; &#160; &#160; &#160;在代码清单3中，虽然方法参数collection的类型还未确定，依然可以访问边界类型的公有方法。

<br>

#### 1.3 多重边界类型

&#160; &#160; &#160; &#160;Java支持范型的多重边界的定义，但有一定的限制：

* 支持多接口类型边界。
* 支持有且仅有一个类边界及其他接口边界。
* 不支持多个类边界。

&#160; &#160; &#160; &#160;原因很简单，Java不支持类的多继承关系，所以一个类不可能有多个无关系的父类。
而接口可以多继承接口，所以支持多个接口类型边界。代码示例如下所示：

{% highlight java %}
public static <T extends Number & Cloneable> void computeCloneableNumber(T number) {
    System.out.println(number.intValue());
}

public static <T extends Serializable & Cloneable> void visitCAndSObject(T object) {
    System.out.println(object);
}

public static interface CAndS extends Cloneable, Serializable {

}

public static class CloneableInteger extends Number implements Cloneable {
    private Integer data;

    public CloneableInteger(Integer data) {
        this.data = data;
    }

    public Integer getData() {
        return data;
    }

    ...
}

//调用
//compile success
computeCloneableNumber(new CloneableInteger(1));
visitCAndSObject(new CAndS(){});

//compile fail，提示Integer不能转成Cloneable
computeCloneableNumber(1);
{% endhighlight %}

{:.center}
代码清单4

<br>

&#160; &#160; &#160; &#160;多重边界的范型，编译器类型擦出过后，总是保留最左侧的边界类型作为实际类型，在代码清单4中，computeCloneableNumber和visitCAndSObject在字节码中的方法参数分别为Number和Serializable：

{% highlight java %}
public static <T extends java.lang.Number & java.lang.Cloneable> void computeCloneableNumber(T);
descriptor: (Ljava/lang/Number;)V

public static <T extends java.io.Serializable & java.lang.Cloneable> void visitCAndSObject(T);
descriptor: (Ljava/io/Serializable;)V

{% endhighlight %}

<br>

## 附件

&#160; &#160; &#160; &#160;本博文所展示的源代码[由此获得](https://github.com/leesir/blog_code/tree/master/src/generic)。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
[3] [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3)[EB/OL].https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3，2019-07-04.<br>
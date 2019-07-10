---
layout: post
title: "Java语法糖-泛型-通配符"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;在范型的使用上，问号"?"表示通配符，代表运行时未知类型。通配符可用作type parameter：
* 方法形参
* 成员变量
* 局部变量
* 方法返回值（不建议这么做，方法的返回值应该是明确的）

&#160; &#160; &#160; &#160;通配符不能用作泛型方法的实参，不能用于范型类的初始化，即不能用作type argument。

<br>

## 1. 有界通配符

&#160; &#160; &#160; &#160;有界通配符与有界泛型一样，代表着泛型的某个边界。不同的地方在于，有界通配符除了上界之外，还有下界，通过super关键字实现。

<br>

#### 1.1 上界通配符

&#160; &#160; &#160; &#160;利用“? extends UpperBoundClass”可以实现上界通配符，比如你想实现一个工具方法，对一个数字列表进行求和，可以将方法参数声明为: <? extends Number>：

{% highlight java %}
public static double sumOfList1(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list) {
        s += n.doubleValue();
    }
    return s;
}
{% endhighlight %}

{:.center}
代码清单1

<!-- more -->

<br>

&#160; &#160; &#160; &#160;调用代码清单1的sumOfList1时，可以传入元素类型为Number子类的列表，如：

{% highlight java %}
//compile success
System.out.println(sumOfList1(Arrays.asList(1, 2, 2.3, new BigDecimal("2"))));
System.out.println(sumOfList1(Arrays.asList(1, 2, 3)));
System.out.println(sumOfList1(Arrays.asList(1.1, 2.2, 2.3)));
//output: 7.3, 6.0, 5.6
{% endhighlight %}

{:.center}
代码清单2

<br>

&#160; &#160; &#160; &#160;与上界泛型类似，在处理通配符列表时，我们可以通过对上界类型的公共方法的访问，通过多态忽略具体类型不同对代码的影响。

<br>

#### 1.2 下界通配符

&#160; &#160; &#160; &#160;利用“? super LowerBoundClass”可以实现下界通配符，将允许接收的最低层次类型限制在LowerBoundClass。
与上界通配符完全不同的是，在代码中不能访问下界通配符的元素。在上界通配符中，能够保证元素一定是UpperBoundClass类型，而在下界通配符中，没有办法保证元素一定是什么类型。
所以我们只能往下界通配符列表中插入一个LowerBoundClass元素，但是不能读取或者遍历该列表。

&#160; &#160; &#160; &#160;代码示例如下所示：

{% highlight java %}
//compile success
public static void sumOfListLower(List<? super Integer> list) {
    list.add(1);
}

//compile fail: incompatible type
public static void sumOfListLower(List<? super Integer> list) {
    Integer value = list.get(0);
}
{% endhighlight %}

{:.center}
代码清单3

<br>

## 2. 无界通配符

&#160; &#160; &#160; &#160;利用“?”可以实现无界通配符，表示完全未知的类型。有2种场景非常适合无界通配符：

* 当某个方法需要满足不同的参数类型，又仅需要Object类提供的功能时。
* 处于泛型类中，又不依赖于泛型T的方法，比如List.size()。

&#160; &#160; &#160; &#160;我们可以看看JDK中的例子：

{% highlight java %}
/**
 * JDK1.8 java.util.Collections
 *
 * Reverses the order of the elements in the specified list.<p>
 *
 * This method runs in linear time.
 *
 * @param  list the list whose elements are to be reversed.
 * @throws UnsupportedOperationException if the specified list or
 *         its list-iterator does not support the <tt>set</tt> operation.
 */
public static void reverse(List<?> list) {
    int size = list.size();
    if (size < REVERSE_THRESHOLD || list instanceof RandomAccess) {
        for (int i=0, mid=size>>1, j=size-1; i<mid; i++, j--)
            swap(list, i, j);
    } else {
        ListIterator fwd = list.listIterator();
        ListIterator rev = list.listIterator(size);
        for (int i=0, mid=list.size()>>1; i<mid; i++) {
            Object tmp = fwd.next();
            fwd.set(rev.previous());
            rev.set(tmp);
        }
    }
}
{% endhighlight %}

{:.center}
代码清单4

<br>

&#160; &#160; &#160; &#160;在代码清单4的reverse方法中，反转逻辑和具体类型是无关的，所以方法参数采用了List<?>。

<br>

## 3. 通配符类型捕获

&#160; &#160; &#160; &#160;在某些情况下，编译器会代码上下文进行类型推到，这个过程称为通配符捕获。
通常情况下开发者不需要关注本小节内容，因为通配符捕获的错误仅在下述十分罕见的代码中才会发生：

{% highlight java %}
//compile fail: incompatible type
public static void setFalse(List<?> target) {
    target.set(0, target.get(0));
}
{% endhighlight %}

{:.center}
代码清单5

<br>

&#160; &#160; &#160; &#160;如果要对某些容器做写入操作，最好声明成明确的类型。对于代码清单5的错误，Oracle官方教程给出了具体的解释，具体内容请参考[Wildcard Capture and Helper Methods](https://docs.oracle.com/javase/tutorial/java/generics/capture.html)。

<br>

## 延伸

&#160; &#160; &#160; &#160;有关通配符上下界的使用，还可以阅读[泛型中<? super T>和<? extends T>的区别](https://leesir.github.io/2015/10/java-qa-generics-extends-super)和[Guidelines for Wildcard Use](https://docs.oracle.com/javase/tutorial/java/generics/wildcardGuidelines.html)。

<br>

## 附件

&#160; &#160; &#160; &#160;本博文所展示的源代码[由此获得](https://github.com/leesir/blog_code/tree/master/src/generic/boundandWildcard)。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
[3] [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3)[EB/OL].https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3，2019-07-04.<br>
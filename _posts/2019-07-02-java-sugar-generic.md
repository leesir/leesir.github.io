---
layout: post
title: "Java语法糖-泛型"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 前言

<br>

&#160; &#160; &#160; &#160;JDK自1.5版本引入泛型机制，代码中常用的容器类（如java.util.Collection和java.util.Map）都属于泛型类。泛型的引入带来了以下好处：

* 在使用集合类时，将以往JDK版本的类型错配报错，由运行时提前到了编译期，使得代码不再需要处理ClassCastException。
* 在访问泛型元素时，不再需要强制转换对象类型，代码更加简洁优雅。
* 泛型类提高了代码复用率。

<br>

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_07_02_image1.png)

{:.center}
[图片来源](https://www.nextptr.com/series/a554793983/explained-java-generics-and-collections)

<br>

&#160; &#160; &#160; &#160;由于Java泛型属于语法糖，编译过后会将泛型信息擦除，如果不看版本号，单看泛型逻辑的部分，JDK 1.4和JDK 1.5版本的字节码是没有区别的。
相较于C++和C#的泛型，Java的泛型更像是伪泛型，因为JVM中没有泛型的概念，完全是编译器的magic。

<!-- more -->

<br>

## 实现

<br>

&#160; &#160; &#160; &#160;在编写泛型代码中，需要遵循Java的泛型命名规范。在Oracle官方教程中，最常用的泛型名称如下所示：

* E，表示元素，广泛用于JDK的集合框架中。
* K，表示Key。
* N，表示Number。
* T，表示Type。
* V，表示Value。
* S, U, V，表示第2、第3、第4个类型。

<br>

#### 1.泛型类

&#160; &#160; &#160; &#160;我们可以看看ArrayList的类定义：

{% highlight java %}
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{% endhighlight %}

&#160; &#160; &#160; &#160;接下来我们写一个自定义泛型类：

{% highlight java %}
public class TestGenericSimple<T> {
    private T data;

    public TestGenericSimple(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;TestGenericSimple的用法如下所示：

{% highlight java %}
//完整写法
TestGenericSimple<String> test1A =  new TestGenericSimple<String>("test1A");
String testAValue = test1A.getData();

//JDK 1.7之后的类型推导写法
TestGenericSimple<String> test1B = new TestGenericSimple<>("test1B");
String testBValue = test1A.getData();

//raw type写法
@SuppressWarnings("unchecked")
TestGenericSimple test1C = new TestGenericSimple("test1C");
String testCValue = (String) test1C.getData();
{% endhighlight %}

&#160; &#160; &#160; &#160;在intelliJ IDEA中，会对完整写法给出提示“Explicit type argument String can be replaced with <>”，这是因为可以使用尖括号<>让Java做类型推导。
而对于泛型类的raw type，编译器会给出警告“Unchecked call”，可以给实例化泛型类的方法或语句上新增@SuppressWarnings("unchecked")取消警告。上述3种写法中，推荐第2种，让Java自行做类型推导。

<br>

#### 1.泛型方法

&#160; &#160; &#160; &#160;泛型方法（静态或非静态）的定义和泛型类的定义类似，不过泛型的作用于仅局限于该方法内部。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
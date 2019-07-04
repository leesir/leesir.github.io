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
* 泛型提高了代码复用率。

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

## 代码实例

<br>

&#160; &#160; &#160; &#160;在编写泛型代码中，需要遵循Java的泛型命名规范。在[Oracle官方教程](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)中，最常用的泛型名称如下所示：

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

{:.center}
代码清单java-sugar-generic1

<br>

&#160; &#160; &#160; &#160;TestGenericSimple的用法如下所示：

{% highlight java %}
//1. 完整写法
TestGenericSimple<String> test1A =  new TestGenericSimple<String>("test1A");
String testAValue = test1A.getData();

//2. JDK 1.7之后的类型推导写法
TestGenericSimple<String> test1B = new TestGenericSimple<>("test1B");
String testBValue = test1A.getData();

//3. raw type写法
@SuppressWarnings("unchecked")
TestGenericSimple test1C = new TestGenericSimple("test1C");
String testCValue = (String) test1C.getData();
{% endhighlight %}

{:.center}
代码清单java-sugar-generic2

<br>

&#160; &#160; &#160; &#160;在intelliJ IDEA中，会对完整写法给出提示“Explicit type argument String can be replaced with <>”，这是因为可以使用尖括号<>让Java做类型推导。
而对于泛型类的raw type，编译器会给出警告“Unchecked call”，可以给实例化泛型类的方法或语句上新增@SuppressWarnings("unchecked")取消警告。上述3种写法中，推荐第2种，让Java自行做类型推导。
注：仅当泛型类在初始化时缺失了泛型参数，才被称作raw type，非泛型类没有raw type的说法。

<br>

#### 2.泛型方法

&#160; &#160; &#160; &#160;泛型方法（静态或非静态）的定义和泛型类的定义类似，不过泛型的范围于仅局限于该方法内部。
与非泛型方法不同的是，需要将该方法内所有泛型用尖括号包含起来，并写在泛型方法返回值之前。测试代码如下所示：

{% highlight java %}
public class TestGenericMethod {

    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
                p1.getValue().equals(p2.getValue());
    }

    public <T> boolean compare2(T object1, T object2) {
        return object1.equals(object2);
    }

    public <T, K, V> boolean compare3(T object1, Pair<K, V> p1) {
        return object1.equals(p1);
    }

    public class Pair<K, V> {

        private K key;
        private V value;

        public Pair(K key, V value) {
            this.key = key;
            this.value = value;
        }

        public void setKey(K key) {
            this.key = key;
        }

        public void setValue(V value) {
            this.value = value;
        }

        public K getKey() {
            return key;
        }

        public V getValue() {
            return value;
        }
    }
}
{% endhighlight %}

{:.center}
代码清单java-sugar-generic3

<br>

&#160; &#160; &#160; &#160;在代码清单java-sugar-generic3中，我们定义了1个泛型类Pair，3个泛型方法，分别为compare，compare2，compare3。
可以看到返回值之前，需要写明该方法的所有泛型，如<K, V>、<T, K, V>。上述代码的调用如下所示：

{% highlight java %}
//1. 完整写法
boolean compareResult = TestGenericMethod.<String, Integer>compare(new Pair<String, Integer>("key1", 1), new Pair<String, Integer>("key2", 2));
boolean compare3Result = new TestGenericMethod().<Object, String, Integer>compare3(new Object(), new Pair<String, Integer>("key3", 3));

//2. 类型推导写法
boolean compareResult1 = TestGenericMethod.compare(new Pair<>("key1", 1), new Pair<>("key2", 2));
boolean compare3Result1 = new TestGenericMethod().compare3(new Object(), new Pair<>("key3", 3));
{% endhighlight %}

{:.center}
代码清单java-sugar-generic4

<br>

&#160; &#160; &#160; &#160;在代码清单java-sugar-generic4中，有2种写法：完整写法和类型推导写法。同样intelliJ IDEA会对完整写法给出提示“Explicit type argument String can be replaced with <>”，建议实际代码中使用类型推导写法。

<br>

## 泛型其他方面

<br>

&#160; &#160; &#160; &#160;在本博文中，展示了Java泛型的语法和用法。由于篇幅关系，将泛型的其他方面单独作为独立的文章进行叙述：

* 类型擦除
* 泛型类之间的关系
* 泛型边界与通配符

<br>

## 附件

&#160; &#160; &#160; &#160;本博文所展示的源代码[由此获得](https://github.com/leesir/blog_code/tree/master/src/generic)。

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
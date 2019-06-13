---
layout: post
title: "Java语法糖-内部类"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;Java中有4种内部类，公有静态内部类，私有静态内部类，公有内部类，私有内部类。这里我们不谈各种内部类的使用场景，不对比优劣，仅从语法糖的角度学习Java是怎么实现内部类的。

## 静态内部类

&#160; &#160; &#160; &#160;静态内部类的初始化，不依赖于外部类的实例，并且不能访问外部类的实例变量，不能调用外部类的实例方法。


#### 公有静态内部类

&#160; &#160; &#160; &#160;测试代码如下所示：

{% highlight java %} 
public class TestPubStaticInnerClass {
    private static int priStaticValue = 3;
    public static int pubStaticValue = 3;

    private static void priFOut() {

    }

    public static void pubFOut() {

    }

    public static class PubStaticInnerClass {
        private int fPubStaticInnerClass() {
            priFOut();
            pubFOut();
            return priStaticValue + pubStaticValue;
        }
    }

    public static void main(String[] args) {
        PubStaticInnerClass pubStaticInnerClass = new PubStaticInnerClass();
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;运行命令：

{% highlight java %} 
//注意参数--removeinnerclasssynthetics false，否则反编译后的代码会重新糖化
java -jar cfr-0.145.jar TestPubStaticInnerClass.class --removeinnerclasssynthetics false
{% endhighlight %}

&#160; &#160; &#160; &#160;将得到如下代码：

{% highlight java %} 
/*
 * Decompiled with CFR 0.145.
 */
public class TestPubStaticInnerClass {
    private static int priStaticValue = 3;
    public static int pubStaticValue = 3;

    private static void priFOut() {
    }

    public static void pubFOut() {
    }

    public static void main(String[] args) {
        PubStaticInnerClass pubStaticInnerClass = new PubStaticInnerClass();
    }

    static /* synthetic */ void access$000() {
        TestPubStaticInnerClass.priFOut();
    }

    static /* synthetic */ int access$100() {
        return priStaticValue;
    }

    public static class PubStaticInnerClass {
        private int fPubStaticInnerClass() {
            TestPubStaticInnerClass.access$000();
            TestPubStaticInnerClass.pubFOut();
            return TestPubStaticInnerClass.access$100() + pubStaticValue;
        }
    }

}
{% endhighlight %}

&#160; &#160; &#160; &#160;可以看到，对于外部类的公有静态属性或方法，内部类可以直接调用。对于私有静态属性或者方法，编译器将会为其生成一个包级可见域的静态方法，如access$000和access$100，供内部类调用。

***

#### 私有静态内部类

&#160; &#160; &#160; &#160;测试代码如下所示：

{% highlight java %} 
public class TestPriStaticInnerClass {
    private static int priStaticValue = 3;
    public static int pubStaticValue = 3;

    private static void priFOut() {

    }

    public static void pubFOut() {

    }

    private static class PriStaticInnerClass {
        private int fPubStaticInnerClass() {
            priFOut();
            pubFOut();
            return priStaticValue + pubStaticValue;
        }
    }

    public static void main(String[] args) {
        PriStaticInnerClass priStaticInnerClass = new PriStaticInnerClass();
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;私有静态内部类在访问外部类的静态属性或方法的逻辑，与公有静态内部类的逻辑是一致的，不同的地方在于编译器对构造函数的处理。
私有静态内部类的默认构造函数是私有的，编译器为此额外生成了一个无逻辑内部类TestPriStaticInnerClass$1.class和一个以此类为参数的包级可见域的构造函数，
通过传入null调用新生成的带参数的构造函数。反编译后的代码如下所示：

{% highlight java %}
/*
 * Decompiled with CFR 0.145.
 */
public class TestPriStaticInnerClass {
    private static int priStaticValue = 3;
    public static int pubStaticValue = 3;

    private static void priFOut() {
    }

    public static void pubFOut() {
    }

    public static void main(String[] args) {
        PriStaticInnerClass priStaticInnerClass = new PriStaticInnerClass(null);
    }

    static /* synthetic */ void access$000() {
        TestPriStaticInnerClass.priFOut();
    }

    static /* synthetic */ int access$100() {
        return priStaticValue;
    }

    private static class PriStaticInnerClass {
        private PriStaticInnerClass() {
        }

        private int fPubStaticInnerClass() {
            TestPriStaticInnerClass.access$000();
            TestPriStaticInnerClass.pubFOut();
            return TestPriStaticInnerClass.access$100() + pubStaticValue;
        }

        /* synthetic */ PriStaticInnerClass(1 x0) {
            this();
        }
    }

}
{% endhighlight %}

***

## 非静态内部类

&#160; &#160; &#160; &#160;非静态内部类的初始化，依赖于外部类的实例，可以访问外部类的实例属性、方法及静态属性、方法。

#### 公有内部类


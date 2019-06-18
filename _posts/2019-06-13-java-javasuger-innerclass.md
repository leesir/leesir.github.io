---
layout: post
title: "Java语法糖-内部类"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;Java中有5种内部类，公有静态内部类，私有静态内部类，公有内部类，私有内部类，匿名内部类。这里我们不谈各种内部类的使用场景，不对比优劣，仅从语法糖的角度学习Java是怎么实现内部类的。

## 1.静态内部类

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

<!-- more -->

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

## 2.非静态内部类

&#160; &#160; &#160; &#160;非静态内部类的初始化，依赖于外部类的实例，会用一个实例变量保存外部类实例。内部类可以访问外部类的所有实例属性、方法及静态属性、方法。

#### 公有内部类

&#160; &#160; &#160; &#160;测试代码如下所示：

{% highlight java %}
public class TestPubInnerClass {
    private int privateValue = 0;
    private static int privateStaticValue = 0;
    public int publicValue = 1;
    public static int publicStaticValue = 3;

    public static void outPubStaticF() {

    }

    private static void outPriStaticF() {

    }

    public void outPubF() {

    }

    private void outPriF() {

    }

    public class PubInnerClass {
        public int f() {
            outPubStaticF();
            outPriStaticF();
            outPubF();
            outPriF();
            return privateValue + privateStaticValue + publicValue + publicStaticValue;
        }
    }

    public static void main(String[] args) {
        TestPubInnerClass testPubInnerClass = new TestPubInnerClass();
        PubInnerClass pubInnerClass = testPubInnerClass.new PubInnerClass();
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;反编译后的代码如下所示：

{% highlight java %}
/*
 * Decompiled with CFR 0.145.
 */
public class TestPubInnerClass {
    private int privateValue = 0;
    private static int privateStaticValue = 0;
    public int publicValue = 1;
    public static int publicStaticValue = 3;

    public static void outPubStaticF() {
    }

    private static void outPriStaticF() {
    }

    public void outPubF() {
    }

    private void outPriF() {
    }

    public static void main(String[] args) {
        TestPubInnerClass testPubInnerClass = new TestPubInnerClass();
        PubInnerClass pubInnerClass = new PubInnerClass(testPubInnerClass);
    }

    static /* synthetic */ void access$000() {
        TestPubInnerClass.outPriStaticF();
    }

    static /* synthetic */ void access$100(TestPubInnerClass x0) {
        x0.outPriF();
    }

    static /* synthetic */ int access$200(TestPubInnerClass x0) {
        return x0.privateValue;
    }

    static /* synthetic */ int access$300() {
        return privateStaticValue;
    }

    public class PubInnerClass {
        final /* synthetic */ TestPubInnerClass this$0;

        public PubInnerClass(TestPubInnerClass this$0) {
            this.this$0 = this$0;
        }

        public int f() {
            TestPubInnerClass.outPubStaticF();
            TestPubInnerClass.access$000();
            this.this$0.outPubF();
            TestPubInnerClass.access$100(this.this$0);
            return TestPubInnerClass.access$200(this.this$0) + TestPubInnerClass.access$300() + this.this$0.publicValue + publicStaticValue;
        }
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;可以看到，对于静态属性和方法的处理，与静态内部类的逻辑是一致的。
对于实例属性和方法，编译器同样为外部类添加了包级可见域的静态方法access$100和access$200，内部类调用方法时，会将外部类实例传参给方法，实现间接调用。

#### 私有内部类

&#160; &#160; &#160; &#160;私有内部类在属性和方法上与公有内部类的处理逻辑一致，除此之外，编译器会额外为私有内部类添加一个构造函数，与私有静态内部类的处理逻辑一致，这里不再赘述。

## 3.匿名内部类

&#160; &#160; &#160; &#160;匿名内部类的情况比上述内部类稍有不同，不过也是有迹可循。匿名内部类不像内部类一样，有公有私有一说，只有静态或非静态、成员或局部一说。
静态匿名内部类只能访问外部类的静态属性和方法，非静态匿名内部类可以访问外部类的所有属性和方法，与上述内部类逻辑一致。测试代码如下所示：

{% highlight java %}
//JDK 1.7代码
package innerclass;

public class TestAnonymousClass {
    public int publicValue = 0;
    public static int publicStaticValue = 0;
    private int privateValue = 0;
    private static int privateStaticValue = 0;

    private Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("thread");
            System.out.println(privateValue);
            System.out.println(privateStaticValue);
            System.out.println(publicValue);
            System.out.println(publicStaticValue);
        }
    });

    private static Thread staticThread = new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("staticThread");
            System.out.println(privateStaticValue);
            System.out.println(publicStaticValue);
        }
    });

    public static void main(String[] args) {
        final int localN = 0;
        Thread localThread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("localThread");
                System.out.println(localN);
                System.out.println(privateStaticValue);
                System.out.println(publicStaticValue);
            }
        });


    }

    private void outF(final int arg1) {
        InnerClass innerClass = new InnerClass() {

            @Override
            void innerF() {
                System.out.println(arg1);
                System.out.println(privateValue);
                System.out.println(privateStaticValue);
                System.out.println(publicValue);
                System.out.println(publicStaticValue);
                outF();
            }
        };
    }

    private void outF() {

    }

    class InnerClass {
        void innerF() {

        }
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;上面展示的demo，编译后将会产生5个内部类字节码文件，
分别对应成员变量thread，静态变量staticThread，内部类InnerClass，main方法中的局部匿名内部类localThread，实例方法outF中的局部匿名内部类innerClass。

{% highlight java %}
private void outF(int arg1) {
        InnerClass innerClass = new InnerClass(this, arg1){
            final /* synthetic */ int val$arg1;
            final /* synthetic */ TestAnonymousClass this$0;
            {
                this.this$0 = testAnonymousClass;
                this.val$arg1 = n;
                super(testAnonymousClass);
            }

            @Override
            void innerF() {
                System.out.println(this.val$arg1);
                System.out.println(TestAnonymousClass.access$000(this.this$0));
                System.out.println(TestAnonymousClass.access$100());
                System.out.println(this.this$0.publicValue);
                System.out.println(publicStaticValue);
                TestAnonymousClass.access$200(this.this$0);
            }
        };
    }
{% endhighlight %}

&#160; &#160; &#160; &#160;我们重点看一下上述匿名内部类的反编译代码。thread和localThread属于非静态的匿名内部类，同样会维护一个外部类类型的成员变量引用外部类的实例。
匿名内部类访问外部类的逻辑同普通内部类一样，直接调用公有属性或方法，调用编译器生成的包级可见域方法间接调用私有属性或方法。

&#160; &#160; &#160; &#160;匿名内部类与普通内部类最大的区别在于，匿名内部类可以访问局部变量和形参，在JDK 1.8以前，对局部变量和方法形参的访问，要求该变量和形参加上final修饰符（形参也属于局部变量，以下统称局部变量）。
方法结束之后，整个虚拟机栈包括局部变量在内，都会被销毁，而匿名内部类对象和普通类对象一样，回收的逻辑视垃圾回收器而定。这就会导致当匿名内部类对象还没被回收时，所引用的数据已经被销毁了，这明显是不合理的。
所以Java在编译阶段，就明确要求被匿名内部类访问的局部变量持有final修饰符，同时通过语法糖，将该变量复制成一个成员变量，并通过构造函数传入。这样可以保证即使局部变量被销毁了，匿名内部类对象依然能够访问相等的值。

&#160; &#160; &#160; &#160;在JDK 1.8版本中，引入了”effectively final“，只要局部变量在方法体内不做修改，编译器会自动对局部变量声明final。当然，如果对局部变量有赋值语句，则编译报错：

```$xslt
Error:(64, 36) java: 从内部类引用的本地变量必须是最终变量或实际上的最终变量
```

## 4.总结

<br>
* #### 内部类字节码命名格式

&#160; &#160; &#160; &#160;不管什么类型的内部类，都和普通类并无二致。编译器会在外部类字节码文件相同目录下，为内部类生成字节码文件。
普通内部类的字节码文件命名格式为：OuterClass$InnerClass.class，匿名内部类的命名方式为：OuterClass$index.class，其中index为匿名内部类在源代码中的顺序，如OuterClass$1.class。

<br>
* #### 内部类对外部类的访问

1. 静态内部类不能访问外部类的实例属性和方法，可以访问外部类的公有或私有的静态属性或方法。
2. 非静态内部类可以访问外部类的实例、静态属性和方法，编译器会为内部类生成一个引用外部类实例的实例属性。
3. 静态内部类访问外部类公有属性或方法，直接调用OuterClass.pubValue或者OuterClass.pubFunc即可。访问外部类私有属性或方法，通过调用编译器为外部类生成的包级可见域的方法即可。
4. 非静态内部类访问外部类属性或方法的逻辑与第三点类似。
5. 除上述逻辑之外，匿名内部类引用的局部变量，都必须是final类型的，JDK 1.8之前要求手动声明final，自JDK 1.8起引入了”effectively final“，不再需要手动声明。

<br>

* #### 附件

&#160; &#160; &#160; &#160;本博文使用到的源代码[由此获得](https://github.com/leesir/blog_code)。

<br>

## 5.参考文献

[1] Lee Benfield.[Inner classes have to fake friendship](http://www.benf.org/other/cfr/inner-class-fake-friends.html)[EB/OL].http://www.benf.org/other/cfr/inner-class-fake-friends.html，2019-06-18.<br>
[2] [浅析java中的语法糖](https://www.cnblogs.com/qingshanli/p/9375040.html)[EB/OL].https://www.cnblogs.com/qingshanli/p/9375040.html，2019-06-18.<br>
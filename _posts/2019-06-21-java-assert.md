---
layout: post
title: "Java语法糖-assert"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;在谈断言之前，必须要谈谈2种编程方式：防御性编程与契约式编程。防御性编程来自于《代码大全》，契约式编程由伯特兰·迈耶与1986年提出。

&#160; &#160; &#160; &#160;防御性编程强调不信任外部输入的参数，要对所有可能出现问题的数据加以判断，这种编程方式的代码中，通常存在大量重复的if语句。

&#160; &#160; &#160; &#160;契约式编程强调系统内各组件必须按规定实现各自的逻辑，在组件交互的过程中不对参数进行校验，如果发现参数不符合要求，程序将立即终止执行。
现代编程语言通过断言的机制实现契约式编程，这种编程方式的代码比较简洁。

&#160; &#160; &#160; &#160;然而在现今的软件开发过程中，不可能实现契约式编程：人员流动大、不愿意写文档、不愿意看文档、需求迭代快。
当然防御性编程也做的不好，否则就不会由bug了。甚至有时候某种做法属于防御性编程还是契约式编程都不清晰，比如使用注解式参数校验框架，看似属于契约式编程：代码简洁、快速失效。
而当反编译过后，实则大量基础if判断。

&#160; &#160; &#160; &#160;防御性编程与契约式编程思想的出现，都算的上是软件开发思想的进步，而受制于当时的知识背景，难免在今天略显过时。就此结束讨论，进入正题。

<!-- more -->

&#160; &#160; &#160; &#160;断言关键字自JDK 1.4引入，下面是Oracle官方文档的解释：

>&#160; &#160; &#160; &#160;An assertion is a statement in the JavaTM programming language that enables you to test your assumptions about your program. For example, if you write a method that calculates the speed of a particle, you might assert that the calculated speed is less than the speed of light.
>
>&#160; &#160; &#160; &#160;Each assertion contains a boolean expression that you believe will be true when the assertion executes. If it is not true, the system will throw an error. By verifying that the boolean expression is indeed true, the assertion confirms your assumptions about the behavior of your program, increasing your confidence that the program is free of errors.
>
>&#160; &#160; &#160; &#160;Experience has shown that writing assertions while programming is one of the quickest and most effective ways to detect and correct bugs. As an added benefit, assertions serve to document the inner workings of your program, enhancing maintainability.

&#160; &#160; &#160; &#160;除了在[Java语法糖](http://leesir.github.io/2019/06/java-javasuger)这篇中介绍的断言的用法，还有一个差别不大但是可以自定义错误信息的用法，代码如下所示：

{% highlight java %} 
assert Expression1 : Expression2 ;
{% endhighlight %}

&#160; &#160; &#160; &#160;其中，Expression1是一个布尔表达式。Expression2是一个值或者是一个有返回值的方法调用，将作为AssertionError的错误信息提示出来。

&#160; &#160; &#160; &#160;实例代码如下：

{% highlight java %}
package assertcode;

import java.util.Random;

public class TestAssertByteCode {

    public static void main(String[] args) {
        assert1();
        assert2();
        assert3();
    }

    /**
     * 测试断言assert Expression1;
     */
    private static void assert1() {
        int assert1 = 0;
        assert1++;
        assert assert1 == 0;
    }

    /**
     * 测试assert Expression1 : Expression2;
     * Expression2为普通值
     */
    private static void assert2() {
        int assert2 = new Random().nextInt();
        assert assert2 <= 0 : "assert2";
    }

    /**
     * 测试assert Expression1 : Expression2;
     * Expression2为方法调用
     */
    private static void assert3() {
        int assert3 = new Random().nextInt();
        assert assert3 <= 0 : getAssert3ErrorMsg();
    }

    private static int getAssert3ErrorMsg() {
        return 3;
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;assert1、assert2、assert3断言检查失败，输出如下所示：

{% highlight java %}
//assert1
Exception in thread "main" java.lang.AssertionError
	at assertcode.TestAssertByteCode.assert1(TestAssertByteCode.java:19)
	at assertcode.TestAssertByteCode.main(TestAssertByteCode.java:8)

//assert2
Exception in thread "main" java.lang.AssertionError: assert2
	at assertcode.TestAssertByteCode.assert2(TestAssertByteCode.java:21)
	at assertcode.TestAssertByteCode.main(TestAssertByteCode.java:9)

//assert3
Exception in thread "main" java.lang.AssertionError: 3
    at assertcode.TestAssertByteCode.assert2(TestAssertByteCode.java:21)
    at assertcode.TestAssertByteCode.main(TestAssertByteCode.java:9)
{% endhighlight %}

&#160; &#160; &#160; &#160;反编译后的代码也比较简单，以assert3为例反编译代码如下所示：

{% highlight java %}
private static void assert3() {
    int assert3 = new Random().nextInt();
    if (!$assertionsDisabled && assert3 > 0) {
        throw new AssertionError(TestAssertByteCode.getAssert3ErrorMsg());
    }
}
{% endhighlight %}

## 总结

&#160; &#160; &#160; &#160;目前主流IDE和各公司生产环境的启动命令，都不会默认将断言功能开启。
即使诸多知乎大牛及oracle官网教程，都对"断言的价值"和"断言的用法"进行了大篇幅的讲述，不过笔者依然不建议使用assert，assert的错误既不方便记录日志，也不够友好。


## 参考文献

[1] [Programming With Assertions](https://docs.oracle.com/javase/7/docs/technotes/guides/language/assert.html)[EB/OL].https://docs.oracle.com/javase/7/docs/technotes/guides/language/assert.html，2019-06-21.<br>
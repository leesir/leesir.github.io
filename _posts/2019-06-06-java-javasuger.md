---
layout: post
title: "Java语法糖"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">


&#160; &#160; &#160; &#160;语法糖并不是Java独有的概念，而是一个计算机术语，先来看看百度百科的释义：

> 语法糖（Syntactic sugar），也译为糖衣语法，是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

{:.center}
![Syntactic Suger]({{ site.baseurl }}/images/post_2019_06_06_image1.jpg){:height="70%" width="70%"}

<br>
&#160; &#160; &#160; &#160;Java语言可以支持某些关键字或者语法的使用，但在编译后的字节码中，却看不到这些代码的痕迹。语法糖可以在性能无损的情况下，提高编程的效率，增加代码可读性，几乎所有的现代编程语言都含有语法糖成分。今天来简单回顾一下Java语言截止至今所包含的语法糖。
注：必须显示指定vm启动参数-ea或者-enableassertions，才可以让assert生效:

{% highlight java %} 
//以下代码为命令行启动java的范例，各个IDE也有相应修改参数的地方，这里不再赘述

java -ea Main
java -enableassertions Main
{% endhighlight %}

<!-- more -->

### 学习前准备

&#160; &#160; &#160; &#160;如果想要查看反编译后的代码，强烈建议不要使用IDE自带的反编译插件，因为这类图形化工具很可能会把语法糖重新”糖化“，让代码高度还原，
但这会对学习语法糖造成一定困惑。

&#160; &#160; &#160; &#160;以IDEA IntelliJ 2019.1版本为例，断言测试代码：

{:.center}
![assert1]({{ site.baseurl }}/images/post_2019_06_06_image1.png)

<br>
&#160; &#160; &#160; &#160;以IDEA IntelliJ 反编译后的代码为：

{:.center}
![assert1]({{ site.baseurl }}/images/post_2019_06_06_image2.png)

<br>
&#160; &#160; &#160; &#160;可以看到代码是高度一致的，然而用javap工具查看字节码文件，可以看到代码里会抛出AssertionError:

{:.center}
![assert1]({{ site.baseurl }}/images/post_2019_06_06_image3.png)

&#160; &#160; &#160; &#160;很明显，IDE反编译插件给出的代码，并不是真正字节码的样子，而是优化后的代码。
现在我们用另外一个[反编译工具CFR](http://www.benf.org/other/cfr/)尝试一下。笔者下载的版本是cfr-0.145.jar，执行以下命令：

{% highlight java %} 
//参数sugarasserts为false，表示不需要把assert重新糖化

java -jar cfr-0.145.jar TestAssertByteCode.class --sugarasserts false
{% endhighlight %}

&#160; &#160; &#160; &#160;执行CFR后得到的反编译代码如下：

{:.center}
![assert1]({{ site.baseurl }}/images/post_2019_06_06_image4.png)

<br>
&#160; &#160; &#160; &#160;这才是我们想要的效果，可以看到assert实际上是通过是否开启断言的变量$assertionsDisabled和源代码里的断言表达式进行逻辑与&&来实现的，如果为false，则抛出AssertionError。
本篇博文以CFR为例，浅析Java目前所包含的语法糖。

## **语法糖列表**

* 内部类、条件编译、assert关键字(JDK 1.4及更早版本)
* 泛型、for-each、自动拆装箱、枚举、可变参数列表(JDK 1.5)
* switch-with-String、switch-with-enum、try-with-resource(JDK 1.7)
* lambda(JDK 1.8)

## 参考文献

[1] [CFR - another java decompiler](http://www.benf.org/other/cfr/)[EB/OL].http://www.benf.org/other/cfr/，2019-06-06.<br>
[2] [Programming With Assertions](https://docs.oracle.com/javase/7/docs/technotes/guides/language/assert.html)[EB/OL].https://docs.oracle.com/javase/7/docs/technotes/guides/language/assert.html，2019-06-06.<br>





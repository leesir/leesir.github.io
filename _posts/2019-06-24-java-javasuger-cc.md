---
layout: post
title: "Java语法糖-条件编译"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 前言

<br>

&#160; &#160; &#160; &#160;条件编译，顾名思义，就是有条件地对代码进行编译，对于一套源代码，不同条件可能会编译出不同的两套目标代码。
条件完全不同于if语句。if语句属于代码逻辑，不管哪个代码分支都会被编译。而条件编译只会编译满足条件的分支的代码。

&#160; &#160; &#160; &#160;条件编译广泛存在于需要跨平台运行的程序里，在真正开始编译之前，预处理过程会把代码扫描一遍，进行初步转换，其中一项任务就是执行条件编译。
在C和C++中，由预处理命令实现条件编译。预处理命令由#开头，比如#ifdef：

{% highlight java %}
//程序处于调试阶段，可以将调试日志打印出来 
#ifdef DEBUG
{% endhighlight %}

&#160; &#160; &#160; &#160;因C\C++的编译会最终生成目标机器的可执行文件，可执行文件是与平台极其相关的，因此C\C++程序可能会出现同一份代码需要分别根据平台进行编译的情况。

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_06_24_image1.jpg)

{:.center}
[图片来源](https://computer.howstuffworks.com/c1.htm)

<!-- more -->

<br>

## 关于Java

<br>

&#160; &#160; &#160; &#160;令人疑惑的是，为什么Java程序员对条件编译的这个名词比较陌生？好像开发者不需要为程序部署在windows上还是unix上费过脑筋呀。
这就得谈谈让Java实现“Write once, run anywhere”的JVM了。

&#160; &#160; &#160; &#160;在理想情况下，Java程序可以在任何系统上开发，并在任何装有正确版本JVM的平台上运行。JVM除了管理运行时环境之外，还屏蔽了底层系统的差异。
所以不能说Java没有条件编译，而是系统层面条件编译的诸多实现下沉到了JVM内部，使得业务开发者无需关注底层细节。

&#160; &#160; &#160; &#160;顺便再聊一下“Write once, run anywhere”（WORA）。有人说这是广告语，有人戏称是“Write once, debug everywhere”。
可以明确的是：

> 通常语言原生的GUI支持都很难做到真正的跨平台，通常需要第三方框架作为替代，比如QT、游戏引擎等。但Java GUI开发也有跨平台成功案例：eclipse和Intellij IDEA。
>
> 如因性能原因需要调用系统底层功能（如Java中的JNI，本地方法调用），这类功能往往和平台是强绑定关系，因此含有JNI的Java程序也很难做到跨平台。
>
> 因编码不当，对诸如文件系统的斜杠或反斜杠等平台相关的数据hard code之后，很难实现跨平台。应尽可能采用语言提供的通用库替代hard code。
>

&#160; &#160; &#160; &#160;抛开上述方面，准确来讲，Java实现了WORA，在主流硬件和系统厂商的设备上，实现了跨平台。

&#160; &#160; &#160; &#160;换个角度诠释同样的概念，如果说某个系统是安全的，是否该系统就绝对安全呢？很明显不是，估计没有什么产品顶得住[腾讯科恩实验室](https://slab.qq.com/news/kuaixun/1284.html)的攻击。
然而，任何黑客攻击都是有目的性的商业行为，比如当你的短信系统被超出正常范围的大量ip和大量真实手机号请求的时候，很可能就是短信通道或者运营商通过黑产让企业强制消费。
而当你的系统做的相对安全，让黑客的投入远大于所得的时候，也就没有了攻击的目的，此时系统就是安全的。

&#160; &#160; &#160; &#160;回到本节主题上来，在商业上，只要满足了绝大部分情况的WORA，那就是WORA，因为完全没有必要为十分小众的硬件和系统体系实现一个完备的JVM。

&#160; &#160; &#160; &#160;程序员群体自视清高，看谁谁傻逼，文人易相轻。
本应该备受赞美的特性，被小部分杠精抓着喷。甚至不少人随声附和“Write once, debug everywhere”，请问真的有过“debug everywhere”的经历吗，胡编乱造有什么意思，不知如何评价这种人。 

<br>

## 实现

&#160; &#160; &#160; &#160;Java仅支持常量条件的if语句条件编译。测试代码如下所示：

{% highlight java %}
package conditionalcompile;

public class TestCC {
    private static final boolean flag = false;

    public static void main(String[] args) {
        if (flag) {
            System.out.println("print false");
        }
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;执行java -jar cfr-0.145.jar TestCC.class之后得到以下反编译代码：

{% highlight java %}
/*
 * Decompiled with CFR 0.145.
 */
package conditionalcompile;

public class TestCC {
    private static final boolean flag = false;

    public static void main(String[] args) {
    }
}
{% endhighlight %}

&#160; &#160; &#160; &#160;可以看到，打印语句因不可达而直接被抹除了。因为if仅能在方法块内部出现，不像C\C++可以改变整个类的编译分支，所以Java的条件编译功能十分有限。
因此，如果一个项目的业务代码中出现了条件编译，可以肯定的是，代码结构出了问题，原因很简单：如果2套代码流程确实不会同时出现，直接抽离成2套服务就行了嘛（小声嘀咕：什么中台，微服务，不就这么来的吗）。

<br>

## 参考文献

[1] [Conditional Syntax](https://gcc.gnu.org/onlinedocs/cpp/Ifdef.html#Ifdef)[EB/OL].https://gcc.gnu.org/onlinedocs/cpp/Ifdef.html#Ifdef，2019-06-24.<br>
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

<br>

## Java的条件编译

<br>

&#160; &#160; &#160; &#160;令人疑惑的是，为什么Java程序员对条件编译的这个名词比较陌生？好像开发者不需要为程序部署在windows上还是unix上费过脑筋呀。
这就得谈谈让Java实现“Write once, run anywhere”的JVM了。

&#160; &#160; &#160; &#160;在理想情况下，Java程序可以在任何设备上开发，并且在任何装有正确版本JVM的平台上运行。JVM除了管理运行时环境之外，还屏蔽了底层系统的差异。
所以不能说Java没有条件编译，而是条件编译的实现下沉到了JVM内部，使得业务开发者无需关注底层细节。

&#160; &#160; &#160; &#160;顺便再聊一下“Write once, run anywhere”（WORA）。有人说这是广告语，有人戏称是“Write once, debug anywhere”。
在讨论WORA之前，我们必须明确几点：

> 不能要求Java或者VM语言运行在


<br>

## 5.参考文献

[1] Lee Benfield.[Inner classes have to fake friendship](http://www.benf.org/other/cfr/inner-class-fake-friends.html)[EB/OL].http://www.benf.org/other/cfr/inner-class-fake-friends.html，2019-06-18.<br>
[2] [浅析java中的语法糖](https://www.cnblogs.com/qingshanli/p/9375040.html)[EB/OL].https://www.cnblogs.com/qingshanli/p/9375040.html，2019-06-18.<br>
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

&#160; &#160; &#160; &#160;Java语言可以支持某些关键字或者语法的使用，但在编译后的字节码中，却看不到这些代码的痕迹。语法糖可以在性能无损的情况下，提高编程的效率，增加代码可读性，几乎所有的现代编程语言都含有语法糖成分。今天来简单回顾一下Java语言截止至今所包含的语法糖。

<br>

{:.center}
![Syntactic Suger]({{ site.baseurl }}/images/post_2019_06_06_image1.jpg){:height="70%" width="70%"}

<!-- more -->

<br>

```//TODO
find out 
```

* 内部类、条件编译
* assert关键字(JDK 1.4)
* 泛型、for-each、自动拆装箱、枚举、可变参数列表(JDK 1.5)
* switch-with-String、switch-with-enum、try-with-resource(JDK 1.7)
* lambda(JDK 1.8)
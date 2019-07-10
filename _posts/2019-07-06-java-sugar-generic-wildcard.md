---
layout: post
title: "Java语法糖-泛型-通配符"
description: ""
category: [Java]
tags: [Java Language]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;在范型的使用上，问号"?"表示通配符，代表运行时未知类型。通配符可用在：
* 方法形参
* 成员变量
* 局部变量
* 方法返回值（不建议这么做，方法的返回值应该是明确的）

&#160; &#160; &#160; &#160;通配符不能用作泛型方法的实参，不能用于范型类的初始化。

<br>

## 1. 有界通配符

&#160; &#160; &#160; &#160;有界通配符与有界泛型一样，代表着泛型的某个边界。不同的地方在于，有界通配符除了上界之外，还有下界，通过super关键字实现。

<br>

#### 1.1 上界通配符



<br>

## 附件

&#160; &#160; &#160; &#160;本博文所展示的源代码[由此获得](https://github.com/leesir/blog_code/tree/master/src/generic)。

<br>

## 参考文献

[1] Josh Juneau.[泛型：工作原理及其重要性](https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html)[EB/OL].https://www.oracle.com/technetwork/cn/articles/java/juneau-generics-2255374-zhs.html，2019-07-02.<br>
[2] [The Java™ Tutorials - Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)[EB/OL].https://docs.oracle.com/javase/tutorial/java/generics/index.html，2019-07-02.<br>
[3] [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3)[EB/OL].https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-4.html#jvms-4.3，2019-07-04.<br>
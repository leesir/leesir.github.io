---
layout: post
title: "Java的历史"
description: ""
category: [Java Basics]
tags: [Java History]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">


从第一代Java 1.0版本的发布至今，已有接近21年的时间了。21年里，计算机及互联网领域，出现了行业红利，出现了泡沫，出现了危机。伴随着行业起伏的大浪潮，同样出现了许许多多的市值千亿美金级别的软件、互联网公司。

截止至目前，最新版本的Java 8的体系架构如下图所示：

![Java8_structure]({{ site.baseurl }}/images/post_2016_08_14_image1.png)

接下来，从1991年~2000年（Java的古代）、2000年~2006年（Java 的近代）、2006年至今（Java的现代）3个时间段，简单介绍一下Java语言诞生至今的重要时刻的时间线。

<!-- more -->

1991年4月起，就职于Sun公司的[James Gosling博士](https://en.wikipedia.org/wiki/James_Gosling)，主持Oak语言的开发计划，目标是让Oak能在各种电子产品上无差别地运行。想法很好，然而Oak并不成功。在Sun即将完全放弃该项目计划时，Oak，这艘快要沉没的小船，踏上了互联网的大浪，从此走向了巅峰。

1995年5月，Sun公司将Oak语言正式更名为Java，并发布了Java 1.0版本，同时提出了我们所熟知的“Write once, Run Anywhere”的口号，此时的Java还没有JDK、JRE一说。

1996年1月，JDK1.0诞生了，同JDK一同发布的还有一个纯解释执行的虚拟机、界面开发的AWT等（时间久远，已经很难找到JDK 1.4之前版本的文档了）。同年，已经有不少公司使用Java Applet开发web应用了。由于早期Java解释执行的性能与C/C++差了很远，性能问题一直是让开发者诟病的重点。

1997年2月，Sun公司发布了JDK 1.1，此版本的一些重要特性，成为了今后Java整个生态系统的重要支撑点，如RMI、JDBC、反射等。此版本也规范了Jar文件的格式。同年，Sun收购了HotSpot虚拟机的开发商，巩固了Sun在虚拟机领域的地位，HotSpot虚拟机成为JDK 1.3以后的默认虚拟机（Sun自研的虚拟机产品Classic VM，远不如其他公司的虚拟机高效、稳定）。

1998年12月，JDK 1.2发布，工程代号为Playground。Sun公司把Java的技术体系拆分为3个方向，分别是J2EE、J2SE、J2ME，分别为面向企业的企业级Java、面向桌面应用的标准版Java、面向移动设备开发的微型版Java（我本人对这几个名词没什么概念）。此版本的虚拟机内置了JIT编译器，同时在语言层面丰富了集合框架。

2000年5月，JDK 1.3发布。该版本新增了一些类库，如Timer等。改进了RMI、2D的实现。此后，1999年发布的HotSpot虚拟机成为JDK默认的虚拟机，并且每两年发布以动物命名的主版本，主版本之间的各种修复、增强小版本以昆虫命名。

![JDK timeline from 1.0 - 1.3]({{ site.baseurl }}/images/post_2016_08_14_image2.png)

2002年2月，JDK 1.4发布。
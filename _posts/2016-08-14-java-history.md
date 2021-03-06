---
layout: post
title: "Java的历史"
description: ""
category: [Java Basics]
tags: [History]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">


&#160; &#160; &#160; &#160;从第一代Java 1.0版本的发布至今，已有接近21年的时间了。21年里，计算机及互联网领域，出现了行业红利，出现了泡沫，出现了危机。伴随着行业起伏的大浪潮，同样出现了许许多多的市值千亿美金级别的软件、互联网公司。

&#160; &#160; &#160; &#160;截止至目前，最新版本的Java 8的体系架构如下图所示：

![Java8_structure]({{ site.baseurl }}/images/post_2016_08_14_image1.png)

&#160; &#160; &#160; &#160;接下来，从1991年~2000年（Java的古代）、2002年~2006年（Java 的近代）、2006年至今（Java的现代）3个时间段，简单介绍一下Java语言诞生至今的重要时刻的时间线。

<!-- more -->

&#160; &#160; &#160; &#160;1991年4月起，就职于Sun公司的[James Gosling博士](https://en.wikipedia.org/wiki/James_Gosling)，主持Oak语言的开发计划，目标是让Oak能在各种电子产品上无差别地运行。想法很好，然而Oak并不成功。在Sun即将完全放弃该项目计划时，Oak，这艘快要沉没的小船，踏上了互联网的大浪，从此走向了巅峰。

&#160; &#160; &#160; &#160;1995年5月，Sun公司将Oak语言正式更名为Java，并发布了Java 1.0版本，同时提出了我们所熟知的“Write once, Run Anywhere”的口号，此时的Java还没有JDK、JRE一说。

&#160; &#160; &#160; &#160;1996年1月，JDK1.0诞生了，同JDK一同发布的还有一个纯解释执行的虚拟机、界面开发的AWT等（时间久远，已经很难找到JDK 1.4之前版本的文档了）。同年，已经有不少公司使用Java Applet开发web应用了。由于早期Java解释执行的性能与C/C++差了很远，性能问题一直是让开发者诟病的重点。

&#160; &#160; &#160; &#160;1997年2月，Sun公司发布了JDK 1.1，此版本的一些重要特性，成为了今后Java整个生态系统的重要支撑点，如RMI、JDBC、反射等。此版本也规范了Jar文件的格式。同年，Sun收购了HotSpot虚拟机的开发商，巩固了Sun在虚拟机领域的地位，HotSpot虚拟机成为JDK 1.3以后的默认虚拟机（Sun自研的虚拟机产品Classic VM，远不如其他公司的虚拟机高效、稳定）。

&#160; &#160; &#160; &#160;1998年12月，JDK 1.2发布，工程代号为Playground。Sun公司把Java的技术体系拆分为3个方向，分别是J2EE、J2SE、J2ME，分别为面向企业的企业级Java、面向桌面应用的标准版Java、面向移动设备开发的微型版Java（我本人对这几个名词没什么概念）。此版本的虚拟机内置了JIT编译器，同时在语言层面丰富了集合框架。

&#160; &#160; &#160; &#160;2000年5月，JDK 1.3发布。该版本新增了一些类库，如Timer等。改进了RMI、2D的实现。此后，1999年发布的HotSpot虚拟机成为JDK默认的虚拟机，并且每两年发布以动物命名的主版本，主版本之间的各种修复、增强小版本以昆虫命名。

![JDK timeline from 1.0 - 1.3]({{ site.baseurl }}/images/post_2016_08_14_image2.png)

&#160; &#160; &#160; &#160;2002年2月，JDK 1.4发布，此版本引入了非阻塞IO，包名为java.nio，让Java在服务器开发的领域更进了一步。除此之外，新增了日志，以及Throwable的部分方法。<br>
&#160; &#160; &#160; &#160;JDK 1.4是很多目前流行的开源框架（如spring）能够运行的最低版本，同时也为以后高性能IO框架（netty、mina）的出现打下了基础。
不得不提的是，.NET Framework也与同年发布，在此后的十多年里，Java和.NET互相学习互相进步，在各自领域都取得了相当不错的成绩。

&#160; &#160; &#160; &#160;2004年，JDK 1.5发布，此版本在语法上做了大量改进，比如泛型、for-each、自动拆装箱等。
新增了一个重量级api包：java.util.concurrent，新增了非常常用的Future、ThreadPoolExecutor、原子操作类java.util.concurrent.atomic.*等。
此版本发布，也使得Java程序员在并发编程的时候，不需要手动创建线程，转而使用更为合理的线程池的实现方式。

&#160; &#160; &#160; &#160;2006年，JDK 1.6发布，此版本没有对Java的易用性做任何改进，主要改进了JVM内部的一些实现，如垃圾回收，类加载等，这也是笔者接触Java的第一个版本。

&#160; &#160; &#160; &#160;插一句题外话，Sun公司于2009年被Oracle收购，在此之前，由于公司经营、经济危机等问题，Java每两年更新一个Major版本的目标已经无法保持，本应于2008年发布的JDK 1.7跳票了。
根据JDK 1.7的规划，该版本包含了许多语法层面和JVM层面的更新，比如Lambda（推迟到JDK 1.8），Jigsaw（推迟到JDK 1.9），G1垃圾收集器等。
Oracle在收购Sun之后，把JDK 1.7的计划做了重大调整，把Lambda、Jigsaw等项目推迟到JDK 1.8版本。


&#160; &#160; &#160; &#160;2011年，JDK 1.7姗姗来迟，此版本提升了JVM性能，提供G1收集器。除此之外，<br>
&#160; &#160; &#160; &#160;新增api：
* fork-join框架
* java.nio.file包，增强了对文件系统的读写访问能力。

&#160; &#160; &#160; &#160;新增语法：
* 新增try-with-resources
* 单个catch语句可以捕获多个exception，比如catch (IOException|SQLException ex)


&#160; &#160; &#160; &#160;2014年，jdk 1.8发布，此版本包含了Hotspot虚拟机的一些更新，比如移除了永久代PermGen，[详细说明点此链接。](http://openjdk.java.net/jeps/122)<br>
&#160; &#160; &#160; &#160;首先，《Java虚拟机规范》中并没有规定JVM一定要包含PermGen区域，其次，在收购Sun之后，Oracle已经拥有2个商业上较为成功的JVM: JRocket, Hotspot。Oracle曾因此宣布要将这俩JVM取长补短，不排除合二为一的可能性，由此可见PermGen的移除是虚拟机改造的第一步。<br>
&#160; &#160; &#160; &#160;在语法层面上，此版本新增了接口的默认方法和Lambda等。在api层面上，新增了java.util.stream和java.time等。

## 总结
&#160; &#160; &#160; &#160;从Java的历史版本更新内容来看，在语法及语言特性层面，有新功能对旧功能的取代，比如JDK 1.8的时间日期库。有不断增强的新功能，比如集合和并发库。有汲取其他语言的优秀特性，比如Lambda函数式编程。<br>
&#160; &#160; &#160; &#160;在JVM层面，Java开发团队更是做出了巨大的努力，让内存管理逐渐由多方诟病转变为语言优势。垃圾收集器的推陈出新，内存管理的改造，锁优化，类加载优化，所有这些努力都为了让Java更高效，更具竞争力。

## 参考文献
[1] 周志明.[深入理解Java虚拟机](https://item.jd.com/11252778.html)[M].北京：机械工业出版社，2013：5-9.<br>
[2] Jon Masamitsu.[JEP 122: Remove the Permanent Generation](http://openjdk.java.net/jeps/122)[EB/OL].http://openjdk.java.net/jeps/122，2010-08-15/2019-06-05.<br>
[3] Oracle.[What's New in JDK 8](https://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)[EB/OL].https://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html.<br>
[4] Oracle.[Java SE 7 Features and Enhancements](https://www.oracle.com/technetwork/java/javase/jdk7-relnotes-418459.html)[EB/OL].https://www.oracle.com/technetwork/java/javase/jdk7-relnotes-418459.html.<br>
[5] Oracle.[Highlights of Technology Changes in Java SE 6](https://www.oracle.com/technetwork/java/javase/features-141434.html)[EB/OL].https://www.oracle.com/technetwork/java/javase/features-141434.html.<br>
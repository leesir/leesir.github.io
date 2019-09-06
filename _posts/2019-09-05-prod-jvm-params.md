---
layout: post
title: "生产环境JVM参数配置记录（一）"
description: ""
category: [Summary]
tags: [Summary]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;本文记录笔者参与的某Java服务的JVM启动参数。

<br>

#### -Djava.awt.headless=true 

&#160; &#160; &#160; &#160;从JDk1.4起，可以通过此参数以无外设（显示器、键盘等）模式运行JVM实例。无外设模式下，代码中仅能使用部分GUI组件，比如Canvas类。
当使用了Button和Frame等依赖于显示设备的类时，将会抛出HeadlessException。

&#160; &#160; &#160; &#160;当然，在Java服务端开发中，不会用到java.awt.*的类，使用此参数可以防止误拷贝了Java GUI开发的代码所引发的问题。

<br>

#### -Djava.net.preferIPv4Stack=true 

&#160; &#160; &#160; &#160;此参数默认为false，如果操作系统支持IPv6，JVM可以与IPv4及IPv6主机进行socket通信。
设置为true，则告诉JVM仅允许与IPv4主机进行socket通信。

<br>


{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_09_05_image1.png)

{:.center}
[图片来源](https://technology.amis.nl/2018/12/09/jvm-performance-openj9-uses-least-memory-graalvm-most-openjdk-distributions-differ/)

<!-- more -->

<br>

#### -server

&#160; &#160; &#160; &#160;以服务器模式运行JVM。64位JDK仅支持server模式，相对于client模式，server模式的初始堆和运行时垃圾回收都会不同。
server模式不关注启动速度，关注运行时的效率和优化。有关server和client模式的选择，请点击[这里](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/server-class.html)。

<br>

#### -Xmx2048M -Xms2048M 

&#160; &#160; &#160; &#160;Xms为初始堆配置，Xmx为最大堆配置，此配置告诉JVM初始化一个大小为2G的堆。通常将这两个参数设置为一样，避免JVM在运行时动态申请或者缩减堆的大小。

<br>

#### -Xmn1024M 

&#160; &#160; &#160; &#160;此参数设置新生代为1G，新生代既不能太大也不能太小，否则会导致GC性能不佳。[官方文档](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html)
推荐的新生代大小为整堆的1/4~1/2。

<br>

#### -XX:SurvivorRatio=7 

&#160; &#160; &#160; &#160;设置新生代中EDEN和1个survivor的比例，默认值为8，既新生代等于10个survivor的大小。此命令完全可以不用设置，延用默认值即可。

<br>

#### -Xss256k 

&#160; &#160; &#160; &#160;设置线程栈大小为256k。这个值没有明确的标准，通常建议32位JVM设置为128k，64位JVM设置为256k。
在某些环境下，Xss设置过大或者过小，启动会失败，比如提示“The stack size specified is too small, Specify at least 228k”。

<br>

#### -XX:PermSize=128M  -XX:MaxPermSize=256m 

&#160; &#160; &#160; &#160;设置永久代初始容量和最大容量，由于JDK1.8之后，运行时内存取消了永久代区域，所以这两个参数在JDk1.8及以后版本被弃用。
可以用-XX:MetaspaceSize和-XX:MaxMetaspaceSize参数作为替代。

<br>

#### -XX:+UseConcMarkSweepGC -XX:+UseParNewGC 

&#160; &#160; &#160; &#160;设置新生代ParNew和老年代CMS的垃圾收集器组合。默认情况下-XX:+UseConcMarkSweepGC是关闭的，如果没有显示设置垃圾收集器，JVM将会自动挑选组合。
另外也可以关注一下G1收集器，JDK1.8版本以后，-XX:+UseG1GC参数可以启用G1收集器。

<br>

#### -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=80

&#160; &#160; &#160; &#160;开启UseCMSInitiatingOccupancyOnly标识之后，JVM始终以预设的堆内存使用比例为条件，启动CMS收集器。
默认情况下UseCMSInitiatingOccupancyOnly标识为关闭状态，由JVM自行决策，除非有明确的理由，否则不建议开启此标志。

&#160; &#160; &#160; &#160;XX:CMSInitiatingOccupancyFraction=80预设当堆内存使用率达80%时，启动CMS收集器。需要注意的时，CMS是并发收集器，
执行过程中应用程序依然会产生许多垃圾，当老年代新增的垃圾速度大于回收的速度时，则可能会导致CMS收集器的“concurrent mode failure”，会临时启用老年代单线程Serial Old收集器进行收集工作，停顿时间会更长。
所以应结合实际情况，适当调整CMSInitiatingOccupancyFraction的阈值。

<br>

#### -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=5

&#160; &#160; &#160; &#160;CMS收集器采用“标记-清理”算法，在回收完成之后，必定会存在内存碎片。
UseCMSCompactAtFullCollection标志默认开启，表示在CMS回收之后进行内存压缩整理，而整理过程无法并发，所以必定会造成停顿。

&#160; &#160; &#160; &#160;-XX:CMSFullGCsBeforeCompaction=5表示在5次CMS回收之后，启动内存压缩整理。当设置为0时，表示每次回收之后都进行内存压缩整理。
应结合生产环境的情况，适当配置CMSFullGCsBeforeCompaction的值。

<br>

#### -XX:MaxTenuringThreshold=10

&#160; &#160; &#160; &#160;设置新生代对象进入老年代的年龄。-XX:MaxTenuringThreshold=10，当新生代对象经过10次垃圾回收之后依然存活，则晋升至老年代。
如果不进行预设，不同垃圾收集器有不同的默认值，比如Parallel Scavenge默认值为15，CMS默认值为6。

<br>

#### -XX:+DisableExplicitGC 

&#160; &#160; &#160; &#160;开启此标识，忽略System.gc()。实际编码过程中，也不应该显示调用System.gc()，把垃圾回收的任务委托给JVM即可。

<br>

#### -Xnoclassgc 

&#160; &#160; &#160; &#160;开启此标识后，垃圾回收器忽略对类对象的回收，应用程序所加载的所有类对象都会被认为是存活的，会在一定程度上节省GC时间。
然而如果应用导入了大量的JAR，大量使用JSP文件或者动态代理、反射等技术，内存中会保存大量的Class对象，长期运行的服务很可能会内存溢出。

<br>

## 参考文献

[1] Artem Ananiev.[Using Headless Mode in the Java SE Platform](https://www.oracle.com/technetwork/articles/javase/headless-136834.html)[EB/OL].https://www.oracle.com/technetwork/articles/javase/headless-136834.html，2019-09-05.<br>
[2] [Networking IPv6 User Guide for JDK/JRE 5.0](https://docs.oracle.com/javase/6/docs/technotes/guides/net/ipv6_guide/)[EB/OL].https://docs.oracle.com/javase/6/docs/technotes/guides/net/ipv6_guide/，2019-09-05.<br>
[3] [Java Platform, Standard Edition Tools Reference](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html)[EB/OL].https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html，2019-09-05.<br>
[4] [Server-Class Machine Detection](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/server-class.html)[EB/OL].https://docs.oracle.com/javase/8/docs/technotes/guides/vm/server-class.html，2019-09-05.<br>
---
layout: post
title: "缓存更新策略实践"
description: ""
category: [系统设计]
tags: [系统设计]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 前言

&#160; &#160; &#160; &#160;缓存在计算机的世界中是一个非常宽泛的概念，因为处处是缓存。操作系统可能缓存DNS的解析结果，浏览器可能缓存host文件，用户访问的资源可能缓存在CDN中，
CPU可能会缓存操作数所属的内存块，数据库的查询结果也可能是缓存好的。总之不管是在系统架构中的哪一部份，都有缓存的身影。所以，抛开具体应用谈缓存是没有意义的，那样过于空泛。
可以肯定的是，所有的缓存都是利用了数据的冗余提高性能，空间换时间，计算机的世界处处都是trade off。

&#160; &#160; &#160; &#160;本博文基于项目中的实际操作整理而来，仅讨论在DB和业务代码之间的缓存服务，主要分析笔者经历的项目中，根据不同的业务，采用了哪些更新策略（注，不讨论缓存的替换策略，如LRU等）。

<br>

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_06_27_image1.jpg)

{:.center}
[图片来源](http://web.sfc.keio.ac.jp/~rdv/computer-architecture-2018-wiki/index.php?Memory%20Subsystems)

<!-- more -->

<br>

## 缓存更新模式

&#160; &#160; &#160; &#160;缓存更新策略，主要包含以下几种：Cache Aside、Read/Write Through、Write Behind、Refresh Ahead。

#### 1.Cache Aside

&#160; &#160; &#160; &#160;业务系统中最常用的模式就属Cache Aside了。逻辑非常简单：

* 读操作如命中缓存，则返回缓存内容。否则查询数据库，将查询结果存入缓存，再返回查询结果。
* 写操作先将数据库更新，待更新成功后，再清除缓存。

&#160; &#160; &#160; &#160;在写操作中，如果先清除缓存，再更新数据库，缓存中出现脏数据的概率会大很多。因为读操作通常比写操作多的多，并且写操作会因为数据库锁（表锁还是行锁，视表的存储引擎和索引使用情况而定）和更新索引而比读操作慢的多，以至于在写操作清除缓存之后更新数据之前，很可能已经被另一个读操作将脏数据更新到了缓存中。

&#160; &#160; &#160; &#160;在笔者的项目中，Cache Aside由spring-context提供给的Cacheable和CacheEvict注解实现。

{% highlight java %}
//需要缓存时，用Cacheable
@Cacheable(value = "getItem", key = "'item_' + #id", unless = "#result == null")
{% endhighlight %}



#### 2.Read Through

## 参考文献

[1] 陈皓.[缓存更新的套路](https://coolshell.cn/articles/17416.html)[EB/OL].https://coolshell.cn/articles/17416.html，2019-06-28.<br>
[2] [Read-Through, Write-Through, Write-Behind, and Refresh-Ahead Caching](https://docs.oracle.com/cd/E15357_01/coh.360/e15723/cache_rtwtwbra.htm#COHDG5177)[EB/OL].https://docs.oracle.com/cd/E15357_01/coh.360/e15723/cache_rtwtwbra.htm#COHDG5177，2019-06-30.<br>
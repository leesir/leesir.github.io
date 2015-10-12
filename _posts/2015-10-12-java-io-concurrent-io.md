---
layout: post
title: "Java IO: 并发IO"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://tutorials.jenkov.com/java-io/concurrent-io.html) 作者: Jakob Jenkov

　　有时候你可能需要并发地处理输入和输出。换句话说，你可能有超过一个线程处理输入和产生输出。比如，你有一个程序需要处理磁盘上的大量文件，这个任务可以通过并发操作提高性能。又比如，你有一个web服务器或者聊天服务器，接收许多连接和请求，这些任务都可以通过并发获得性能的提升。

<!-- more -->

　　如果你需要并发处理IO，这里有几个问题可能需要注意一下：

　　在同一时刻不能有多个线程同时从InputStream或者Reader中读取数据，也不能同时往OutputStream或者Writer里写数据。你没有办法保证每个线程读取多少数据，以及多个线程写数据时的顺序。

　　如果线程之间能够保证操作的顺序，它们可以使用同一个stream、reader、writer。比如，你有一个线程判断当前的输入流来自哪种类型的请求，然后将流数据传递给其他合适的线程做后续处理。当有序存取流、reader、writer时，这种做法是可行的。请注意，在线程之间传递流数据的代码应当是同步的。

　　注意：在Java NIO中，你可以让一个线程读写多个“channel”。比如，你有很多网络连接处于开启状态，但是每个连接中都只有少量数据，类似于聊天服务器，可以让一个线程监视多个频道(连接)。Java NIO是另一个话题了，会后续教程中介绍。


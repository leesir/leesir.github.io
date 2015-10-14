---
layout: post
title: "Java IO: 字符流的Buffered和Filter"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

作者: Jakob Jenkov

　　本章节将简要介绍缓冲与过滤相关的reader和writer，主要涉及BufferedReader、BufferedWriter、FilterReader、FilterWriter。

#BufferedReader

　　[原文链接](http://tutorials.jenkov.com/java-io/bufferedreader.html)

　　BufferedReader能为字符输入流提供缓冲区，可以提高许多IO处理的速度。你可以一次读取一大块的数据，而不需要每次从网络或者磁盘中一次读取一个字节。特别是在访问大量磁盘数据时，缓冲通常会让IO快上许多。

　　BufferedReader和BufferedInputStream的主要区别在于，BufferedReader操作字符，而BufferedInputStream操作原始字节。只需要把Reader包装到BufferedReader中，就可以为Reader添加缓冲区(译者注：默认缓冲区大小为8192字节，即8KB)。代码如下：

    {% highlight java %} 
Reader input = new BufferedReader(new FileReader("c:\\data\\input-file.txt"));
    {% endhighlight %}
	
　　你也可以通过传递构造函数的第二个参数，指定缓冲区大小，代码如下：
	
	{% highlight java %} 
Reader input = new BufferedReader(new FileReader("c:\\data\\input-file.txt"), 8 * 1024);
    {% endhighlight %}
	
　　这个例子设置了8KB的缓冲区。最好把缓冲区大小设置成1024字节的整数倍，这样能更高效地利用内置缓冲区的磁盘。
	
　　除了能够为输入流提供缓冲区以外，其余方面BufferedReader基本与Reader类似。BufferedReader还有一个额外readLine()方法，可以方便地一次性读取一整行字符。
	
<!-- more -->
	
#BufferedWriter

　　[原文链接](http://tutorials.jenkov.com/java-io/bufferedwriter.html)

　　与BufferedReader类似，BufferedWriter可以为输出流提供缓冲区。可以构造一个使用默认大小缓冲区的BufferedWriter(译者注：默认缓冲区大小8 * 1024B)，代码如下：

    {% highlight java %} 
Writer writer = new BufferedWriter(new FileWriter("c:\\data\\output-file.txt"));
    {% endhighlight %}
	
	也可以手动设置缓冲区大小，代码如下：

	{% highlight java %} 
Writer writer = new BufferedWriter(new FileWriter("c:\\data\\output-file.txt"), 8 * 1024);
    {% endhighlight %}
	
　　为了更好地使用内置缓冲区的磁盘，同样建议把缓冲区大小设置成1024的整数倍。除了能够为输出流提供缓冲区以外，其余方面BufferedWriter基本与Writer类似。类似地，BufferedWriter也提供了writeLine()方法，能够把一行字符写入到底层的字符输出流中。值得注意是，你需要手动flush()方法确保写入到此输出流的数据真正写入到磁盘或者网络中。

#FilterReader

　　[原文链接](http://tutorials.jenkov.com/java-io/filterreader.html)

　　与FilterInputStream类似，FilterReader是实现自定义过滤输入字符流的基类，基本上它仅仅只是简单覆盖了Reader中的所有方法。

　　就我自己而言，我没发现这个类明显的用途。除了构造函数取一个Reader变量作为参数之外，我没看到FilterReader任何对Reader新增或者修改的地方。如果你选择继承FilterReader实现自定义的类，同样也可以直接继承自Reader从而避免额外的类层级结构。

#FilterWriter

　　[原文链接](http://tutorials.jenkov.com/java-io/filterwriter.html)

　　内容同FilterReader，不再赘述。
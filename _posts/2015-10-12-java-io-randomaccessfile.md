---
layout: post
title: "Java IO: RandomAccessFile"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://tutorials.jenkov.com/java-io/randomaccessfile.html) 作者: Jakob Jenkov

　　RandomAccessFile允许你来回读写文件，也可以替换文件中的某些部分。FileInputStream和FileOutputStream没有这样的功能。

#创建一个RandomAccessFile

　　在使用RandomAccessFile之前，必须初始化它。这是例子：：

    {% highlight java %} 

RandomAccessFile file = new RandomAccessFile("c:\\data\\file.txt", "rw");

    {% endhighlight %}
	
　　请注意构造函数的第二个参数：“rw”，表明你以读写方式打开文件。请查阅Java文档获知你需要以何种方式构造RandomAccessFile。

<!-- more -->

#在RandomAccessFile中来回读写

　　在RandomAccessFile的某个位置读写之前，必须把文件指针指向该位置。通过seek()方法可以达到这一目标。可以通过调用getFilePointer()获得当前文件指针的位置。例子如下：

    {% highlight java %} 

RandomAccessFile file = new RandomAccessFile("c:\\data\\file.txt", "rw");

file.seek(200);

long pointer = file.getFilePointer();

file.close();

    {% endhighlight %}

#读取RandomAccessFile

　　RandomAccessFile中的任何一个read()方法都可以读取RandomAccessFile的数据。例子如下：

    {% highlight java %} 

RandomAccessFile file = new RandomAccessFile("c:\\data\\file.txt", "rw");

int aByte = file.read();

file.close();

    {% endhighlight %}
	
#写入RandomAccessFile

　　RandomAccessFile中的任何一个write()方法都可以往RandomAccessFile中写入数据。例子如下：

    {% highlight java %} 

RandomAccessFile file = new RandomAccessFile("c:\\data\\file.txt", "rw");

file.write("Hello World".getBytes());

file.close();

    {% endhighlight %}
	
　　与read()方法类似，write()方法在调用结束之后自动移动文件指针，所以你不需要频繁地把指针移动到下一个将要写入数据的位置。
	
#RandomAccessFile异常处理

　　为了本篇内容清晰，暂时忽略RandomAccessFile异常处理的内容。RandomAccessFile与其他流一样，在使用完毕之后必须关闭。想要了解更多信息，请参考[Java IO异常处理](http://leesir.github.io/2015/10/java-io-exception)。
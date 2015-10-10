---
layout: post
title: "Java IO 字节和字符数组"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://tutorials.jenkov.com/java-io/arrays.html) 作者: Jakob Jenkov

　　Java中的字节和字符数组经常被用于临时存储应用程序内部的数据。所以数组也是常见的数据来源以及数据流目的地。如果你在程序执行过程中需要频繁访问文件的内容，你可能会愿意将文件加载到数组中去。当然你可以通过索引直接访问这些数组。但是如果你有一个组件的设计初衷是从InputStream或者Reader而非数组中读取某些数据呢？

<!-- more -->

#通过InputStream或者Reader读取数组

　　为了让你的组件能够从数组中读取数据，你需要把字节或者字符数组包装到一个ByteArrayInputStream或者CharArrayReader中。这种方式允许通过包装好的stream或者reader读取数组中的字节或者字符数据。这是一个简单的示例：

    {% highlight java %} 

byte[] bytes = new byte[1024];
//write data into byte array...
InputStream input = new ByteArrayInputStream(bytes);
//read first byte
int data = input.read();
while(data != -1) {
    //do something with data
	//read next byte
	data = input.read();
}

    {% endhighlight %}

　　操作一个字符数组的代码与本例类似，只需要将字符数组包装到CharArrayReader中。

#通过InputStream或者Reader写入数组

　　同样可以将数据写入到ByteArrayOutputStream或者CharArrayWriter中。你所需要做的是创建一个ByteArrayOutputStream或者CharArrayWriter，然后写入数据，就像你操作其他类型的stream或者writer一样。当所有的数据都写入完毕，只需调用toByteArray()或者toCharArray()，即可得到写入数据的数组形式。这是一个简单的示例：

    {% highlight java %} 

OutputStream output = new ByteArrayOutputStream();
output.write("This text is converted to bytes".toBytes("UTF-8"));
byte[] bytes = output.toByteArray();

    {% endhighlight %}
	

　　操作一个字符数组的代码也与本例类似，只需要将字符数组包装到CharArrayWriter中。

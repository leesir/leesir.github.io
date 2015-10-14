---
layout: post
title: "Java IO: Buffered和Data"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

作者: Jakob Jenkov

　　本小节会简要概括Java IO中Buffered和data的输入输出流，主要涉及以下4个类型的流：BufferedInputStream，BufferedOutputStream，DataInputStream，DataOutputStream。

#BufferedInputStream

　　[原文链接](http://tutorials.jenkov.com/java-io/bufferedinputstream.html)

　　BufferedInputStream能为输入流提供缓冲区，能提高很多IO的速度。你可以一次读取一大块的数据，而不需要每次从网络或者磁盘中一次读取一个字节。特别是在访问大量磁盘数据时，缓冲通常会让IO快上许多。

　　为了给你的输入流加上缓冲，你需要把输入流包装到BufferedInputStream中，代码如下：

    {% highlight java %} 

InputStream input = new BufferedInputStream(new FileInputStream("c:\\data\\input-file.txt"));

    {% endhighlight %}
	
　　很简单，不是吗？你可以给BufferedInputStream的构造函数传递一个值，设置内部使用的缓冲区设置大小(译者注：默认缓冲区大小8 * 1024B)，就像这样：

    {% highlight java %} 

InputStream input = new BufferedInputStream(new FileInputStream("c:\\data\\input-file.txt"), 8 * 1024);

    {% endhighlight %}
　　
　　这个例子设置了8KB的缓冲区。最好把缓冲区大小设置成1024字节的整数倍，这样能更高效地利用内置缓冲区的磁盘。

　　除了能够为输入流提供缓冲区以外，其余方面BufferedInputStream基本与InputStream类似。
	
<!-- more -->
	
#BufferedOutputStream

　　[原文链接](http://tutorials.jenkov.com/java-io/bufferedoutputstream.html)
	
　　与BufferedInputStream类似，BufferedOutputStream可以为输出流提供缓冲区。可以构造一个使用默认大小缓冲区的BufferedOutputStream(译者注：默认缓冲区大小8 * 1024B)，代码如下：

    {% highlight java %} 
OutputStream output = new BufferedOutputStream(new FileOutputStream("c:\\data\\output-file.txt"));
    {% endhighlight %}
	
　　也可以手动设置缓冲区大小，代码如下：

    {% highlight java %}
OutputStream output = new BufferedOutputStream(new FileOutputStream("c:\\data\\output-file.txt"), 8 * 1024);
    {% endhighlight %}
	
　　为了更好地使用内置缓冲区的磁盘，同样建议把缓冲区大小设置成1024的整数倍。
	
　　除了能够为输出流提供缓冲区以外，其余方面BufferedOutputStream基本与OutputStream类似。唯一不同的时，你需要手动flush()方法确保写入到此输出流的数据真正写入到磁盘或者网络中。
	
#DataInputStream

　　[原文链接](http://tutorials.jenkov.com/java-io/datainputstream.html)
	
　　DataInputStream可以使你从输入流中读取Java基本类型数据，而不必每次读取字节数据。你可以把InputStream包装到DataInputStream中，然后就可以从此输入流中读取基本类型数据了，代码如下：
	
    {% highlight java %}
DataInputStream input = new DataInputStream(new FileInputStream("binary.data"));

int aByte = input.read();

int anInt = input.readInt();

float aFloat = input.readFloat();

double aDouble = input.readDouble();//etc.

input.close();
    {% endhighlight %}
	
　　当你要读取的数据中包含了int，long，float，double这样的基本类型变量时，DataInputStream可以很方便地处理这些数据。
	
#DataOutputStream

　　[原文链接](http://tutorials.jenkov.com/java-io/dataoutputstream.html)

　　DataOutputStream可以往输出流中写入Java基本类型数据，例子如下：

    {% highlight java %} 

DataOutputStream output = new DataOutputStream(new FileOutputStream("binary.data"));

output.write(45);

//byte data output.writeInt(4545);

//int data output.writeDouble(109.123);

//double data  output.close();

    {% endhighlight %}
	
　　其他方面与DataInputStream类似，不再赘述。
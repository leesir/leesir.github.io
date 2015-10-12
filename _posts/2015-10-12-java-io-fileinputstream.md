---
layout: post
title: "Java IO: FileInputStream"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://tutorials.jenkov.com/java-io/fileinputstream.html) 作者: Jakob Jenkov

　　FileInputStream可以以字节流的形式读取文件内容。FileInputStream是InputStream的子类，这意味着你可以把FileInputStream当做InputStream使用(FileInputStream与InputStream的行为类似)。

　　这是一个FileInputStream的例子：

    {% highlight java %} 

InputStream input = new FileInputStream("c:\\data\\input-text.txt");

int data = input.read();while(data != -1) {

    //do something with data...

    doSomethingWithData(data);

    data = input.read();

}

input.close();

    {% endhighlight %}

<!-- more -->

　　请注意，为了清晰，这里忽略了必要的异常处理。想了解更多异常处理的信息，请参考[Java IO异常处理](http://leesir.github.io/2015/10/java-io-exception/)。

　　FileInputStream的read()方法返回读取到的包含一个字节内容的int变量(译者注：0~255)。如果read()方法返回-1，意味着程序已经读到了流的末尾，此时流内已经没有多余的数据可供读取了，你可以关闭流。-1是一个int类型，不是byte类型，这是不一样的。

　　FileInputStream也有其他的构造函数，允许你通过不同的方式读取文件。请参考[官方文档](http://docs.oracle.com/javase/7/docs/api/)查阅更多信息。

　　其中一个FileInputStream构造函数取一个File对象替代String对象作为参数。这里是一个使用该构造函数的例子：

    {% highlight java %}

File file = new File("c:\\data\\input-text.txt");

InputStream input = new FileInputStream(file);

    {% endhighlight %}

　　至于你该采用参数是String对象还是File对象的构造函数，取决于你当前是否已经拥有一个File对象，也取决于你是否要在打开FileOutputStream之前通过File对象执行某些检查(比如检查文件是否存在)。
---
layout: post
title: "Java IO 网络"
description: ""
category: [译文]
tags: [Java IO]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://tutorials.jenkov.com/java-io/networking.html) 作者: Jakob Jenkov

　　Java中网络的内容或多或少地超出了Java IO的范畴。关于Java网络更多的是在我的Java网络教程中探讨。但是既然网络是一个常见的数据来源以及数据流向地，并且因为你使用Java IO API通过网络连接进行通信，本文将简要触及网络。

　　当两个进程之间建立了网络连接之后，他们通信的方式如同操作文件一样：利用InputStream读取数据，利用OutputStream写入数据。换句话来说，Java网络API用来在不同进程之间建立网络连接，而Java IO则用来在建立了连接之后的进程之间交换数据。

<!-- more -->

　　基本上意味着如果你有一份能够对文件进行写入某些数据的代码，那么这些数据也可以很容易地写入到网络连接中去。你所需要做的仅仅只是在代码中利用InputStream替代FileInputStream进行数据的写入。因为FileInputStream是InputStream的子类，所以这么做并没有什么问题。(译者注：此处应该是OutputStream和FileOutputStream)

　　实际上对于文件的读操作也类似，一个具有读取文件数据功能的组件，同样可以轻松读取网络连接中的数据。只需要保证读取数据的组件是基于InputStream而非FileInputStream即可。

　　这是一份简单的代码示例：

    {% highlight java %} 

public class MyClass {
    public static void main(String[] args) {
        InputStream inputStream = new FileInputStream("c:\\myfile.txt");
        process(inputStream);
    }
    public static void process(InputStream input) throws IOException {
        //do something with the InputStream
    } 
}

    {% endhighlight %}

　　在这个例子中，process()方法并不关心InputStream参数的输入流来自于文件还是网络(例子只展示了输入流来自文件的版本)。process()方法只会对InputStream进行操作。

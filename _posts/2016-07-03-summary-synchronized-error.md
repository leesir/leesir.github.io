---
layout: post
title: "对包装类型变量使用synchronized不当造成的同步不正确"
description: ""
category: [Summary]
tags: [Summary]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

关于关键字synchronized使用不当，造成未同步或者同步不正确，是开发过程中常见的问题。先引出3个问题：

1. 同步synchronized的底层实现原理是什么？
2. 对非常量或者非单例对象上使用synchronized，会有什么效果？
3. 对包装类型使用synchronized，会有什么效果？

<!-- more -->

#### 1. 同步synchronized的底层实现原理是什么？

在对象上使用synchronized，会尝试获取对象的锁。获取成功之后，程序可以进入到同步代码块中执行逻辑。如果获取失败，则会进入到等待该对象锁的等待队列中。试图获取null的锁的操作都将抛出空指针异常，null在虚拟机内部的表示不属于虚拟机规范，所以不同的虚拟机可能会有不同的实现方式。

对象锁是非公平的（可以使用java.util.concurrent.locks.Lock的实现类实现公平锁，当然会有额外的性能消耗），也就是说，唤醒线程时，并不参考其等待时间，在极端情况下，线程会出现饿死的情况。

通过synchronized使用对象锁，不需要显示地获取或者释放锁。在编译（前端编译，即javac或者类似编译器，不是JIT编译）过后，如果synchronized同步的是代码块，字节码文件中会存在monitorenter与monitorexit，2个虚拟机指令实现代码块的进入与退出，这也是虚拟机规范之一。而同步方法则不会显示这两个指令，因为同步方法的实现细节不属于虚拟机规范，各个商用虚拟机会有不同的细节，当然，也可以通过monitorenter和monitorexit来实现。感兴趣的读者可以动手写个简单的类，通过命令查看反编译后的执行指令。

```
javap -c -p yourPath/TestClass
```
注：TestClass是类名，不是TestClass.class或者TestClass.java等文件名。
#### 2. 对非常量或者非单例对象上使用synchronized，会有什么效果？

理解了synchronized的实现原理，就来看看对非常量或者非单例对象上使用synchronized，会有什么效果。

显而易见，当不是常量或者非单例，不同的执行线程有可能会在不同的对象上获取、释放锁，这些不同的对象是不会互相影响的，所以有可能会出现没有同步的情况。

所以，当我们使用一个内部私有对象实现同步的时候，需要将该变量定义成常量，如：

{% highlight java %}
private static final Object monitor = new Object();
{% endhighlight %}

对于Spring等对象容器来说，你需要关注通过配置文件往容器加入的对象，是否是单例（Spring默认是单例，既scope = singleton）。非单例的对象，当应用程序向容器索取对象时，容器可能会返回一个新建的对象。

#### 3. 对包装类型使用synchronized，会有什么效果？

首先，因为包装类型是对象，所以对包装类型使用synchronized同样适用第2点，即存在非常量及非单例对同步逻辑的影响。

其次，包装类型有特殊的逻辑。当把基本变量赋值给包装类型的变量（其实编译过后的操作就是调用包装类型的静态方法valueOf）或者调用静态valueOf方法时：

* Boolean返回的是缓存的对象。
* 整型（Byte,Short,Integer,Long）会检查该数字是否在1个字节可表示的有符号整数范围内（-128~127），是则返回缓存对象，否则返回新对象。
* Character会缓存整型值为0~127的字符，同样会检查字符是否落在缓存范围中，是则返回，否则返回新对象。
* Double和Float的valueOf方法始终返回新对象。

所以，当我们对非常量的包装类型，如Boolean类型的变量上使用同步时，比如：

{% highlight java %}
public Boolean flag = false;

...
//thread1
synchronized (flag) {
...
}
{% endhighlight %}

如果另外的线程thread2执行了flag = true，即flag = Boolean.valueOf(true)，之后又马上获取flag的对象锁，假设Boolean缓存的TRUE的对象锁可用的情况下，线程会立即执行同步代码，此时程序的行为看上去像是同步失效了。

不过运气好的话，执行flag = false后立即获取对象锁，则thread2会等到thread1执行完毕后才进入同步代码块，同步会生效，因为获取的是同一个对象锁。

因此，这种程序不仅是面向对象程序，还是面向运气程序。
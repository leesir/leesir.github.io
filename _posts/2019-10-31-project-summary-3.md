---
layout: post
title: "项目总结-设计模式的应用"
description: ""
category: [Summary]
tags: [Summary]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

&#160; &#160; &#160; &#160;本文对笔者经历的项目中关于设计模式的应用，进行简单的阶段性总结。

<br>

## 主动使用的设计模式

<br>

#### 策略模式 & 工厂模式

&#160; &#160; &#160; &#160;对放款主流程来说，不管采用何种还款方式（比如等额本息、等额本金等），除了计算方式不同造成的结果差异之外，不会产生业务逻辑的差异。因此，将每个还款方式封装成单独的策略，给每个策略分配一个整型的type，在计算计划表时，根据type获取相应策略，可以直接获取结果。伪代码如下所示：

{% highlight java %}


{% endhighlight %}

{:.center}
代码清单1

<br>

#### 模板方法模式 

&#160; &#160; &#160; &#160;借款人进行还款时，需要进行相应的检查，检查通过之后再进行还款。伪代码如下所示：

{% highlight java %}
//abstract repay service
public Result repay(RepayParams params) {
	preCheck(params);
	doRepay(params);
}

public abstract Result preCheck(RepayParams params);

public abstract Result doRepay(params);

{% endhighlight %}

{:.center}
代码清单2

&#160; &#160; &#160; &#160;需要针对每种还款方式，实现其各自的逻辑，例如需要实现借款人还款服务，代偿还款服务，垫付还款服务等。

<br>

#### 工厂模式

#### 外观模式

webservice的胶水代码

## 间接使用的设计模式

#### 观察者模式

MQ

#### 代理模式

RPC

#### 单例模式

#### 命令模式

hystrix的运用

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_09_05_image1.png)

{:.center}
[图片来源](https://technology.amis.nl/2018/12/09/jvm-performance-openj9-uses-least-memory-graalvm-most-openjdk-distributions-differ/)

<!-- more -->

<br>



<br>

## 参考文献

[1] 
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

{:.center}
![c compile]({{ site.baseurl }}/images/post_2019_10_31_image1.jpg)

{:.center}
[图片来源](https://sandeepdass003.wordpress.com/2018/02/23/design-patterns-in-software-design/)

## 主动使用的设计模式

<br>

#### 策略模式 & 简单工厂模式

<br>

&#160; &#160; &#160; &#160;对放款主流程来说，不管采用何种还款方式（比如等额本息、等额本金等），除了计算方式不同造成的结果差异之外，不会产生业务逻辑的差异。因此，将每个还款方式封装成单独的策略，给每个策略分配一个整型的type，在计算计划表时，根据type获取相应策略，可以直接获取结果。伪代码如下所示：

{% highlight java %}
//简单工厂方法
public RepayStrategy getRepayStrategy(int type) {
    switch (type) {
        //for each case return corresponding instance
    }
    throw new RuntimeException("unsupported type");
}

...

//放款主流程，生成相应的收益计划
public Result transfer(TransferParams params) {
    int repayType = params.getType();
    RepayStrategy repayStrategy = Factory.getRepayStrategy(type);
    List<Plan> planList = repayStrategy.generatePlanList(params);
    //subsequent process
}

{% endhighlight %}

{:.center}
代码清单1

<!-- more -->

<br>

#### 模板方法模式 

<br>

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

<br>

&#160; &#160; &#160; &#160;需要针对每种还款方式，实现其各自的逻辑，例如需要实现借款人还款服务，代偿还款服务，垫付还款服务等。

<br>

#### 外观模式

&#160; &#160; &#160; &#160;不管后端应用是单点web系统，还是服务化的分布式应用，总会有一层代码专门用于给客户端请求拼装数据。这部分代码通常与前端业务紧密相连，可能会高频率更新。如果考虑到版本兼容性，某个功能还可能存在多个版本。

&#160; &#160; &#160; &#160;不管这层代码叫做facade还是controller，其作用在于给客户端提供一个简化的调用方式，一定程度上屏蔽了后方子系统的复杂性。以账户中心为例，通常这个页面需要展示的信息有：

1. 用户数据：姓名，注册时间等。
2. 交易数据：用户余额，累计投资金额，累计收益等。
3. 营销数据：红包、优惠券等。
4. 其他数据：系统消息、登陆记录等。

&#160; &#160; &#160; &#160;暂时不考虑性能问题的话，这个接口通常会这么写：

{% highlight java %}
public Result getAccountCenterData(long userId) {
    Result result = new Result();
    //交易数据
    result.put("trade", tradeService.getData(userId));
    //营销数据
    result.put("activty", actService.getData(userId));
    //用户数据
    result.put("user", userService.getData(userId));
    //系统数据
    result.put("system", systemService.getData(userId));
    return result;
}
{% endhighlight %}

{:.center}
代码清单3

<br>

&#160; &#160; &#160; &#160;代码清单3中的getAccountCenterData几乎没有自己的业务，属于外观接口，这样的代码组织形式则属于外观模式。

<br>

## 间接使用的设计模式

<br>

&#160; &#160; &#160; &#160;这里简单列举项目中间接使用到的设计模式。

<br>

#### 观察者模式

* 消息队列的消息生产-消费模型。
* dubbo的服务生产者和消费者在启动后，都会订阅注册中心的某些数据变更通知（消费者订阅服务提供者、元数据、路由，生产者订阅元数据，dubboAdmin订阅所有生产者、消费、路由、元数据）。

#### 代理模式

* RPC框架用于屏蔽调用细节的代理类。
* 静态字节码增强、运行时字节码增强所生成的增强类（可以是接口的实现类，也可以是类的子类）。

#### 单例模式

* JVM的Runtime属于单例。
* Spring注入的实例默认为单例。

#### 命令模式

* Netflix的hystrix，利用命令模式，将调用封装实现熔断降级。

<br>

## 参考文献

[1] 埃里克·伽玛等.设计模式：可复用面向对象软件的基础[M].北京：机械工业出版社. <br>
[2] 弗里曼等.Head First设计模式[M].北京：中国电力出版社.
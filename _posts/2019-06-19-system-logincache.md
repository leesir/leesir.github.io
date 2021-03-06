---
layout: post
title: "一种基于redis的登录态的设计与实现"
description: ""
category: [系统设计]
tags: [系统设计]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

## 1.前言

<br>

&#160; &#160; &#160; &#160;本文浅析一种可行的、实现简单的登录态方案。登录态几乎是所有系统都要考虑的问题，旨在维护用户登录之后的状态。
“用户登录”这个功能已经发展很多年了，伴随着“用户登录”的演进，登录态的实现也在不断完善。从最简单的在url后加一个参数作为标记，到现在大型站点的SSO，
登录态方案从单应用单机到多应用集群，不断满足不同架构的需求，也不断满足安全方面的需求。

&#160; &#160; &#160; &#160;由于用户登录功能和登录态涉及的面非常广，比如密码的传输和存储、session共享、SSO、网络安全、第三方授权登录等，笔者见识尚浅，无法进行全面分析，
故本文仅就“登录态在服务器端的维护”一点进行阐述，上述方面不在本文讨论范围之内。笔者使用的redis版本为2.8.19，spring-data-redis版本为2.1.9。

<br>

{:.center}
![login cache]({{ site.baseurl }}/images/post_2019_06_19_image1.jpg){:height="60%" width="60%"}

<br>

<!-- more -->

## 2.功能点

<br>

* 维持状态

&#160; &#160; &#160; &#160;这是登录态最基本的功能。

* 时效

&#160; &#160; &#160; &#160;登录态需要有时效，不能无限期处于登录状态中。

* 安全

&#160; &#160; &#160; &#160;登录态需要防止伪造或窃取。

* 互斥

&#160; &#160; &#160; &#160;登录态需要在同类型客户端中实现互斥登录，比如pc上只能允许最后一次登录有效，app类似，但是pc和app的登录不能互相影响，做到隔离。

* 强制失效

&#160; &#160; &#160; &#160;用户修改密码，或者找回密码，需要强制所有客户端的登录态失效。

<br>

## 3.实现

<br>

&#160; &#160; &#160; &#160;此处仅给出基本方案，以及重要部分的代码实现。方案的最优化应与具体业务挂钩，故不讨论最优解，不追求完美。

<br>

#### a) 维持状态

&#160; &#160; &#160; &#160;想要维持状态，除了服务器需要存储登录态之外，客户端请求还必须带上sessionId或者token参数，使服务端能够把请求与登录态关联起来。
参数必须由服务器按一定规则生成，可以携带在cookie里，也可以携带在restful接口的公共参数里。

<br>

#### b) 时效

&#160; &#160; &#160; &#160;任何登录态的设计都要考虑时效的问题，避免永久登录。
* 可以记录到期时间，如果当前时间大于到期时间，则登录态失效。
* 可以记录登录时间，如果登录时间 + 登录态时效 < 当前时间，则登录态失效。
* 可以将登录态时效作为redis缓存的过期时间，由redis进行维护。服务端如果获取的登录态为空时，则登录态失效。

<br>

#### c) 安全

&#160; &#160; &#160; &#160;用户登录之后，可以访问所有要求登录权限的功能。但在登录之后的后续请求中，我们需要确定请求是否来自于登录动作完成的设备。

&#160; &#160; &#160; &#160;如果在请求参数里只带上了sessionId，通常不能满足安全性的要求。因为当sessionId泄露之后，用户的请求就可以被伪造。
服务端根据一个id不能正确判断请求是否来自于用户，存在安全漏洞，所以一般建议将用token来关联登录态。

&#160; &#160; &#160; &#160;这里涉及到客户端请求的重放和伪造，详情由此进入。

<br>

#### d) 互斥

&#160; &#160; &#160; &#160;用户在一个设备登录之后，通常要求踢掉同类型设备之前的登录态（注：互斥不是单点登录SSO）。可以将每个用户的登录成功记录，保存为redis的有序集合，
将sessionId作为member，登录成功的时间长当做score，代码如下：

{% highlight java %}
//redis命令，添加记录
ZADD login_record:key score sessionId

//java代码，value为sessionId，score为登录成功时间戳
ZSetOperations<String, String> zSetOperations = stringRedisTemplate.opsForZSet();
zSetOperations.add(key, sessionId, score);
stringRedisTemplate.expire(key, expireTimestamp, timeUnit);
{% endhighlight %}

&#160; &#160; &#160; &#160;通过score倒叙排序，获取第一个sessionId，很简单就能匹配出当前请求的sessionId是否是最后一次登录的id，不一致则返回报错。

{% highlight java %}
//redis命令，查找有序集合最新一次登录的sessionId
ZREVRANGE login_record:key 0 0

String sessionId = getSessionIdFromRequest();
ZSetOperations<String, String> zSetOperations = stringRedisTemplate.opsForZSet();
String lastLoginSessionId = zSetOperations.reverseRange(key, 0, 0);
if (!lastLoginSessionId.equals(sessionId)) {
    //报错
}
{% endhighlight %}

&#160; &#160; &#160; &#160;根据redis提供的有序集合，可以很高效的实现登录互斥。
在用户登录成功之后，将login_record:key的过期值设置为登录态有效期，过期自动清除内容，也不会占用很多缓存空间。而为了各端的登录互不影响，
可以将每个端的登录成功记录各保存一份，比如缓存键可以设计为：login_record:app_key，login_record:pc_key，login_record:m_key等。

<br>

#### d) 强制失效

&#160; &#160; &#160; &#160;当用户忘记密码、修改密码、修改了重要资料或其他临时紧急情况发生时，通常也需要将某用户的全站登录态做失效处理。
我们可以对各端的编号做如下定义：

{% highlight java %}
//客户端类型枚举
PC(1, "pc"),
H5(2, "m"),
ANDROID(4, "android"),
IOS(8, "ios"),
APP(12, "app");
{% endhighlight %}

&#160; &#160; &#160; &#160;给每个用户再维护一个哈希类型的强制登出控制变量，其中一个域表示需要强制登出的端的枚举类型二进制表示的整数，假设哈希field为needReloginTerminal，
则needReloginTerminal的值和需要强制退出的端的关系如下表所示（第一行表头为needReloginTerminal的值，打钩表示强制退出）：

<!--
|term-flag|   1   |   2   |   3   |   4   |   5   |   6   |   7   |   8   |   9   |   10  |   11  |   12  |   13  |   14  |   15  |
|:-------:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
| PC      |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|
| H5      |  X    |&radic;|&radic;|  X    |  X    |&radic;|&radic;|  X    |  X    |&radic;|&radic;|  X    |  X    |&radic;|&radic;|
| ANDROID |  X    |  X    |  X    |&radic;|&radic;|&radic;|&radic;|  X    |  X    |  X    |  X    |&radic;|&radic;|&radic;|&radic;|
| IOS     |  X    |  X    |  X    |  X    |  X    |  X    |  X    |&radic;|&radic;|&radic;|&radic;|&radic;|&radic;|&radic;|&radic;|
-->

{:.center}
![login cache]({{ site.baseurl }}/images/post_2019_06_19_image2.png){:height="100%" width="100%"}

<br>

&#160; &#160; &#160; &#160;当然在实际情况中，往往android和ios的客户端应视为一类，所以表格又可以简化成如下所示：

<!--
|term-flag|   1   |   2   |   3   |   12  |   13  |   14  |   15  |
|:-------:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
| PC      |&radic;|  X    |&radic;|  X    |&radic;|  X    |&radic;|
| H5      |  X    |&radic;|&radic;|  X    |  X    |&radic;|&radic;|
| APP     |  X    |  X    |  X    |&radic;|&radic;|&radic;|&radic;|
-->

{:.center}
![login cache]({{ site.baseurl }}/images/post_2019_06_19_image3.png){:height="80%" width="80%"}

<br>

&#160; &#160; &#160; &#160;当用户修改密码之后，我们要强制全端登录态失效，可以这么做：

{% highlight java %}
//redis命令
HSET userReloginFlag:userId needReloginTerminal 15

//Java代码
int terminal = getTerminalFromRequest();
HashOperations<String, String, Object> hashTemplate = stringRedisTemplate.opsForHash();
hashTemplate.put(userReloginFlag:userId, needReloginTerminal, 15);
{% endhighlight %}

&#160; &#160; &#160; &#160;上述代码执行完毕之后，标志位已经设置好了。下一次请求到来时，我们需要做响应的判断，位运算可以很方便实现这一点：

{% highlight java %}
//redis命令
HGET userReloginFlag:userId needReloginTerminal 15

//Java代码
int terminal = getTerminalFromRequest();
HashOperations<String, String, Object> hashTemplate = stringRedisTemplate.opsForHash();
int userReloginFlag = hashTemplate.get(userReloginFlag:userId, needReloginTerminal);
if (userReloginFlag & terminal > 0) {
    //清除登录态
}
{% endhighlight %}

&#160; &#160; &#160; &#160;当然有些业务可能还需要具体的报错信息，可以在哈希userReloginFlag:userId中增加一个域，定义每个端对应的错误信息。

<br>

## 4.总结

&#160; &#160; &#160; &#160;在登录态的校验过程中，可以采用责任链模式层层判断，只要某一层的校验失败，直接返回报错，而校验优先级顺序则依具体业务而定。
基于redis的登录态实现，足够高效，实现简单，能够满足大部分登录态的需求，是一种完全可行的方案。而如果将本方案用于实际业务，方案可能需要微调。
考虑的因素可能有：

1. 当redis抖动造成的登录态缓存丢失，是否会对业务有重大影响？
2. 是否对redis占用的内存大小非常敏感？
3. 登录态需要存储时间戳，以应对过期时间设置失效的问题。

<br>

## 5.参考文献

[1] 陈皓.[你会做WEB上的用户登录功能吗？](https://coolshell.cn/articles/5353.html)[EB/OL].https://coolshell.cn/articles/5353.html，2019-06-19.<br>
[2] 廖雪峰.[设计一个可扩展的用户登录系统](https://www.liaoxuefeng.com/article/1029274073038464)[EB/OL].https://www.liaoxuefeng.com/article/1029274073038464，2019-06-18.<br>
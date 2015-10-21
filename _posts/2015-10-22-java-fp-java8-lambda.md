---
layout: post
title: "一个Java 8中简单Lambda表达式程序"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2013/05/a-simple-application-of-lambda-expressions-in-java-8.html) 作者: [Mohamed Sanaulla](http://www.javacodegeeks.com/author/Mohamed-Sanaulla/)

　　我尝试过把Lambda表达式融入到我的代码中，下面的代码例子是此次尝试的结果。对于那些完全不知道Lambda表达式的Java程序员，我强烈建议在继续阅读之前，浏览一下[这篇文章](http://blog.sanaulla.info/2013/03/11/using-lambda-expression-to-sort-a-list-in-java-8-using-netbeans-lambda-support/)。

　　Ok，现在你已经熟悉Lambda表达式了（在阅读过推荐的Lambda入门文章之后），那我们现在开始学习一个我认为很好的Lambda表达式的例子。

　　考虑一下这种场景：某些操作在执行之前需要做预处理，执行之后需要做后期处理。待执行的操作会随着行为的不同而变化。预处理会提取出这个操作所需的必要参数，后期处理做一些清理的工作。

　　我们来看看如何利用接口与接口的匿名实现类模拟这个场景。

<!-- more -->

#使用匿名内部类

　　一个提供了必要行为方法的接口实现：

{% highlight java %}
interface OldPerformer {

    public void performTask(String id, int status);

}
{% endhighlight %}

　　接下来是一些预处理和后期处理方法：

{% highlight java %}
public class PrePostDemo {

    static void performTask(String id, OldPerformer performer) {

        System.out.println("Pre-Processing...");

        System.out.println("Fetching the status for id: " + id);

        int status = 3;

        //Some status value fetched

        performer.performTask(id, status);

        System.out.println("Post-processing...");

    }

}
{% endhighlight %}

　　我们需要传递2样东西：预处理所需的标识，以及操作的实现。如下所示：

{% highlight java %}
public class PrePostDemo {

    public static void main(String[] args) {

    //has to be declared final to be accessed within

    //the anonymous inner class.

    final String outsideOfImpl = "Common Value";

    performTask("1234", new OldPerformer() {

        @Override

        public void performTask(String id, int status) {

            System.out.println("Finding data based on id..."

        );

            System.out.println(outsideOfImpl);

            System.out.println("Asserting that the status matches");

        }

    });
	
	    performTask("4567", new OldPerformer() {

        @Override

        public void performTask(String id, int status) {

            System.out.println("Finding data based on id...");

            System.out.println(outsideOfImpl);

            System.out.println("Update status of the data found");

        }

    });

    }

}
{% endhighlight %}

　　从上面的代码可以看出，匿名内部类外部的变量要想被匿名内部类访问，需要声明成final。例子的代码输出如下：

{% highlight java %}
Pre-Processing...</pre>
Fetching the status for id: 1234

Finding data based on id...

Common Value

Asserting that the status matches

Post-processing...

PreProcessing...

Fetching the status for id: 4567

Finding data based on id...

Common Value

Update the status of the data found

Post-processing...

{% endhighlight %}

#使用Lambda表达式

　　我们来看看如何用Lambda表达式实现上述例子：

{% highlight java %}
public class PrePostLambdaDemo {

    public static void main(String[] args) {

        //Need not be declared as final for use within a

        //lambda expression, but has to be eventually final.

        String outsideOfImpl = "Common Value";

        doSomeProcessing("123", (String id, int status) -> {

            System.out.println("Finding some data based on"+id);

            System.out.println(outsideOfImpl);

            System.out.println("Assert that the status is "+status );

        });
		
		doSomeProcessing("456", (String id, int status) -> {

            System.out.print("Finding data based on id: "+id);

            System.out.println(outsideOfImpl);

            System.out.println("And updating the status: "+status);

        });

    }
	
	static void doSomeProcessing(String id, Performer performer ){

        System.out.println("Pre-Processing...");

        System.out.println("Finding status for given id: "+id);

        int status = 2;

        performer.performTask(id, status);

        System.out.println("Post-processing...");

    }

}

interface Performer{

    public void performTask(String id, int status);

}
{% endhighlight %}

　　除了有趣的Lambda表达式语法之外，还有一点不同，那就是Lambda表达式外部的变量没有声明成final。但最终变量还是会成为常量，这意味着outsideOfImpl 在声明及初始化之后的值不能被更改。

　　这个例子只是展示了如何使用清晰明了的Lambda表达式代替匿名内部类。

　　一个小提示：JDK8的发布时间推迟到了2014年2月，完整的发布时间表可以从[这里](http://openjdk.java.net/projects/jdk8/)查阅。我每天都会更新Lambda的构建项目，如果在最新的构建中出现了任何问题，请随时联系我。我会尽最大努力持续构建，并且会在这里发表最新的例子。

　　另一个提示：不要让Java 8的更新使你不知所措，大部分新特性已经存在于其他编程语言中。我发现学习Lambda表达式的语法和方法可以帮助我以函数式的思维进行思考，对此我还应特别感谢Scala闭包。



　　
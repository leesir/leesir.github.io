---
layout: post
title: "采用Java 8中Lambda表达式和默认方法的模板方法模式"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2013/05/template-method-pattern-using-lambda-expressions-default-methods.html) 作者: [Mohamed Sanaulla](http://www.javacodegeeks.com/author/Mohamed-Sanaulla/)

　　模板方法模式是“四人帮”（译者注：Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides）所著[《Design Patterns book》](http://www.flipkart.com/design-patterns-elements-reusable-object-oriented-software-1st/p/itmdytsswfy3qeqn?pid=9788131700075&affid=INMohamblo)一书中所描述的23种设计模式其中的一种，该模式旨在：

>  "Define the skeleton of an algorithm in an operation, deferring some steps to subclasses.
>  TemplateMethod lets subclasses redefine certain steps of an algorithm without changing the algorithm’s structure"。

　　即模板方法定义一个算法的架构，并将某些步骤推迟到子类中实现。模板方法允许子类在不改变算法架构的情况下，重新定义算法中某些步骤。

　　为了以更简单的术语描述模板方法，考虑这个场景：假设在一个工作流系统中，为了完成任务，有4个任务必须以给定的执行顺序执行。在这4个任务中，不同工作流系统的实现可以根据自身情况自定义任务的执行内容。

　　模板方法可以应用在上述场景中：将工作流系统的4个核心任务封装到抽象类当中，如果任务可以被自定义，则将可自定义的任务推迟到子类中实现。

<!-- more -->

　　代码实现：

{% highlight java %}
/** 
 * Abstract Workflow system 
 */
abstract class WorkflowManager2{

    public void doTask1(){

        System.out.println("Doing Task1...");

    }

    public abstract void doTask2();

    public abstract void doTask3();

    public void doTask4(){

        System.out.println("Doing Task4...");

    }

}

/** 
 * One of the extensions of the abstract workflow system 
 */
class WorkflowManager2Impl1 extends WorkflowManager2{

    @Override
    public void doTask2(){

        System.out.println("Doing Task2.1...");

    }

    @Override
    public void doTask3(){

        System.out.println("Doing Task3.1...");

    }

}

/** 
 * Other extension of the abstract workflow system 
 */
class WorkflowManager2Impl2 extends WorkflowManager2{

    @Override
    public void doTask2(){

        System.out.println("Doing Task2.2...");

    }

    @Override
    public void doTask3(){

        System.out.println("Doing Task3.2...");

    }

}
{% endhighlight %}

　　我们来看看工作流系统如何使用：

{% highlight java %}
public class TemplateMethodPattern {

    public static void main(String[] args) {

        initiateWorkFlow(new WorkflowManager2Impl1());

        initiateWorkFlow(new WorkflowManager2Impl2());

    }

    static void initiateWorkFlow(WorkflowManager2 workflowMgr){

        System.out.println("Starting the workflow ... the old way");

        workflowMgr.doTask1();

        workflowMgr.doTask2();

        workflowMgr.doTask3();

        workflowMgr.doTask4();

    }

}
{% endhighlight %}

　　输出如下所示：

{% highlight java %}
Starting the workflow ... the old way

Doing Task1...

Doing Task2.1...

Doing Task3.1...

Doing Task4...

Starting the workflow ... the old way

Doing Task1...

Doing Task2.2...

Doing Task3.2...

Doing Task4...
{% endhighlight %}

　　目前为止一切顺利。但是本篇博客的主要关注点不是模板方法模式，而是如何利用Java 8的Lambda表达式和默认方法实现模板方法模式。我之前已经说过，接口只有在只声明了一个抽象方法的前提下，才可以使用Lambda表达式。这个规则在本篇的例子中应这样解释：WorkflowManager2只能有一个抽象或者说自定义的任务。

　　如果你仍然对Java 8中的Lambda表达式和默认方法感到疑惑，可以在深入研究之前，花一点时间看一看[Lambda表达式](http://blog.sanaulla.info/2013/03/11/using-lambda-expression-to-sort-a-list-in-java-8-using-netbeans-lambda-support/)和[默认方法](http://blog.sanaulla.info/2013/03/20/introduction-to-default-methods-defender-methods-in-java-8/)这两篇文章。

　　我们可以利用带有默认方法的接口替代抽象类，所以我们的新工作流系统如下所示：

{% highlight java %}
interface WorkflowManager{

    public default void doTask1(){

        System.out.println("Doing Task1...");

    }

    public void doTask2();

    public default void doTask3(){

        System.out.println("Doing Task3...");

    }

    public default void doTask4(){

        System.out.println("Doing Task4...");

    }

}
{% endhighlight %}

　　现在我们的工作流系统带有一个可自定义的任务2，我们继续往下走，利用Lambda表达式处理初始化工作：

{% highlight java %}
public class TemplateMethodPatternLambda {

    public static void main(String[] args) {

       /**     
        * Using lambda expression to create different      
        * implementation of the abstract workflow 
        */
        initiateWorkFlow(()->System.out.println("Doing Task2.1..."));

        initiateWorkFlow(()->System.out.println("Doing Task2.2..."));

        initiateWorkFlow(()->System.out.println("Doing Task2.3..."));

    }

    static void initiateWorkFlow(WorkflowManager workflowMgr){

        System.out.println("Starting the workflow ...");

        workflowMgr.doTask1();

        workflowMgr.doTask2();

        workflowMgr.doTask3();

        workflowMgr.doTask4();

    }

}
{% endhighlight %}

　　这就是一个Lambda表达式应用在模板方法模式中的例子。
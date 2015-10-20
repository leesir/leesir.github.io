---
layout: post
title: "Java Functional Programming: 伪造闭包工厂，创建域对象"
description: ""
category: [译文]
tags: [Java Functional Programming]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

[原文链接](http://www.javacodegeeks.com/2012/03/java-faking-closure-with-factory-to.html) 作者: [Mark Needham](http://www.javacodegeeks.com/author/Mark-Needham/)

　　最近我们想构建一个需要使用外部依赖进行计算的域对象，同时我们希望在测试的时候能够忽略这些依赖。

　　最开始，我们简单地在域对象中创建依赖，这使得在测试的过程中，不能随意修改依赖的值。

　　同样，由于外部依赖仅仅只是域对象的计算所需，并非定义域对象的可变状态，我们不应该把依赖通过构造函数传入域对象内部。

　　最后，我们把域对象定义成内部类，代码如下：

    {% highlight java %} 
public class FooFactory {
    private final RandomService randomService;

    public FooFactory(RandomService randomService) {
        this.randomService = randomService;
    }

    public Foo createFoo(String bar, int baz) {
        return new Foo(bar, baz);
    }

    class Foo {
        private String bar;
        private int baz;

        public Foo(String bar, int baz) {
            this.bar = bar;
            this.baz = baz;
        }

        public int awesomeStuff() {
            int random = randomService.random(bar, baz);
            return random * 3;
        }
    }
}
    {% endhighlight %}
	
<!-- more -->

　　测试这段代码的测试用例如下：

    {% highlight java %} 
public class FooFactoryTest {

    @Test
    public void createsAFoo() {
        RandomService randomService = mock(RandomService.class);
        when(randomService.random("bar", 12)).thenReturn(13);
        FooFactory.Foo foo = new FooFactory(randomService).createFoo("bar", 12);
        assertThat(foo.awesomeStuff(), equalTo(39));
    }

}
    {% endhighlight %}
	
　　代码看似冗余，却合理地解决了测试与外部依赖的解耦问题。

#参考文献

*  [Java: Faking a closure with a factory to create a domain object](http://www.markhneedham.com/blog/2012/02/26/java-faking-a-closure-with-a-factory-to-create-a-domain-object/)
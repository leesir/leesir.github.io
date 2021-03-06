---
layout: post
title: "Java代码规范实践手册"
description: ""
category: [Code Review]
tags: [Code Convention]
---
<link rel="stylesheet" href="{{ site.baseurl }}/css/pygments.css">

　　舒适区最早是心理学的一个概念，它与人类的压力有着直接的关系。顾名思义，处于这一区域，你会感到非常舒服，觉察不到任何真正的压力，或者用自我麻痹来勉强应对它们。因此，我们既没有强烈的改变欲望，也不会主动地付出太多的努力，所有的行为，无非是为了保持舒适的感觉和假象而已。

——摘自《逃离舒适区》

　　程序员从学会第一行“hello world”开始，就逐渐培养起了自己的开发习惯。这些习惯或许来自于讲台上的讲师、搞笑娱乐平台百度知道、同性交友网github等。阅尽网络上难以保证质量的代码，培养了各种各样的毒瘤习惯，而我们却乐在其中。

　　是不是觉得Spark、Flume很高端？TB级数据碉堡了吧？把屈指可数的几个线程玩弄于股掌之中？

　　“Talk is cheap. Show me the code.”-- Linus Torvalds(http://coolshell.cn/articles/1278.html)

　　本人从业多年，却依旧写不好一行代码。决心踏出舒适区，从变量命名开始，重塑编码习惯。本文将从代码规范实践的3个等级(初学乍练，融会贯通，登峰造极)，指导开发者如何在项目中引入代码规范。
题外话：引入代码规范，是为了后续能够顺利进行Code Review。有很多优秀博文已经论述了code review的重要性，其中一篇：http://coolshell.cn/articles/11432.html 。

注：

1. 本文仅适用于使用intelliJ IDEA(14.1.x)开发并使用Maven执行构建的开发者。
2. 业内认可的规范包括Google规范和Sun规范，本次实践所使用的规范是Sun规范。
3. Checkstyle是本次实践的核心工具。本次实践将通过插件使用checkstyle。
4. 本次实践的代码规范检查仅用于基础检查。团队各异的规定，还需事先定制好。余额宝，是yuebao，还是yuEBao，还是balancePackage？
5. 之后附录里会加入checkstyle的检查内容（没那么快）。

<!-- more -->

# 初学乍练

　　在此阶段的实践中，基本上属于程序员的自娱自乐。开发者在自己的intelliJ上安装代码风格插件，用于检查编辑器内的Java代码。建议使用checkstyle插件，这是一款默认使用Sun规范检查代码的插件，可以实时检查当前源码文件，检查整个项目，并且支持更换规范规则。

　　集成步骤如下：

　　1. 打开settings，选择Plugins：

![java_code_convention_image_1]({{ site.baseurl }}/images/post_2015_11_21_image1.png)

　　2. 选择Browse repositories并输入checkstyle：

![java_code_convention_image_2]({{ site.baseurl }}/images/post_2015_11_21_image2.png)

　　然后选择右半窗口的Install plugin进行安装。因本人IDE已经安装，所以显示了Update plugin。

　　安装完成之后，IDE下方的工具栏里，会出现checkstyle的窗口：

![java_code_convention_image_3]({{ site.baseurl }}/images/post_2015_11_21_image3.png)

　　3. 点开checkstyle窗口，下拉选择规则：

![java_code_convention_image_4]({{ site.baseurl }}/images/post_2015_11_21_image4.png)

　　初次安装，只会有默认规则，即Sun规范规则。

　　4. 选中默认规则，点击左边绿色箭头，进行当前文件检查，结果如下：

![java_code_convention_image_5]({{ site.baseurl }}/images/post_2015_11_21_image5.png)

　　可以看到checkstyle窗口列出了错误的原因，比如Missing a Javadoc comment表示没有写注释。双击错误的原因，光标会跳到错误出现的代码行。

　　5. 当然还可以增加新的规范规则文件，打开settings里的other settings：

![java_code_convention_image_6]({{ site.baseurl }}/images/post_2015_11_21_image6.png)

　　可以看到默认规则选用的就是sun_checks.xml，表示Sun的规范文件。我们可以在https://github.com/leesir/checkstyle 下载到最新的Google规范和Sun规范，以及最新的checkstyle插件源代码。

　　当需要更换规则时，如上图，把规范加入到checkstyle中即可。此时checkstyle默认是不会勾选规则文件的，所以此时的插件不会进行实时检查。

　　6. 当勾选其中任何一个规则并确定之后，checkstyle的实时检查就生效了，效果如下：

![java_code_convention_image_7]({{ site.baseurl }}/images/post_2015_11_21_image7.png)

　　鼠标移动到红色字体之上，即可看到错误的提示，检查结果与手动点击绿色箭头进行检查的结果是一致的。

　　7. 我们可以针对此文件的规范错误进行修正：

![java_code_convention_image_8]({{ site.baseurl }}/images/post_2015_11_21_image8.png)

　　第一个：Missing package-info.java file. (0:0)。(0:0)不是在卖萌，表示的是第0行第0个字符出现了错误。意思是缺少包信息描述类。

　　8. 那我们就在该类所在的包下新建一个package-info.java：

![java_code_convention_image_9]({{ site.baseurl }}/images/post_2015_11_21_image9.png)

　　编写好注释：

![java_code_convention_image_10]({{ site.baseurl }}/images/post_2015_11_21_image10.png)

　　9. 此时切换到Main.java，可以看到package处的错误已经消失，说明我们已经修正了该处的错误：

![java_code_convention_image_11]({{ site.baseurl }}/images/post_2015_11_21_image11.png)

　　10. 接着一鼓作气，改掉其他的错误，源代码效果如下：

![java_code_convention_image_12]({{ site.baseurl }}/images/post_2015_11_21_image12.png)

　　可以看到该文件已经没有任何规范错误，至此，我们完成了第一个文件的代码规范修复(请做好准备，随便一个项目出现的规范错误数都会在2000以上，上不封顶)。修复后的Main.java是不是更加professional了?

　　第一个等级的图文教程已经结束，文章末尾将会附上定制过的Sun规范文件。不使用默认规范的原因是检查太过于严厉，以至于Java源代码都不如其法眼。可以逐步开放规范中的限制，循序渐进地向标准靠拢。不建议初次引入规范，就要达到标准的程度，程序员心理压力太大，效果会适得其反。

　　初学乍练等级的规范检查，由于检查的地点是在个人电脑上，所以当然也有人不安装检查插件，或者不愿意修改规范错误，从而导致源代码库中依然存有不符合规范的代码。

# 融会贯通

　　全世界的程序员中，自由开发者占少数。所以自娱自乐的代码规范实践，并不能满足身处团队需要协作的程序员们。所以，本小节将会把上一小节的代码规范实践，通过引入Maven扩展插件，推广到项目组中。

　　将最新的Maven checkstyle插件加入到pom文件中：

![java_code_convention_image_13]({{ site.baseurl }}/images/post_2015_11_21_image13.png)

　　更新maven，会自动下载该maven插件。使用mvn checkstyle:checkstyle或者mvn validate(可配置成package或者install等)均可在编译时进行规范检查。效果如下：

![java_code_convention_image_14]({{ site.baseurl }}/images/post_2015_11_21_image14.png)

　　可以看到控制台中输出了很多规范错误，内容提示同上一小节。
　　请注意红色框框内的内容，大致意思是maven编译的结果并不会因为源代码中存在规范错误而失败。在团队协作中，还是建议把maven编译的结果同规范检查的结果关联起来，这样就能保证源代码库中的代码是符合规范的。可以修改以下配置达到这一目标：

![java_code_convention_image_15]({{ site.baseurl }}/images/post_2015_11_21_image15.png)

　　可以在Maven生成的targer文件夹下，查看规范检查的结果以及所使用的规范文件：

![java_code_convention_image_16]({{ site.baseurl }}/images/post_2015_11_21_image16.png)

　　打开checkstyle-result.xml，可以看到每个文件的错误以及提示：

![java_code_convention_image_17]({{ site.baseurl }}/images/post_2015_11_21_image17.png)

　　至此，我们通过强制在编译过程中加入了规范检查，达到了在项目中引入代码规范的目的。

　　请注意，Maven的checkstyle插件和intelliJ的checkstyle插件没有半毛钱关系。intelliJ的插件在代码未完成时，就已经开始工作，而Maven的插件在代码完成之后的编译过程中才开始工作。单独使用其中某一个，都可以到达这一等级的代码规范。

　　比如使用intelliJ插件，程序员需保证每个人安装的插件版本一致，并且每个人都需要开启实时检查功能，才能够让整个团队的规范保持一致。然而并不是每个人都喜欢在写代码时看到一大堆鲜艳的红色错误，某些人肯定会在编写代码时关闭实时检查功能，可以肯定的是，除非团队已经非常默契，否则源代码库中一定会包含不符合规范的代码。

　　使用Maven插件，产生的编译错误信息通常会更加令人紧张，也会更加谨慎。并且源代码版本库会记录pom.xml，加入到团队中的任何一个人都会被强制性地进行编译时的规范校验，所有人的行为都将统一，除非有人蓄意破坏团队规则(如编译时注释掉插件)。相对而言，使用Maven插件更能保证团队代码规范不被破坏。

　　而intelliJ + Maven插件的混合使用，使得每个人既在编码时纠正规范错误，同时在编译时进一步校验源代码中是否有不符合规范的地方。

　　注：因Maven插件更新较慢，最新版本对应的checkstyle版本为6.2，而最新的intelliJ插件对应的checkstyle版本为6.11。版本不一致，会导致双方的检查结果不一致。为了配合Maven插件，我们必须使用checkstyle版本号在6.2左右的intelliJ插件。在本文末尾将会附上使用6.5版本checkstyle的intelliJ插件。只需将插件压缩包解压，把内部的CheckStyle-IDEA文件夹拷贝到intelliJ IDEA安装目录下的plugins里，重启后即可生效。经测试，6.2与6.5版本的checkstyle检查的结果一致。

# 登峰造极

　　除了上述两种集成checkstyle的方法以外，还可以在SVN、Git等代码管理工具里加入checkstyle插件。只要某次提交中包含了不符合规范的代码，将会被拒绝写入到版本库中。对于实施了持续集成的团队，也可以在每次构建中，首先进行源代码规范检查，一旦发现错误，将停止构建，然后群发通报邮件，并且停止构建的部分损失由责任人来承担。通过在代码编写过程中的各个时期，进行各种检查，我有一百种方法让开发者不敢随意提交代码。这种方式几乎可以保证代码库中不存在不规范的代码，同时也是最难以实施以及最残忍的一种实践方式。由于时间关系，没能做到这一等级的实践，其实心里也有些许害怕，担心推广到团队之后会被同事殴打。

# 总结

　　不管何种方式，核心思想都是通过checkstyle引入代码规范。将checkstyle作用于代码上线前的不同时期，将会得到不同的结果，所以也就有了上述的等级一说。当然，如果团队中每一个人不依靠checkstyle就能写出符合最高标准的代码，这便是终极等级——得道升仙。

# 成果

　　checkstyle代码规范检查在天天基金服务中正式启用。当装上intelliJ IDEA插件的那一刻，我的天空都灰暗了。面对着已经细到点不动的结果页面的滚动条，一片通红的编辑器，我艰难地做出了降低标准的决定。

　　经过一个多星期的修复，天天基金中某一个服务(并非全部)的代码终于纠正完毕。然而结果却不那么值得称赞，因为存在不少“为了写注释而写注释”的行为。编写注释对于大多数程序员而言，难度比编写代码高得多。集中几句话，把代码意图表达清楚，是十分考验表达能力的。不管如何，此次实践已经是一大突破，接下来将展示引入代码规范的部分成果。

![java_code_convention_image_18]({{ site.baseurl }}/images/post_2015_11_21_image18.png)

　　生成标准Javadoc之后，package-info.java发挥了大作用。在Javadoc首页，可以看到每个包的描述。点进aop包，可以看到aop下所有类的描述：

![java_code_convention_image_19]({{ site.baseurl }}/images/post_2015_11_21_image19.png)

　　点击某个类，能看到每个方法的主要作用：

![java_code_convention_image_20]({{ site.baseurl }}/images/post_2015_11_21_image20.png)

　　再来看看代码：

![java_code_convention_image_21]({{ site.baseurl }}/images/post_2015_11_21_image21.png)

　　是不是有点赏心悦目呢？篇幅有限，很遗憾并不能完整展示整个项目。

　　对于新进团队的员工，可以通过阅读Javadoc文档快速了解一个项目的组织架构，阅读带有详细注释的方法了解代码的意图。除此之外，符合团队规范的代码才建议组织code review，因为大家聚在一起，不是为了看你的常量命名是否规范、检查哪个方法是否编写注释这种低级问题，不要让奇怪的代码风格降低code review的效率。

　　在此感谢组内小伙伴无偿的支持。规范化是一条很长的路，一个团队的就算达到了得道升仙的水平，也依然有继续奋斗的目标——制定标准。

　　感谢阅读。
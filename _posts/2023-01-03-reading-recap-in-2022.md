---
layout: post
title: 2022 年读书小结
subtitle: 
date:       2023-01-03
author:     "Scott"
header-img: "img/coding.jpg"
catalog:    true
tags:
  - coding
  - book
---

# 2022 年读书总结

今天读了不少技术图书，今年编程功力之所以能质变，离不开他们，当然，更重要的是很幸运加入了 ops 这个团队，让我能在实践中对很多理论和原则进行融汇贯通。


如一个朋友所言，有些书读完就忘了，而有些书注定是 life-change 级别——神作。

* 书 1：Unit Testing Principles, Practices, and Patterns https://www.manning.com/books/unit-testing by **Vladimir Khorikov**(二刷神作)

这本书不仅告诉你什么是好的单元测试：能发现 bug；不乱喊狼来了；反馈够快；好读且好写。而且通过一种渐进式的方式引导你写出这样的测试。当然，全书最精彩的部分是点明了软件难以维护，难以测试的根源——高复杂和多依赖并存于一处，且提出了切实可行的解决之道。


* 书 2：Learning Domain-Driven Design: Aligning Software Architecture and Business Strategy by **Vlad Khononov**(神作)
https://www.amazon.com/Learning-Domain-Driven-Design-Aligning-Architecture/dp/1098100131

这是一本关于 DDD 讲解的最为深入浅出的书籍，有别于很多领域驱动设计的书通常从各种词汇定义开始，这本书是从战略的角度开始，这一点是很多程序员忽略，但这其实是极重要的。

如书里所言： 

> 软件开发是很难得，每周学习一个新的 Js 框架并不是我们工作中最困难部分，对新的业务领域的理解可能更具挑战性。

作为一个工程师，你不仅要努力工作，更重要的是知道在正确的方向上努力。那什么方向才是正确的呢？这本书给出了一些识别的方式和技巧，核心域/支持域和通用子域，他们的识别方式和应对方式都是不一样的。

* 书 3: Grokking Simplicity: Taming complex software with functional thinking y by **Normand, Eric** (杰作+)
https://www.amazon.com/Grokking-Simplicity-software-functional-thinking/dp/1617296201

这本书最大的特点在于对于 `data`, `computation`, `action` 三者的区分，而且很多 action 是可以拆分成 data, computation 以及更小的 action，另外 action 是会污染 computation 的，这也是我们为什么要尽可能的把 action 推向边缘的原因。

所以这本书更多的是从函数式架构的角度谈论编程，如果你一定要挑一本书入门函数式，那一定是这本，因为这可以让你在任何语言都使用这些技巧，从而写出更易维护，更易测试的代码。而很多人只懂函数式技巧，而忽略架构，哪怕使用纯函数语言写出的代码依然是可组合性差，可维护性差。

另外本书的后半部分对于如何管理并发时间线也提供了很简单很可行的方法：先画时间线图，然后尽可能减少时间线，缩短时间线，不做时间线之间的资源共享等等。

* 书 4: enricopolanski/functional-programming: Introduction to Functional Programming using TypeScript and fp-ts by **Giulio Canti**(神作)
https://github.com/enricopolanski/functional-programming

这是 fp-ts 作者 Giulio Canti 意大利文写的「函数式简介」这本书书的英文翻译，作者首先点出了函数式编程的两大支柱：参照透明(Referential transparency)和组合(composition)，然后围绕着组合，对各种范畴进行建模，从岩浆到半群再到幺半群， 再从范畴论的函子到应用函子再到单子，你会发现这些概念离我们日常工作越来越近，而且你最终会知道为什么单子(monad)那么重要，因为这是这是保持「一致性」去解决副作用问题的关键。

本书可以搭配 Scott Wlaschin  的几个视频一起观看，Giulio Canti  是从范畴论的角度谈组合，而 Scott 则是用 human language 讲组合。

* The Power of Composition - Scott Wlaschin - NDC Oslo 2020
https://www.youtube.com/watch?v=rCKPgu4DvcE&ab_channel=NDCConferences
* The Functional Toolkit - Scott Wlaschin - NDC 2019
https://www.youtube.com/watch?v=bK-Tz-GLfOs&ab_channel=NDCConferences

* 书 5：A Skeptic’s Guide to Functional Programming with JavaScript
https://jrsinclair.com/skeptics-guide by **James Sinclair** (可用之书+)

这是一本很有意思的 Javascript 函数式编程的书， 如果你想在 ts 或 js 中使用函数式，但又担心同事反对，里面对大部分情况做了预判，比如为什么有些人写的函数式代码很难懂呢？

* 书 6：Grokking Functional Programming by **Michał Płachta** (杰作-)
https://www.manning.com/books/grokking-functional-programming

这是一本极为详尽的函数式书籍，作为一个写了几年函数式代码的人，我甚至觉得他详尽到了啰嗦的程度，不过第 7 章 requirements as types 和 第 8 章的 IO as values 还是很有价值的，尤其是作者先用命令式做示范，然后说明这样做的问题，再用函数式一步步解决这些问题。

* 书 7：Good Code, Bad Code: Think like a software engineer:  by **Long, Tom**(杰作)
https://www.manning.com/books/good-code-bad-code

好代码有标准么？本书的回答的是可以有，好的代码应该是易读的，不被被误用的，模块化的，可重用和可泛化的以及可测试的。

为什么这些标准(作者称之为支柱)很重要？因为这有利于我们达成软件开发的真正目标：软件是可用的(满足需求)，软件可以一直工作(易改且不容易破坏原有功能)，不用重新发明轮子。

针对这些目标，这本书提供了适用于许多适应于日常开发的参考。

* 书 8： Software Design: Tidy First?  By  **Kent Beck** (杰作+)
https://tidyfirst.substack.com/Kent Beck 时隔14 年之后重新写书了，作者是个很有智慧的人，对于设计的很多问题都有着不同反响的洞察。

比如说：

> 软件的成本主要是变更它的成本。

而变更成本包括：

> 1. 理解当前行为
> 2. 修改行为不会打破任何东西
> 3. 验证修改在生产环境中也会按预期工作
> 4. 部署新版本

对于软件设计是什么？他也有独到的见解：

> 软件设计是一种人际关系的练习。

这本书目前是订阅制的，不过前半部分基本完成了，里面对于识别什么进行整理，以及怎么整理都有着切实可行的建议。

* 书 9：The Art of Readable Code  by **Dustin Boswell, Trevor Foucher** (杰作)
https://www.oreilly.com/library/view/the-art-of/9781449318482/?_gl=1*im6fnz*_ga*MTQ5MTEwOTQ5OC4xNjY3NzE4NzM3*_ga_092EL089CH*MTY3MjYzMDc2OC4zLjAuMTY3MjYzMDc3My41NS4wLjA

机器需要可读代码么？不，机器不需要。只有人需要，人为什么需要？因为人的工作记忆很有限。最新的认知科学研发发现，人脑的工作记忆容量仅为 4。所以写可读代码主要目的是为了减轻认知负荷。

本书是从命名到注释，再到块教你一步步的写出更符合人类认知的代码。

* 书 10：Design and Reality by **Rebecca Wirfs-Brock and Mathias Verraes**  (杰作)

https://leanpub.com/design-and-reality

作者其中之一是面向对象设计的大牛，Rebecca Wirfs-Brock 是资深的设计顾问，今年已经 70 岁了，而另一位则是长期活跃在 DDD 社区的设计顾问。

这本书首先说明了一个在领域建模中的一个谬论：可以通过发现领域中的所有相关概念，然后做一些简单的转化，添加一些简单的行为，这样就把问题解决了。然后事实远远没有那么简单，而且这种误解和简化会导致很多问题，比如说会让人误以为从现实(问题空间)到模型(解决方案空间)是单向的，另外，也会让人觉得领域语言和无处不在的语言是同一种事物。

书里沿着这个方向，探讨了好几个精彩的例子，我最喜欢的无疑是 [Drilling Mud](https://verraes.net/2021/09/design-and-reality/) 这个例子，Rebecca 通过 Alert conditions 这个模型代替旧模型 Alarms 的做法精彩绝伦，因为新模型在现实世界中并没有以领域语言存在过，但却能很好的解决现实问题——这个模型将紧急情况与警报的响声解耦，而且它开启很多全新的可能，这足以说明好的设计是可以改变现实的，而不仅仅是一种对现实的映射。

> “Our measure of success lies in how clearly we invent a software reality that satisfies our application’s requirements—and not in how closely it resembles the real world.” —  [Object Design](https://www.informit.com/promotions/object-design-142314) , Rebecca Wirfs-Brock

书的第二章则是提到了隐喻的重要性，如果你的软件很复杂，很难以维护，很有可能开发人员之间，以及与业务专家之间都没有很好的沟通，换句话说如果大家沟通很困难，那么肯定是缺少一个好的共享心智模型。他们通过坐在一起，用白板建模，越来越多词汇跃然板上，再经提问，搞清楚这些词是什么意思，在什么场景使用？有一些词汇的概念变的越来越重要。最终他们发现了新的隐喻——Trust。这个更好的隐喻带来很多好处，比如能让双方都能参与讨论而不至于迷失在细节之中，而且能在降低认知负荷的情况下去处理复杂情况，还能有更清晰的上下文。

后面几章也很有意思，比如关于模型的评估等等。


* 书 11： Designing Elixir Systems with OTP: Write Highly Scalable, Self-Healing Software with Layers by James Edward Gray, II and Bruce A. Tate
https://pragprog.com/titles/jgotp/designing-elixir-systems-with-otp/
*  书 12：Programming Phoenix LiveView: Interactive Elixir Web Programming Without Writing Any JavaScript by Bruce A. Tate and Sophie DeBenedetto
https://pragprog.com/titles/liveview/programming-phoenix-liveview/
*  书 13：Programmer Passport: LiveView by Bruce Tate
https://pragprog.com/titles/passlive/programmer-passport-liveview/
*  书 14：Design for Ecto
https://grox.io/language/ecto/course
*  书 15：Programmer Passport: OTP by Bruce Tate
https://pragprog.com/titles/passotp/programmer-passport-otp/

这几本都是 Bruce Tate 的书，而且核心内容其实说的是类似的事，所以放到一起总结吧。

Bruce Tate 最大的贡献在于他用 CRC pattern(constructor, reducer, converter) 把 Elixir 的 LiveView, Ecto, genserver 以及functional core 等等贯穿起来了，并且在他的各本书中提出了具体的例子(虽然很多例子都太简单)。但这种模式的最大好处在于让人能快速入手 Elixir，同时对于模块的内聚程度能做很好的识别。

另外他对于如何分层，目录划分，关注点分离也有很多独到见解，比如在 Ecto 的书中，我第一次看到了原来可以把 query 和 schema 都当做 Data，而对他们的具体组合则是归为 context ，这样做的最大好处在于 context 变得更薄，更清晰，只关心做什么，而不关心具体怎么做。

还有关于怎么在 Elixir 中实践 [Functional Core，Imperative shell](https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell)， 他也给出了方向和例子。遗憾的是他并没有说中间状态的问题，这一点上，可以参考 unit testing 的 humble object pattern，有更深入的探讨。


* 书 16：风格感觉(杰作+)
https://book.douban.com/subject/30186025/

这是一本关于写作的书，但我觉得任何程序员都值得一看，因为大部分情况下，开发都不是单人作战，而怎么写出简洁，清晰的文档是一项基本功。


### 小结

以上就是 2022 年读过的且记忆深刻的所有书。 最后，我想推荐一个今年对我影响很深的一系列 screencast： https://www.destroyallsoftware.com/screencasts/catalog

尤其是这几个：

* Functional Core, Imperative Shell
https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell (免费)
* Separating Arrangement and Work
https://www.destroyallsoftware.com/screencasts/catalog/separating-arrangement-and-work
* Test Isolation Without Mocks
https://www.destroyallsoftware.com/screencasts/catalog/test-isolation-without-mocks

看这个系列，你可以看到一个卓越的程序员是怎么优雅的解决日常编码问题。而这个系列的大部分视频都是作者 10 几年前录的，但一点都不过时，让人不由感叹：软件开发中的困难点真是几十年都没有怎么变过。






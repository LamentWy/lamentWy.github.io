---
layout: post
current: post
title: JVM Overview
navigation: True
description: 序章
tags: [Scroll]
class: post-template
subclass: 'post tag-scroll'
---

前一阵微信提醒我不要浪费订阅号资源。

于是就顺手画了一张图回忆JVM，顺便借着这个机会把JVM的知识点系统化一下。

因为当时先发的微信订阅号，所以这里的内容会跟订阅号略有不同。

![https://lament-z.com/assets/images/JVM/JVM-Overview.jpg](JVM Overview)

*** 绿色部分：共享内存区域 ***

## 用法  

我特别不擅长背东西，通常需要借助一些手段来帮助记忆。

对于JVM这种，我用的是看图说话+推理式记忆法，核心思路就是按照逻辑去记忆和推理细节。  

当JVM的模型结构还不熟悉时，可以一边口述逻辑关系，一边画出这个图。

当可以熟练的画出该图后，可以尝试分模块口述每个模块的功能，甚至每个模块内部的细分结构。

每一层只回忆当前层的知识，深入讨论放在合适的层面，比如overview时不去回忆什么方法区，只想RuntimeDataArea。

这样可以在脑内建立一个符合JVM模型/设计逻辑的知识体系，相当于一个索引一样。

当工作/面试时遇到某些零散知识点的时候，研究明白之后就可以相对精准的补充到合适的维度。

不至于重演之前用完就忘了的尴尬。

## 一个面试题告诉你什么叫鱼的记忆

这简直就是我鱼的记忆一个典型例子。

我学习JVM最初主要通过《深入理解JAVA虚拟机》这本书，翻来覆去的看了好多遍，然而昨天朋友发来一道从该书提取出的面试题大致如下：

```

        String str1 = new StringBuilder("lament").append("z").toString();
        System.out.println(str1);
        System.out.println(str1.intern());
        System.out.println(str1 == str1.intern());


        System.out.println();

        String str2 = new StringBuilder("ja").append("va").toString();
        System.out.println(str2);
        System.out.println(str2.intern());
        System.out.println(str2 == str2.intern());


```

我对此完全没有印象，打开书特意找了一下才发现确实是介绍方法区那一节的一个例子。

这个例子本身非常有趣，但是其实作为面试题（尤其是问为什么最后一行的输出是false）来考察是不是看过这本书，对我这种鱼的记忆简直太过分了。

需要注意的是这个例子是与环境强相关的，如果你想自己重现false的情况，可以使用 *** Oracle JDK7u / OpenJDK7u HotSpot VM *** 。

不然你很可能看到两个true XD。

当然如果你看到这里突然对“字符串常量池”和“String#intern”产生兴趣了，可以看看美团的这一篇[解析](https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html).

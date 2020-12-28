---
layout: post
current: post
title: JVM-RuntimeDataArea
navigation: True
description: 二
tags: [Scroll]
class: post-template
subclass: 'post tag-scroll'
---

# 运行时数据区 Runtime Data Areas  

## 概述

  《Java虚拟机规范》定义了如下几个运行时数据区：

  ![RT Data Areas Overview](https://lament-z.com/assets/images/JVM/runtime-data-area-v1.jpg)

  其中一些区域随着线程的启动和结束而创建和销毁，另一部分则随着JVM的启动而一直存在到JVM关闭。

## 使用方法  

  一边画图一边看，画一块看一块。
  过完一遍之后，擦了重来，哪里忘了看哪里。

## 详解

  下图可以更直观的区分不同数据区域:

  ![RT Data Areas with Thread](https://lament-z.com/assets/images/JVM/runtime-data-area-v2.jpg)

  从第二张图可以清晰的看出，虚拟机栈(JVM Stack)、本地方法栈(Native Stack)、以及程序计数器(The Program Counter Register)与线程共生共灭。

  而堆（Heap）和方法区（Method Area 也被称作非堆）则被所以线程共享。

  接下来逐个介绍每个区域的功能，按照是否线程隔离，先从与线程共生共灭的几种说起。

### 程序计数器 (The Program Counter Register)

  程序计数器，之后简称为PC Register，简单的说就是记录当前线程执行的字节码所在行号的一小块内存，如果线程执行的是本地方法(Native Method)则记录undefined，其生命周期与线程相同。

  JVM的多线程是“分片”式实现：在任意确定时刻，一个处理器(多核CPU则是其中一个核)只都只执行一个线程中的指令（看作一个片）。并发只是不断在各个分片上切换的"假象"。而每个线程的PC Register就是用来保证线程切换后可以正确恢复的关键。

  PC Register的工作就是通过改变PC Register存储的值来选取需要执行的下一条字节码指令。
  程序控制流，如分支、循环、跳转、异常处理、线程恢复等等都依赖这个指示器。

#### 内存

  其内存大小足够持有一个"return Address"或者是一个指定系统的Native pointer，具体到不同版本JVM的实现里是多大没关心过。

#### 异常  

  唯一一个“规范”中没有定义内存异常的区域。

### 虚拟机栈 (JVM Stack)

***
  这部分摘自《深入理解JAVA虚拟机(第三版)》。
  虚拟机栈(VM Stack)描述的是Java方法执行的线程内存模型:

  每个方法被执行的时候，Java虚拟机都会同步创建一个帧(StackFrame)用于存储局部变量表(Local Variable Array)、操作数栈(Operand Stack)、动态连接、方法出口等信息。

  每一个方法被调用直至执行完毕的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。
***

  啥意思呢，先看下图，主要是右半部分，懒得单独做一张 VM Stack 的图了。

  ![RT Data Areas with vm stack details](https://lament-z.com/assets/images/JVM/RuntimeDataArea-details.jpg)

  每当一个新线程启动的时候，JVM就会给这个线程创建一个 VM Stack，而VM Stack把线程的状态存储在栈帧(Stack Frame)中，JVM对 VM Stack 的直接操作就俩：入栈，出栈（Because the Java Virtual Machine stack is never manipulated directly except to push and pop frames, frames may be heap allocated.）。

  无论是我们写的```System.out.println("Hello World");``` 还是别的什么方法，都是基于这个模型完成的。

  ”Stack Frame“ ，包括“JVM执行方法时都发生了啥”这里暂时不展开，回头单独开一篇顺便打包上“递归”一起说。

  万一我鸽太久，也可以先看看《Inside the Java Virtual Machine》中的[The Java Stack](https://www.artima.com/insidejvm/ed2/jvm8.html)这一节，讲述的非常清晰。

#### 内存

  该区域中内存无需连续。

#### 异常  

  1. 当线程申请 VM Stack 的深度超过的规定时，抛 ***StackOverflowError***。

  2. 当 VM Stack 为可拓展型时，线程需要动态扩展自己的 VM Stack，但空余内存不足时；或者当空余内存不足以初始化线程的 VM Stack 时，抛 ***OutOfMemoryError***。

### 本地方法栈 (Native Stack)

  跟VM Stack作用差不多，只不过是调用Native方法。
  异常情况也是类似。


***
  说完了线程隔离的区域，来看看共享的区域：Heap & Method Area。
***
### 堆 (Heap)
***
The heap is the run-time data area from which memory for all class instances and arrays is allocated.   -- From JVMS.
***

  所有的类的实例，数组啥的都在堆(Heap)上分配。
  提到Heap就免不了说一嘴分代，什么新生代老年代永久代，包括元空间blabla，这些是基于“分代收集理论”or某些JVM具体实现引出的说法，Heap在本篇仅限于“JVMS中规定的Heap”这么一个抽象模型。

#### 内存  

  Heap可以是固定大小，也可以是可扩展式的。
  通常遇到的都是可扩展的，通过-Xmx(maximum heap size ) -Xms(minimum heap size，也称为初始 Heap size)来设定其大小。

  关于某些特定的JVM的这类参数啦、这类参数选项都有啥啦、它们的含义啥的可以参考Oracle的[Tuning Java Virtual Machines](https://docs.oracle.com/cd/E21764_01/web.1111/e13814/jvm_tuning.htm#PERFM150)文档。

  关于TLAB(Thread Local Allocation Buffer):

  《深入理解JAVA虚拟机(第三版)》里面提到了，为了提升对象分配时效率，Heap中会划分出多个被线程私有的分配缓冲区，这个缓冲区就是TLAB，里面存的依然是对象的实例或Array。说白了就是从大Heap里分几个小Heap给Thread们拿去用，只是优化的手段，Heap还是Heap。


#### 异常

1. 如果Heap中剩余内存不够分配给实例or数组，并且也没更多的内存让Heap完成扩展时，抛***OutOfMemoryError***。

### 方法区 (Method Area)

  方法区 (Method Area，很多资料也用非堆来将它与堆进行区分)用来存储被JVM加载的：

  1. 类型信息 TypeInformation   

  2. 常量池 The Constant Pool

  3. 域信息 Field Information

  4. 方法信息 Method Information

  5. 类变量 Class Variables   

  鉴于我接触的很多人都不这么叫，很多书上也不这么写，稍微解释下啥是类变量。
  举个例子，比如```Integer.MAX_VALUE```中的```MAX_VALUE```这种作为类属性之一，并且被```static```修饰(是不是public无所谓)，当然还自带初始值的，就是类变量。

  6. 各种引用

  具体细节单独展开
  这里的部分翻译参考了[Java 中 field 和 variable 区别及相关术语解释](https://www.ituring.com.cn/article/491755).

#### 内存  

  规范中对该区域规定也比较宽松，不需要内存连续，可以是固定大小的也可以是可扩展的。

  还有之前提到的“分代理论”，以前一些JVM按照他们自己的分代设计，用永久代实现了方法区。
  叫永久可能是这部分存贮的都是些比较"固定"的东西，但是不代表***永久代 == 方法区***，也不代表该区域就绝对不进行内存回收。

#### 异常  

1. 无法满足新的内存分配需求时,抛***OutOfMemoryError***。  

### 运行时常量区 (Run-Time Constant Pool)

  它是方法区的一部分，规范规定当JVM创建一个类或者接口时，就为该类或对象构建一个运行时常量池。

  写了半天都不太满意，要么不够清楚，要么不够简洁，先挂着[别人的吧](https://blog.jamesdbloom.com/JVMInternals.html#constant_pool)。

#### 内存

  它的内存是从方法区的内存里分配的，受到方法区本身内存大小的限制。

#### 异常  

1. 当创建类(Class)或者接口(Interface)时，运行时常量池需要的内存大于方法区剩余的空余内存,抛***OutOfMemoryError***。  

### 补充说明

#### Heap 和 Method Area 的逻辑关系。

  可以看看[The Heap](https://www.artima.com/insidejvm/ed2/jvm6.html)下面画的几种关系图。
  我就先不画了，偷个懒，后面单独写Heap和MethodArea的时候再说。

#### 直接内存 (Direct Memory)

  该内存区域不属于运行时数据区(Runtime Data Areas)，也不在《Java虚拟机规范》的定义中。
  比如开发者可以通过JNI或者NIO的ByteBuffer来调用malloc.

#### NIO

***
  这部分摘自《深入理解JAVA虚拟机(第三版)》。

  NIO(New Input/Output)类，引入了一种基于通道(Channel)与缓冲区 (Buffer)的I/O方式。

  它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆里面的DirectByteBuffer对象作为这块内存的引用进行操作。

  这么做避免了在Java堆和Native堆中来回复制数据，能在一些场景中显著提高性能。

***


## References

1. [Java Virtual Machine (JVM) Stack Area](https://www.geeksforgeeks.org/java-virtual-machine-jvm-stack-area/)  

2. [JVMS-Run-Time Data Areas](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5)

3. [深入理解JAVA虚拟机(第三版)](https://book.douban.com/subject/34907497/)

4. [Native Memory Tracking in JVM](https://www.baeldung.com/native-memory-tracking-in-jvm)

5. [Inside the Java Virtual Machine](https://www.artima.com/insidejvm/ed2/index.html)

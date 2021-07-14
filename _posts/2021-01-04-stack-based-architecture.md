---
layout: post
current: post
title: Stack Frame - 草稿
navigation: True
tags: [Scroll]
class: post-template
subclass: 'post tag-scroll'
---

> The JVM uses a stack based architecture.


## 数据结构里的栈(stack)

先抛开语言环境啥的，单聊一下数据结构中的栈是个啥(stack)。  

Stack 的结构和性质跟网球罐子差不多。

![tennis](https://lament-z.com/assets/images/tennis.png)

每次我们想取出一个球只能从最上面拿，想拿最下面的球就必须先把上面的先拿完。

往里装球的时候也是，最先放进去的在最下面，最后放进去的在最上面。

对应到数据结构中这就是 Stack。

网球罐子开口的那一端，对应 Stack 的 top。

向 Stack 中添加元素（也就是往里塞球），叫做入栈，push。

从 Stack 中取出元素(往外拿球)，叫出栈，pop。

LIFO，Last in first out。

## 栈帧 Stack Frame

JVM最基本的执行单元为方法。

而 Stack Frame 是支持方法调用和方法执行背后的数据结构。

### 组成  

![Stack Frame](https://lament-z.com/assets/images/JVM/Stack Frame Diagram.jpg)

Stack Frame中包含 局部变量表，操作数栈，以及其他一些栈帧数据。

#### 局部变量表 local variables Table

TODO  复用槽啥的也想略。

+ 是什么

局部变量表(Local Variables Table)是一组变量值的存储空间，用于存放方法参数和方法内部定义 的局部变量。在Java程序被编译为Class文件时，就在方法的Code属性的max_locals数据项中确定了该方 法所需分配的局部变量表的最大容量。

所谓的一组变量值具体是指：方法参数，方法内部定义的那些个局部变量。

局部变量表的最小组成部分叫做变量槽（Variable Slot）。

它的大小要能存放一个X类型的数据，这个X指的是：6种基础元素类型的JVM版（除去double long）+对象实例的引用+returnAddress（比较少见）。

对象实例的引用类型的长度，跟虚拟机是32位/64位相关，如果是64位还跟有没有开启某些对象指针压缩的优化有关。

除了八种大小不会超过32位的数据类型之外，还有double和long这俩64位的。
JVM存储64位数据类型的方式是，通过高位对其的方式将其分配到俩连续的槽中。

32位的数据类型：

+ 怎么用

JVM通过索引定位的方式使用局部变量表。

索引范围：[0，slotMax]。

访问32位数据时，索引n就代表第n个槽。
访问64位时，索引n则是n和n+1俩槽，并且不允许单独访问其中一个。

当方法被调用时，JVM会使用 局部变量表 来完成参数值到参数列表的传递过程。

即实参到型参的传递。

如果执行的是实例方法（非static），局部变量表的0槽里默认存放 用于传递方法所属对象实例的引用。（方法里面写的this就是它）。
其余的参数则按照参数表顺序排列，从槽1开始依次占坑。

参数表分配完之后，再根据方法内部定义的变量顺序和作用域分配其余的槽。

+ 复用槽  

为了尽可能节省栈帧耗用的内存空间，局部变量表中的变量槽是可以重用的。

方法中的变量的作用域不一定覆盖整个方法体，当pc计数器的值超过了某个变量的作用域范围时，这个槽就可以重用。

次重用的设计虽然节省了栈空间，但是偶尔会影响到GC。

影响主要产生于重用这里，重用并不是超出作用域范围之后立刻清空，而是等到需要的时候才会清。
在槽被清之前，这个变量虽然已经超出作用域范围了，但是引用还在，gc的时候由于仍然保持着引用，就依然属于可达。

### 操作数栈/操作栈 Operand Stack

也是个栈，也有槽，槽的大小跟前面一致，栈的最大深度是编译器在做数据流分析的时候保证不超过max_stacks的最大值。


#### 运行流程

  一个方法刚开始执行时，OperandStack是空的，方法执行的过程中会有各种字节码指令对OperandStack进行入栈、出栈操作。

  比如 1+2 ,就是先把俩数入栈，然后字节码指令iadd把这俩数出栈，然后相加，最后把相加的结果再入栈。

  抽象模型中 两个StackFrame是相互独立的，但是虚拟机的实现里通常会进行优化处理，通过将不同的栈帧部分重叠，让下面栈帧的部分操作数栈和上面栈帧的部分比局部变量表重叠。

  这样即可以节约空间，还可以在方法调用时直接公用一部分数据，避免进行额外的参数复制传递。

### Frame data: Reference to Runtime Constant Pool

每个栈帧都包含一个指向运行时常量池中该栈帧所属方法的引用，持有这个引用是为了支持方法调用过程中的**动态连接**(Dynamic Linking)。



类加载的时候提到过**解析阶段**会将 符号引用 转化为 直接引用，这种**叫静态解析**。
还要一部分 符号引用 在是在运行时转化为 直接引用，这个就是动态链接Dynamic Linking。

### Frame data: returnAddress

TODO returnAddress想略过不表。

这个东西属于上古遗留产物，是一种特殊的类型，给Java虚拟机的jsr，ret和jsr_w指令（§jsr，§ret，§jsr_w）用的，主要是处理异常。
现在异常处理都是用异常表。​
一个方法开始执行后，有两种退出方式：  

+ 一种是正常退出 Normal Method Invocation Completion

  就正常执行完，要么结束（void），要么返回个返回值给它的上一层调用。

+ 一种是执行过程中遇到异常 Abrupt Method Invocation Completion

  方法没执行完，然后代码中出现了未妥善处理的异常，或者jvm内部出错，反正只要是在该方法异常表里没找到匹配的异常处理器，就直接导致方法退出。

无论是那种方式退出，退出之后必须返回最初方法被调用时的位置，程序才能继续运行。
正常退出时，一般就是主调方法pc计数器的值就是返回地址。


方法退出的过程实际上等同于把当前栈帧出栈，因此退出时可能执行的操作有:恢复上层方法的 局部变量表和操作数栈，把返回值(如果有的话)压入调用者栈帧的操作数栈中，调整PC计数器的值 以指向方法调用指令后面的一条指令等。笔者这里写的“可能”是由于这是基于概念模型的讨论，只有 具体到某一款Java虚拟机实现，会执行哪些操作才能确定下来。

### Frame data: others

> 《Java虚拟机规范》允许虚拟机实现增加一些规范里没有描述的信息到栈帧之中，例如与调试、性能收集相关的信息，这部分信息完全取决于具体的虚拟机实现。

TODO 这部分也想略。
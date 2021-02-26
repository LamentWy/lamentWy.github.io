---
layout: post
current: post
title: JVM-类加载笔记-草稿
navigation: True
tags: [Scroll]
class: post-template
subclass: 'post tag-scroll'
---
# 笔记

Java虚拟机把描述类的数据从Class文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的Java类型，这个过程被称作虚拟机的类加载机制。

## 类加载的整个生命周期   

![pic of class life circle](https://lament-z.com/assets/images/JVM/class-life-circle.jpg)

其中验证、准备、解析三个阶段统称为：连接 Linking。

图中的整个生命周期中，解析可能在初始化之后才开始，并且很多阶段是交叉混合运行的，并不是某阶段结束后下一阶段才开始。

### 加载 Loading

加载阶段JVM要完成三件事：
1. 通过类的全限定名拿到这个类的二进制字节流。

2. 把这个字节流转化成方法区的运行时数据结构。

3. 内存中生成一个代表这个类的java.lang.Class对象，作为这个类在方法区的访问入口。

说白了就是找到类，拿到它的数据，按照JVM的要求进行存储，然后在内存里打个招牌，就可以营业了。

这一步就有很多方式:

比如从jar,war中读取

运行时计算生成（动态代理技术），java.lang.reflect.Proxy中的ProxyGenerator.generateProxyClass()。

从其他文件中生成类，比如jsp。

不常见和没见过的，从加密文件中获取，网络中获取，数据库中读取。

加载阶段获取类的二进制字节流的这个动作，是实现代码动态性的一个重要口子。

数组又不太一样。

数组本身是jvm在内存中直接动态生成的，但是它的类型也跟类加载有关系。
首先它有俩类型，一个element type ,一个component type.

component type 比较好理解，数组的特点就是它存储一堆相同类型的元素，在jvms里面 数组里这些元素就是component，他们的类型就是component type。

这个element type就很绕了，但实际麻烦的是component type，先上原文。
```
The component type of an array may itself be an array type.
The components of such an array may contain references to subarrays.
If, starting from any array type, one considers its component type, and then (if that is also an array type) the component type of that type, and so on, eventually one must reach a component type that is not an array type; this is called the element type of the original array, and the components at this level of the data structure are called the elements of the original array.
```
这也是《深入》一书中所说的去掉所有维度的类型。

就是数组中的元素可以是数组，而且这个嵌套可以一直往下套，一直套到最后这个数组它的元素不是数组为止，这个不是数组的元素的类型就叫元素类型。说白了就是数组套娃套完之后的类型。数组的元素类型最终依然需要类加载器来加载。

而数组的组件类型如果是引用类型的话，就要通过递归的方式加载组件类型，然后标识在对应的类加载器的 类命名空间上。
不是引用类型，则是把标识与引导类加载器关联。

加载阶段开始进行之后，就会有linking中的动作要开始执行了，比如校验：检查字节码文件的格式等。

但是开始的顺序肯定是 加载先开始，然后校验再开始。

### Linking - 1 校验 Verification

说加载的时候说了一万次二进制字节流，通过对加载阶段的了解，我们可以知道在字节码这个环节我们多少还是可以皮一下的。
所以JVM一定会对这些个二进制字节流进行校验。

瞄了一眼jvms 校验这块真的好长啊，大致可以分为以下四类校验。

挑几个好玩的记录一下。

1. 文件格式校验  

该验证阶段的主要目的是保证输入的字节流能正确地解析并存储于方法区之内，格式上符合描述一个Java类型信息的要求。

这阶段的验证是基于二进制字节流进行的，只有通过了这个阶段的验证之后，这段字节流才被允许进入Java虚拟机内存的方法区中进行存储，所以后面的三个验证阶段 全部是基于方法区的存储结构上进行的，不会再直接读取、操作字节流了。

比如：是否以魔数**0xCAFEBABE**开头

2. 元数据校验  

第二阶段是对字节码描述的信息进行语义分析。
有点像检查语法错误一样，主要是保证这些信息符合jls。

3. 字节码校验  

简单的说就是“检查代码”。

主要目的是通过数据流分析和控制流分析，确定程序语义是合法的、符合逻辑的。
这阶段要对类的方法体(Class文件中的Code属性)进行校验分析，保证被校验类的方法在运行时不会做出危害虚拟机安全的行为。

然后这事儿就很麻烦（“停机问题”(Halting Problem)），最终在JDK 6之后的Javac编译器和Java虚拟机里进行了一项联合优化，把尽可能多的校验辅助措施挪到Javac编译器里进行。

具体做法是给方法体Code属性的属性表中新增加了一项名为“StackMapTable”的新属性，这项属性描述了方法体所有的基本块(Basic Block，指按照控制流拆分的代码块)开始时本地变量表和操作栈应有的状态。

在字节码验证期间，Java虚拟机就不需要根据程序推导这些状态的合法性，只需要检查StackMapTable属性中的记录是否合法即可。

将字节码验证的类型推导转变为类型检查，从而节省了大量校验时间。

4. 符号引用校验  
这个校验发生在 JVM 将符号引用转化为直接引用的时候，而这个转化行为发生在解析阶段。

### linking - 准备 Preparation

准备阶段是正式为类变量(the static fields for a class or interface)分配内存并设置类变量初始值的阶段.

这些变量所使用的内存都应当在方法区中进行分配，但是方法区本身是个抽象概念，Java8之前是永久代实现方法区，所以分配在永久代（假设有），Java8开始用元空间替换永久代，以前永久代的一部分数据现在被安排去了Heap中，这里面就包括类变量。

并且这里设置类变量的初始值的时候，设置的是这个变量所属类型的0值。
例如 ``` public static int i = 123; ``` 在这个阶段就是i=0，123这个值要等到类初始化阶段，类构造器<clinet>()方法里面，putstatic指令把123给到i.

但是像```Integer.MAX_VALUE```这种则会直接赋值为```0x7fffffff```，因为它的定义是```public static final int   MAX_VALUE = 0x7fffffff;``` 多了个final，这个值不可变，那么在编译阶段，也就是javac干活的时候会给 MAX_VALUE 生成 ConstantValue 属性，等到准备阶段，JVM 会根据 ConstantValue 来初始化 MAX_VALUE 的值。

### 解析 Resolution

解析阶段是Java虚拟机将常量池内的符号引用替换为直接引用的过程.

符号引用(Symbolic References):  

符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。

直接引用(Direct References):  

直接引用是可以直接指向目标的指针、相对偏移量或者是一个能间接定位到目标的句柄。


---
TODO 需要重新思考这部分内容。

直接引用是和虚拟机实现的内存布局直接相关的，同一个符号引用在不同虚拟机实现上翻译出来的直接引用一般不会相同。如果有了直接引用，那引用的目标必定已经在虚拟机的内存中存在。

JVMS只规定了解析发生的时间需要在执行操作符号引用的字节码指令之前，具体是类加载时就解析还是要用的时候再解析要根据不同JVM自身实现来看。

对同一个符号引用进行多次解析 和 一次解析之后缓存解析结果 这两种情况都是存在的。

比如对于invokedynamic这种用于动态语言支持的指令，就肯定每次都要重新解析。

解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符这7 类符号引用进行，分别对应于常量池的CONSTANT_Class_info、CONSTANT_Fieldref_info、 CONSTANT_M ethodref_info、CONSTANT_InterfaceM ethodref_info、 CONSTANT_MethodType_info、CONSTANT_MethodHandle_info、CONSTANT_Dyna-mic_info和
CONSTANT_InvokeDynamic_info 8种常量类型。

---

### 初始化  initialization
初始化阶段就是执行类构造器```<clinit>()```方法的过程。

除了通过自定义类加载器的方式局部参与之外，其他的动作全部由JVM控制。

#### 类构造器方法 ```<clinit>()```
这个方法是编译阶段javac根据源代码自动生成的。

·```<clinit>()```方法是由编译器自动收集类中的所有**类变量的赋值动作**和**静态语句块**```(static{})```中的语句合并产生的，编译器收集的顺序是由语句在源文件中出现的顺序决定的。


___

TODO  考虑移除这个种阻塞的描述

JVM必须保证一个类的```<clinit>()```方法在多线程环境中被正确地加锁同步，如果多个线程同时去初始化一个类，那么只会有其中一个线程去执行这个类的```<clinit>()```方法，其他线程都需要阻塞等待，直到活动线程执行完毕```<clinit>()```方法。如果在一个类的```<clinit>()```方法中有耗时很长的操作，那就可能造成多个进程阻塞，在实际应用中这种阻塞往往是很隐蔽的。

同一个类加载器下，一个类型就初始化一次，虽然其他线程会被阻塞，但是只要干活儿的那个线程退出```<clinit>()```方法之后，其他阻塞线程唤醒后不会再进入该方法。
___

#### 必须执行初始化的六种情况  

TODO 很明显这六种必须初始化的情况都属于 类可能是第一次被使用的情况。 考虑移除。

1. 遇到这四种字节码指令时：new getstatic putstatic invokestatic。

  如果遇到这些字节码指令，但是类型还没有初始化时，要先初始化。

  对应的代码场景为：

  new 对象的时候。

  读取或设置一个类型的静态字段(被final修饰、已在编译期把结果放入常量池的静态字段除外，如Integet.MAX_VALUE)
的时候。

  调用一个类型的静态方法的时候。

2. 使用java.lang.reflect包的方法对类型进行反射调用的时候

  如果类型没有进行过初始化，则需要先触发其初始化。

3. 当初始化类的时候，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化。

4. 当虚拟机启动时，用户需要指定一个要执行的主类(包含main()方法的那个类)，虚拟机会先
初始化这个主类。

5. 当使用JDK 7新加入的动态语言支持时。
如果一个java.lang.invoke.MethodHandle实例最后的解析结果为REF_getStatic、REF_putStatic、REF_invokeStatic、REF_newInvokeSpecial四种类型的方法句柄，并且这个方法句柄对应的类没有进行过初始化，则需要先触发其初始化。

6. 当一个接口中定义了JDK 8新加入的默认方法(被default关键字修饰的接口方法)时，如果有 这个接口的实现类发生了初始化，那该接口要在其之前被初始化。

以上场景中的行为统称为对一个类型进行**主动引用**。

有主动肯定还有**被动引用**。

## 类加载器

能实现 **通过一个类的全限定名来获取描述该类的二进制字节流** 这个功能的代码，都叫类加载器。

### 类和类加载器  

对于任意一个类，都必须由加载它的类加载器和这个类本身一起共同确立其在Java虚拟机中的唯一性。

每一个类加载器，都拥有一个独立的类名称空间。

两个类是否相等必须是处于同一个类加载器下面，否则哪怕是同一个class文件被同一个VM加载，只要加载器不同，这俩类就不一样，equals() isAssignableFrom(),isInstance()等都受到类加载器的影响。

这个验证也很好验证，自己创建一个类A和一个类加载器CL，然后用CL加载一个A出来，去跟直接new的比。

### 双亲委派 parents delegation model  
翻译导致误解的又一个例子，歧义有点多，反正我第一次看到这个词以为是从双亲向下递归，结果它是反着来的。

在JVM的视角，只存在两种不同的类加载器.

启动类加载器（Bootstrap ClassLoader)，由C++实现（也有java写的关机方法通过JNI回调c），是JVM自身的一部分；

另一种就是其他类加载器，独立于JVM之外，由java语言实现的，全部继承自抽象类java.lang.ClassLoader。

Extension ClassLoader

Application ClassLoader

双亲委派模型的工作过程是:

如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送到最顶层的启动类加载器中，只有当父加载器反馈自己无法完成这个加载请求(它的搜索范围中没有找到所需的类)时，子加载器才会尝试自己去完成加载。

但它不是一个强制性约束。

### 破坏双亲委派模型

Java发展的历史上出现过三次大规模破坏双亲委派模型的情况。
Java9之后顺便发生了第四次。

1. 第一次导致模型被破坏是双亲委派模型诞生之前。
java.lang.ClassLoader在Java第一个版本就存在了，搞出来好多自定义的classLoader，但是双亲委派模型从Jdk1.2开始引入，为了向前兼容，无法用技术手段避免loadClass()被子类覆盖的可能性，只能在JDK 1.2之后的java.lang.ClassLoader中添加一个新的 protected方法findClass()，并引导用户编写的类加载逻辑时尽可能去重写这个方法，而不是在 loadClass()中编写代码。


2. 第二次被破坏是由于模型自身的短板  

双亲委派很好地解决了各个类 加载器协作时基础类型的一致性问题(越基础的类由越上层的加载器进行加载)，基础类型之所以被称为“基础”，是因为它们总是作为被用户代码继承、调用的API存在。

但是还存在基础类型又要调用回用户的代码的情况。

然后Java设计团队引入了一个可以逆向的加载器，线程上下文类加载器 (Thread Context ClassLoader)。这个类加载器可以通过java.lang.Thread类的setContext-ClassLoader()方 法进行设置，如果创建线程时还未设置，它将会从父线程中继承一个，如果在应用程序的全局范围内都没有设置过的话，那这个类加载器默认就是应用程序类加载器。

有了这个加载器，就可以做出父类加载器去请求子类加载器完成类加载的行为。

Java中涉及SPI的加载基本上都采用这种方式来完成，例如JNDI、 JDBC、JCE、JAXB和JBI等。不过，当SPI的服务提供者多于一个的时候，代码就只能根据具体提供 者的类型来硬编码判断，为了消除这种极不优雅的实现方式，在JDK 6时，JDK提供了 java.util.ServiceLoader类，以M ETA-INF/services中的配置信息，辅以责任链模式，这才算是给SPI的加 载提供了一种相对合理的解决方案。

3. 热部署

OSGi模块化热部署。 核心就是自定义的类加载机制，每个bundle都有自己的类加载器，需要热替换的时候，连bunlde带它的类加载器一起替换。

4. 模块化

双亲委派模型基本还存在，但是发生了一丢丢变化，委派给父加载器之前要先判断下能不能归属到某个系统模块，如果可以就优先委派给负责该模块的加载器。



>**Run-time Built-in Class Loaders** <br/>
The Java run-time has the following built-in class loaders:<br/><br/>
***Bootstrap class loader*** <br> It is the virtual machine's built-in class loader, typically represented as null, and does not have a parent.<br>
***Platform class loader*** <br>All platform classes are visible to the platform class loader that can be used as the parent of a ClassLoader instance. Platform classes include Java SE platform APIs, their implementation classes and JDK-specific run-time classes that are defined by the platform class loader or its ancestors.<br>
To allow for upgrading/overriding of modules defined to the platform class loader, and where upgraded modules read modules defined to class loaders other than the platform class loader and its ancestors, then the platform class loader may have to delegate to other class loaders, the application class loader for example. In other words, classes in named modules defined to class loaders other than the platform class loader and its ancestors may be visible to the platform class loader.<br>
***System class loader*** <br>It is also known as application class loader and is distinct from the platform class loader. The system class loader is typically used to define classes on the application class path, module path, and JDK-specific tools. The platform class loader is a parent or an ancestor of the system class loader that all platform classes are visible to it.


更多细节参考：
[JDK9-Docs-ClassLoader](https://docs.oracle.com/javase/9/docs/api/java/lang/ClassLoader.html)

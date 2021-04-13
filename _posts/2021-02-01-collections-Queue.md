---
layout: post
current: post
title: Java集合类系列:Queue
navigation: True
description: Queue
tags: [Scroll]
class: post-template
subclass: 'post tag-scroll'
---

# Queue 笔记

## 几种不同场景下的队列

数据结构中，通常把 FIFO 这种叫做队列，而 FILO 这种叫做栈。

但是编码的时候队列和栈的实现都可以“看作”是不同性质的队列。


## Queue In Java

Queue 在Java中是作为接口出现的，它的文档也提现了这一点。

FIFO就不是强制性的了。 这个接口定义了 新增/删除/检测三类操作，并且规定了两套实现规则：抛异常/返回特定值,见下表。

|         | Summary of Queue methods |                       |
| ------- | ------------------------ | --------------------- |
| ops     | Throws exception         | Returns special value |
| Insert  | add(e)                   | offer(e)              |
| remove  | remove()                 | poll()                |
| Examine | element()                | peek()                |


### Deque

Deque is short for "double ended queue".

它继承Queue接口，并在这个基础上做了双端队列的扩展，自然规定了双端队列的一整套方法。
同时它可以用作传统的FIFO "队列" ，也可以用作 "FILO" "栈"。 Java为这两种"场景"都提供了对应的方法。

+ 作为双端队列时，提供了12个方法

***Summary of Deque methods***

| ops     | Head               |                 | Tail               |                 |
| ------- | ------------------ | --------------- | ------------------ | --------------- |
|         | *Throws exception* | *Special value* | *Throws exception* | *Special value* |
| Insert  | addFrist(e)        | offerFirst(e)   | addLast(e)         | offerLast(e)    |
| Remove  | removeFirst()      | pollFirst()     | removeLast()       | pollLast()      |
| Examine | getFirst()         | peekFirst()     | getLast()          | peekLast()      |

+ 作为 FIFO Queue 时，提供如下方法

***Comparison of Queue and Deque methods***

| Queue Method | Equivalent Deque Method |
| ------------ | ----------------------- |
| add(e)       | addLast(e)              |
| offer(e)     | offerLast(e)            |
| remove()     | removeFirst()           |
| poll()       | pollFirst()             |
| element()    | getFirst()              |
| peek()       | peekFirst()             |


+ 作为 FILO Stack时，提供如下方法

***Comparison of Stack and Deque methods***

| Stack Method | Equivalent Deque Method |
| ------------ | ----------------------- |
| push(e)      | addFirst(e)             |
| pop()        | removeFirst()           |
| peek()       | peekFirst()             |


### ArrayDeque

  ArrayDeque 是基于变长数组对 Deque 的实现。

#### 性质
可自动扩容，具备双端队列的能力，非线程安全，具备快速失败的能力。

#### 源码分析

##### 基于数组

  就是个 Object 数组。
  ```
  transient Object[] elements; // non-private to simplify nested class access
  ```

  不过由于这个场景是双端队列，所以要看作是循环数组，在普通数组的基础上添加了头尾的标记，数组的头尾不再是array[0] 和 array[size-1]，而是由 head / tail 来决定，其中tail永远指向数组中的一个未被占用的元素。

  ```
  transient int head;

  transient int tail;
  ```
##### 安全性：非线程安全

快速失败，根据索引删除方法``delete(int index)``和遍历元素的代码中都可以看到。

##### 遍历

俩迭代器实现，一个从head遍历到tail，一个从tail到head。

##### 构造方法：3个 无论使用哪个构造方法，其容量一定是2的幂。
 1. 无参，默认数组容量为16
    ```
    public ArrayDeque() {
        elements = new Object[16];
    }
    ```
 2. 指定数组容量 ,但数组真实容量永远是正好比 numElements 大的2的n次幂
    ```
    public ArrayDeque(int numElements) {
        allocateElements(numElements);
    }
    ```

    其中的``allocateElements(numElements); ``的逻辑很简单就是``new Object[calculateSize(numElements)]``

    主要看一下计算数组容量，如何让数组的真实容量永远是正好比 numElements 大的2的n次幂

    当传入数组容量的参数不是2的幂时
    ```
     private static int calculateSize(int numElements) {
            int initialCapacity = MIN_INITIAL_CAPACITY; //这个值为 8

            // Find the best power of two to hold elements.
            // Tests "<=" because arrays aren't kept full.
            if (numElements >= initialCapacity) { //如果我们指定的值大于8
               initialCapacity = numElements;
               initialCapacity |= (initialCapacity >>>  1);
               initialCapacity |= (initialCapacity >>>  2);
               initialCapacity |= (initialCapacity >>>  4);
               initialCapacity |= (initialCapacity >>>  8);
               initialCapacity |= (initialCapacity >>> 16);
               initialCapacity++;

           if (initialCapacity < 0)   // Too many elements, must back off
               initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
                }
                return initialCapacity;
            }
    ```
    以上代码的逻辑是：

    如果我们给定的数组容量小于8，则按8创建。
    如果比8大，则找到 正好大于给定值的2^n幂。
    如果这个值大到INT越界，那它就是个负数，>>>=1 可以把它变成一个至少 2 ^ 30 级别的整数

    这里分析下计算容量的代码：

    ```
    initialCapacity |= (initialCapacity >>>  1);
    initialCapacity |= (initialCapacity >>>  2);
    initialCapacity |= (initialCapacity >>>  4);
    initialCapacity |= (initialCapacity >>>  8);
    initialCapacity |= (initialCapacity >>> 16);
    ```

    5行代码都非常相似，它的功能是找到比 numElements 大的那个 2^n .

    原理如下：
    首先，所有 2^n 的值，转化成二进制之后，肯定长这样： 1000000...0000。
    然后减去1 ，变成 01111111....1111这样，高位的0忽略，就是 11111111...11111。

    那怎么把 任意一个整数变成 1111...1111这样呢，就是代码里的办法了。

    任意整数的二进制表示，忽略掉高位的0之后，他左边的第一位肯定是1，也就是长这样：
    1xxxxxxx。
    当它向右移动一位并补0之后，就变成01xxxxx

    1xxxxxxx
    01xxxxxx
    --或运算--
    11xxxxxx

    这个时候，initialCapacity = 11xxxxxx 了

    后面无论移动多少位都是这个原理，因为或操作代表只要不同时为0，都是1，而>>>是一个右移补0的操作，意味着高位永远是1。
    一共最多移动 1 + 2 + 4 + 8 + 16 = 31位，
    如果这个数字非常大，也就是当31位移动完之后，才填满所有的1，一个填满了31位 111...111，再加1妥妥的越界啦。

    同时这个操作还有个好处是，当你得到想要的结果后，后面的重复计算不会改变结果。  

    假设``numElements=9``:   

    ```
    第一次运行
    1001  aka initialCapacity = 9
    0100  aka initialCapacity >>> 1
    -或运算-
    1100

    第二次运行
    1100
    0011 aka initialCapacity >>>  2
    ----
    1111 到这里其实已经拿到结果了

    第三次运行
    1111
    0000 aka initialCapacity  >>>  4
    ----
    1111

    后两次略
    ```

    同时这个过程可以看出来为什么 移动的位数 是 1 2 4 ...这个序列。
    移动第一次之后，头两位肯定是11xxxx，所以第二次直接右移动2补俩0，下一次自然就是1111xxx，以此类推。



 3. 把集合类的实例转化成对应的Deque的实例

    ```
    public ArrayDeque(Collection<? extends E> c) {
        allocateElements(c.size());
        addAll(c);
    }
    ```
##### ArrayDeque 中操作元素的几个主要方法

  ArrayDeque 是 Deque 的实现类，而前面介绍 Deque 时罗列的那么多方法它都得实现，打开源码能看到一万个方法，但是实际上主要的新增和删除元素的方法就四个：addFirst，addLast，pollFirst，pollLast。它源码里有三行单行注释说的就是这个。

  其他方法是根据这些定义的，基本不用看。

  1. addFirst()   

  ```
  public void addFirst(E e) {
            if (e == null)
       throw new NullPointerException();
            elements[head = (head - 1) & (elements.length - 1)] = e; //主要看这句
            if (head == tail) //数组已满需要扩容
            doubleCapacity(); //这个后面展开
  }
  ```

  重点说addFirst，这个看明白了剩下3个都不难，而且这个跟自动扩容也有点关系。

  这个方法的作用是在 head 之前插入一个元素，并且把这个元素作为新的 head。

  完成这个操作需要考虑容量溢出和下标越界两种情况：

  1. 容量溢出  
    在 addFirst() 方法中一定不会出现容量溢出，因为在 ArrayDeque 的实现中，tail 永远指向一个可插入数据的空数组元素，所以 head最少也可以占用这个空，此时也正好满足插入完成后，head == tail 从而触发扩容。

  2. 数组下标越界  
    下标越界是指，由于是双端链表，头尾位置不固定，对于这个方法而言就是发生在 head = 0 时，此时 head = head -1 ,也就是 -1。

      下面来详细分析下``head = (head - 1) & (elements.length - 1)``这段代码 ,它即完成了计算head新值的功能，又解决了-1越界的问题。

      先从功能上分析，addFirst 时，head 的新值只需要考虑两种可能，head 是否为0，我们自己写代码时可能就会写出如下逻辑：
      ```
      //伪代码
      if (head == 0){
        head = length -1;
      }else{
        head = head -1;
      }
      ```

      而源码中则利用"与"运算的几个小技巧（tips的验证和推导在最后）：
      > Tips1: n为任意整数，-1 & n 等价于 求n的补码，我们用的都是补码，说白了就是n， -1 & n === n

      > Tips2: 任意正整数 m,n ,当 m <= 2^n-1时, m & (2^n - 1) ==  m mod (2^n -1) 文字描述： m 对 2^n - 1 进行与操作，等价于 m 对 2^n - 1 取模。  

      这俩技巧有啥用呢，我们回到前面 head 求值那行代码。
      ```
      head = (head - 1) & (elements.length - 1);

      //当head = 0时，容量甭管是多少，反正肯定是2^n ,上面的代码实际上变成了
      head = （-1 & elements.length - 1） = elements.length - 1 // 运用了tips1

      //当head != 0 时，head的取值范围是 1 ～ (length -1) ，head - 1 的范围就是 0 ~ (length -1 -1 )。
      // 也就是说 head - 1 < length - 1 恒成立 。
      // 俩正整数 n<m 时，n mod m 恒等于 n ，这里如果一下想不明白的话，可以套用数学里面的求余。
      head = (head -1) & (elements.length - 1) = head -1 ; //tips2
      ```
      源码用一行代码替换了我们前面写的if/else逻辑，并且提供了一些性能上的优势。

      这也是为什么数组的实际容量 (elements.length) 一定是2的幂次，这是因为可以方便这里的取模(mod)操作，注意 mod (取模) 跟 % (取余) 对于计算机而言是有区别的，主要是返回结果的取舍上，比如 4 & 3 == 0 ，4 % 3 == 1。


  2. addLast()  

  ```
  public void addLast(E e) {
      if (e == null)
        throw new NullPointerException();
        elements[tail] = e;
      if ( (tail = (tail + 1) & (elements.length - 1)) == head)
        doubleCapacity();
  }
  ```
  这个方法的作用是在 Tail 处直接添加新元素，然后 Tail 指向下一个空的数组元素，如果没有空的就进行扩容。与`` addFirst() ``基本类似，区别在于要保证 Tail 永远要指向一个空的数组元素。

  还是老样子，根据这个需求我们先自己尝试实现一下。尾部同样是容量溢出和数组下标越界两个问题需要解决。

  ```
    //伪代码  
    elements[tail] = newElement; //这一步是一定能完成的
    //判断数组此时是否已满，如果满了则扩容，如果没满，则tail +1

  if(isFull()){
    grow(); //扩容
    tail= tail + 1;
  }else{
    tail = tail + 1;
  }
  //由于是双端队列，判断数组是不是满了分为两种情况
  // head > tail 时，tail + 1 =head 则满了
  // head < tail 时，head + tail + 1 = length 就满了

  isFull(){
      if(head > tail){
        return tail +1 == head;
      }else{
        return head + tail + 1 == length;
      }
  }
  ```
  上面的伪代码只解决了容量溢出的问题，没有解决数组下标越界的问题。

  比如情况1： head=4 ，tail=6，length=7，按照双端队列 tail 之后应该为 0，而不是 7；

  或者情况2： 如果head是1，tail是0，触发扩容，扩容之后，tail+1指到head头上去了。

  ```
    //伪代码 moveTail只能解决情况1.
    moveTail(){
      if (tail + 1 == length){
        tail = 0;
      }else{
        tail = tail + 1;
      }
    }

    //为了解决情况2，我们需要在扩容时把双端数组规整一下。让head在新数组中回到0的位置去，其他的按照顺序依次填充。
    grow(){
      newArrary[length*2];
      //遍历旧数组，把head放到newArray[0]，其他的按顺序排列。
      head = 0;
      tail = length -1; //因为我们的伪代码里 grow()之后tail 还要+1
    }
  ```

  是不是特别麻烦，下面来看源码里为什么这么短。  
  ```
  elements[tail] = e; //向空的数组元素中插入数据
  if ( (tail = (tail + 1) & (elements.length - 1)) == head) //即完成了tail的赋值工作，又完成了数组是否已满的判断。
    doubleCapacity();
  ```

  由于 tail 永远指向一个可插入数据的空数组元素，所以插入数据这一步``elements[tail] = e;``是一定会完成的，如果满了就进行扩容，除非扩容失败，比如数组的容量超过数组允许的最大值会``throw new IllegalStateException("Sorry, deque too big");``。扩容解决了，但是 tail 如何指向空的数组元素，这个在``doubleCapacity();``中进行保证，逻辑大体跟我们伪代码里一样，只不过它的tail = length，细节在扩容部分再说。

  ```
    //拆分代码
    tail = (tail + 1) & (elements.length - 1);

    if(tail == head){
      doubleCapacity();
    }
  ```  

  根据前面 addFirst()中学到的技巧，当 (tail + 1) <= (elements.length - 1) 时，tail = tail + 1。但是 tail = length-1 时， 很明显 tail + 1 就是 length , 这时候代码转化为 tail = length & （length -1）= 0, 由于length一定是2^n所以，tail一定为0(这里不明白拉到最后看一眼tips3)。

  由于先计算了tail的新值，所以判断数组是否满了也不需要我们之前伪代码演示的那么麻烦了，直接判断 head == tail即可，并且也不存在tail 指向head这种情况发生，因为扩容之后head会重新回到0，tail则为length。

  3. pollFirst()  

  ```
    public E pollFirst() {
        int h = head;
        @SuppressWarnings("unchecked")
        E result = (E) elements[h];
        // Element is null if deque empty
        if (result == null)
            return null;
        elements[h] = null;     // Must null out slot
        head = (h + 1) & (elements.length - 1);
        return result;
    }
  ```

  这方法就简单多了，把 elements[head] 取出来，然后把这个数组元素置为空，移动head。需要注意的是```elements[h] = null;     // Must null out slot``` 注释也说了，必须null掉。虽然好像不null掉也问题不大，判断扩容啥的都是根据下标来的，实际上问题很大，比如```contains(Object o)```方法就会出现poll完了，怎么还在的情况。

  4. pollLast
    同上，略。

##### 自动扩容
  当队列满了，或者说数组填充满元素时会触发自动扩容。

 + 触发扩容的条件

   只有添加元素时才会触发自动扩容，也就是`` addFirst()``、``addLast()`` 这俩方法。

 + 自动扩容：

 ```
 private void doubleCapacity() {
        assert head == tail;
        int p = head;
        int n = elements.length;
        int r = n - p; // number of elements to the right of p
        int newCapacity = n << 1;
        if (newCapacity < 0)
            throw new IllegalStateException("Sorry, deque too big");
        Object[] a = new Object[newCapacity];
        System.arraycopy(elements, p, a, 0, r);
        System.arraycopy(elements, 0, a, r, p);
        elements = a;
        head = 0;
        tail = n;
 }        
 ```

 大体逻辑跟我们在伪代码中写的一样。
 通过 doubleCapacity() ，直接将原数组扩为2倍大小， int newCapacity = n << 1;
 如果这个值超过了数组允许的最大值，则throw new IllegalStateException("Sorry, deque too big");
 当扩容成功之后，对数组进行复制，由于循环数组是有头尾的，而且头尾会移动，
 所以复制的时候会在新数组中重新拼接一下之前的数组。

 ```
 //原数组中从 head 开始，向右到数组最后一个元素，顶头放入新数组
 System.arraycopy(elements, p, a, 0, r);
 //原数组中从 tail 开始, 向左的全部元素（换个方向描述就是从element[0] 到 tail 的全部元素)，拼接到a剩下的空槽中
 System.arraycopy(elements, 0, a, r, p);
```




## tips

### 位运算符 和 正反补

  表格的样式有点问题，讲究看吧，我前端是渣渣搞不清楚这个。

**位运算符**

| ops | 中文名      | 描述                                                 | 口诀 / 小技巧    | 例子                  |
| --- | ----------- | ---------------------------------------------------- | ---------------- | --------------------- |
| &   | 与          | 如果相对应位都是1，则结果为1，否则为0                | 同1为1，不同为0  | A & B                 |
| 或  | 或          | 如果相对应位都是 0，则结果为 0，否则为 1             | 同0为0，不同为1  | A                     |
| ^   | 异或        | 如果相对应位值相同，则结果为0，否则为1               | 相同为0，不同为1 | A ^ B                 |
| ～  | 取反        | 也叫按位取反，就是把每一位的数反转，即0变成1，1变成0 | 取反不需要口诀吧 | ~A                    |
| <<  | 左移        | 把符号左面的数的二进制版本向左移动n位                | 1 << n = 2^n     | 假设A = 1, A << 2 = 4 |
| >>  | 右移        | 跟左移一样，不过方向向右                             | n/a              | 假设A = 1, A >> 2 = 0 |
| >>> | 按位右移补0 | 跟右移差不多，不过空位要补0                          |                  |                       |

---

***正反补码***

| name | 描述                                                                        | 正负0                                                                          | 拓展                                                                                                |
| ---- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
| 原码 | 原码最高位为符号为，0正1负，其余位置表示数值，不足位时在符号为和数值之间补0 | 正数的原码就是它本身，符号位为0；负数的原码符号位是1，其他跟正数一样；0就全是0 | 大致长这样：{1: 0 0001} {-1: 1 0001} {0: 0 0000} 但是不能做加减法，-1+1原码的结果是-2，于是搞了反码 |
| 反码 | 反码主要折腾负数，负数的反码符号位依然为1，但是其他位要全部取反。           | 正数还是跟原码一样，负数前面说了，0就尴尬了，有俩0.                            | 反码解决了 1-1 = -2的问题，但是根据符号位不同，诞生了正0和负0两个0                                  |
| 补码 | 补码也是折腾负数，负数的补码是在其反码的末位+1，如果超出最高位则丢弃最高位  | 正数正反补码都一样，负数就是前面说的反码末位+1，0就是0啦                       | 补码解决了双0问题，反码中负0全是1，末位+1之后最高位越界丢弃，只剩0，终于只有一个0了。               |


补码，中补的来源，two's complement，对2求补，这是离散数学中的一种计算方法，我念书的时候为啥离散数学没教这个。

---

### tips1   

-1 & n === 求n的补码 aka -1 & n === n，n 可以是任意整数。
```
推导过程:
-1 ，其符号位为1，原码为 1 0001，但是计算机存储负数是存其补码，也就是符号位不变，其他位反转，之后末位+1
-1的反码: 1 1110 ,  
-1的补码: 1 1111 。

然后按位与操作的原则是： 同1则1，不同为0.

很明显-1这个所有位上全是1的二进制数 跟 任何整数 进行 与操作 都不会改变对方的值。

1111     1111
1010     0000
--------------
1010     0000

```

### tips2

 任意正整数 m,n ，当 m <= 2^n-1时， m & (2^n - 1) ==  m mod (2^n -1)

 描述： m 对 2^n - 1 进行与操作，等价于 m 对 2^n - 1 取模。

```
 2^n 的二进制除了高位有个1，低位全是0，也就是 1000...000 这个样子。
 2^n -1 的二进制低位全是1，也就是 000111..111 这种样子。

 类似上面-1的那一堆1是不是。
 当 m <= 2^n - 1 时候，那么一定是这个样子
 m    : 0000 1001
 2^n-1: 0111 1111
 -----------------
 肯定还是m本身，跟前面-1的情况基本类似。  
 从取模的角度去思考的话，跟 2 mod 10 = 2 一样。
```

### tips3

```
2^n & 2^n-1 === 0
证明看下图:
2^n   : 1000...000
2^n-1 : 0111...111
----------按位与-----
        0000000000

```

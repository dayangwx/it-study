# JVM

---

### 1.JVM作用

> 
>

### 2.JVM体系结构

> 五大部分：类装载器子系统 、 运行时数据区 、 执行引擎 、 本地方法接口 和 垃圾收集模块

![image-20220529211828213](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529211828213.png)

![preload](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/3yqcc1zbgl.png)

#### 2-1.JVM的组成部分

> 五大部分：类装载器子系统 、 运行时数据区 、 执行引擎 、 本地方法接口 和 垃圾收集模块

- 类加载子系统

  Loading - 加载，顾名思义，用于加载类，它有三种类加载器，根据双亲委托模型，从不同路径进行加载：

      Bootstrap ClassLoader - 加载 rt.jar 核心类库，是优先级最高的加载器
      Extension ClassLoader - 负责加载 jre\lib\ext 文件夹中的类
      Application ClassLoader -负责加载CLASSPATH 指定的类库

  Linking - 链接，动态链接到运行时所需的资源，分为三步：

      Verify - 验证：验证生成的字节码是否正确
      Prepare - 准备：为所有静态变量，分配内存并赋予默认值
      Resolve - 解析：将 class 文件常量池中所有对内存的符号引用，替换成到方法区的直接引用

  Initialization - 类初始化，类加载的最后阶段，这里对静态变量进行赋值，并执行静态块。（注意区分对象初始化）

- 运行时数据区

  堆、栈、本地方法栈、程序计数器、方法区

- 执行引擎

  运行时数据区存储着要执行的字节码，执行引擎将会读取并逐个执行。

- 本地方法接口(JNI)

- 垃圾回收模块

  收集和删除未引用的对象

### 3.类加载器

> 虚拟机的类加载机制：Java虚拟机将描述类的数据从class字节码文件加载到内存，并且对数据进行校验，转化，解析，初始化的工作，最终形成在内存中可以直接使用的数据类型。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529214202769.png" alt="image-20220529214202769" style="zoom:47%;" />

​	JVM三类类加载器：

​	1：启动类(根)加载器 BootstrapClassLoader （这个是用C++写的，所以如果打印这个加载器的话，就是个null）

​	2：扩展类加载器   ExtClassLoader

​	3：应用程序加载器 AppClassLoader

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529211849767.png" alt="image-20220529211849767" style="zoom:33%;" />

### 4.双亲委派机制

#### 什么是双亲委派机制

> 双亲委派模型：当一个类加载器接收到类加载请求时，会先请求其父类加载器加载，依次递归，当父类加载器无法找到该类时（根据类的全限定名称），子类加载器才会尝试去加载。

​	双亲委派机制的作用就是为了保证安全。比如说我自己也写了一个String类，包名也是java.lang，那我的程序调用的时候就会

调用我自己写的这个String类吗？显然不是的。那具体过程就是：

​	首先：类加载器收到类加载的请求，

​	然后：然后将这个请求向上委托给父类加载器(parent classloader)，一直向上委托，直到启动类加载器(RootClassLoader)

​	其次：到了启动器类加载器(RootClassLoader)检查是否能够加载这个String类，能加载就结束，如果不能加载，抛出异常，通知子类加载器去加载，去ExtClassLoader中找，找不到再去AppClassLoader中找，如果都找不到那就报错：Class Not Found，找到了就可以使用。

#### 为什么使用双亲委派模型

**双亲委派模型是为了保证Java核心库的类型安全。**所有Java应用都至少需要引用java.lang.Object类，在运行时这个类需要被加载到Java虚拟机中。如果该加载过程由自定义类加载器来完成，可能就会存在多个版本的java.lang.Object类，而且这些类之间是不兼容的。

通过双亲委派模型，对于Java核心库的类的加载工作由启动类加载器来统一完成，保证了Java应用所使用的都是同一个版本的Java核心库的类，是互相兼容的。

### 5.沙箱安全机制



### 6.Native

> native关键字，当java代码中的方法用到了native，说明它已经超出了java的范畴了，它要去调用底层的C语言库。
>
> 在设计之初，C语言非常火爆，java为了能够调用C语言，就有了native，后来**native主要就是用来调用第三方类库的**。
>
> 它存放在本地方法库中，然后通过JNI(java native interface)去调用本地方法库。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529211906903.png" alt="image-20220529211906903" style="zoom:33%;" />

### 7.PC寄存器



### 8.方法区

> 方法区是一个比较小的空间，它里面存放着常量、静态变量、类信息(构造方法、接口定义)，以及运行时的常量池。不过需要注意，实例变量存储在堆内存中。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529211934732.png" alt="image-20220529211934732" style="zoom:50%;" />

### 9.栈

> 栈是一种数据结构。
>
> 程序=数据结构+算法
>
> 程序=框架+业务逻辑 这种很容易被淘汰

通常情况下，与栈类比的一个数据结构是：队列(queue)

栈：先进后出

队列：先进先出(FIFO:first input first output)

栈内存，主程序的运行，与线程共存亡。【**栈式线程级的，有很多个**，而堆一个JVM只有一个堆(heap)】

线程一旦结束，栈内存就释放。当然**栈不存在垃圾回收问题**，线程完了，栈也跟着结束了！

栈里有：引用，八大基本数据类型(byte、short、int、long、float、dubble、boolean、char)



如果一个方法调用另一个方法，而被调用的方法又反过来调用调用它的方法，并且没有出口(其实就是死锁)，

这种情况就会出现内存溢出(StackOver Flow)

### 10.三种JVM

- Sun hotspot 这也是我们学习的
- BEA JRockit
- IBM J9 VM  运行在IBM自己的一些机器上，独占优势。

### 11.堆

> 类加载器加载了class后，把什么东西放到堆内存呢？
>
> 堆内存是用来存储引用类型的真实对象：类、方法、常量、变量等；
>
> 一个JVM只有一个堆内存。 

堆又细分为三个区域：

- 新生区：用来存放young/new的对象。
- 老年区：用来存放old对象
- 永久存储区：perm 基本数据类型，JVM内置的一些东西。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212004206.png" alt="image-20220529212004206" style="zoom:35%;" />

GC分为轻GC和重GC，

轻GC主要就是回收新生区的垃圾，

重GC住哟啊回收老年区的垃圾。

OOM：OutOfMemoryError:java heap space。堆内存溢出。

就是产生了非常多的垃圾，GC都回收不过来，把整个堆内存都占满了。

**java8之后，永久存储区改名为：元空间(meta space)。**

### 12.新生代区、老年代区

> 新生区和老年区占满了堆内存的空间，元空间(Metaspace)是个逻辑上的概念，在物理上并不存在，里面的常量保存在老年区的某个地方。 
>
> 
>
> **新生代区是用来存放新生的对象。一般占据堆的 1/3 空间。由于频繁创建对象，所以新生代会频繁触发 MinorGC 进行垃圾回收。**
>
> **老年代区主要存放应用程序中生命周期长的内存对象。**
>
> GC垃圾回收过程大概是：
>
> ​	程序运行，有新对象创建，当把伊甸区占满后会来一次轻GC，回收一部分垃圾，并且把存活下来的放到幸存区，程序一直运行，慢慢的虽然有轻GC，但是新生区还是满了，就会把数据放到老年区中，当老年区也满了，就调用重GC，重GC把老年区没有的垃圾进行回收，空出空间，供轻GC回收后存活下来的数据生存。
>
> ​	
>
> 大多数的对象(99%)都是临时对象，在新生区中！
>
> **新生代占1/3，老年代占2/3**
>
> **Eden与Survivor的内存空间分配比值是8:1:1**

![image-20220529212042491](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212042491.png)

设置堆内存空间

```shell
-Xms1024m -Xmx1024m -XX:+PrintGCDetails
```

查看堆内存：

> 从下面可以得出，堆内存=新生区+老年区。
>
> 也就是说：**其实元空间只是个逻辑上的概念，在物理上并不存在**。

```java
// 堆内存最大值
long maxMemory = Runtime.getRuntime().maxMemory();
System.out.println("max->"+maxMemory+"   size："+((double)maxMemory/1024/1024));

// 初始化堆内存大小
long totalMemory = Runtime.getRuntime().totalMemory();
System.out.println("total->"+totalMemory+"   size："+((double)maxMemory/1024/1024));

// young 305664k 298.5m
// old   699392k 683m    young将近old的一半

打印信息：
Connected to the target VM, address: '127.0.0.1:59211', transport: 'socket'
max->1029177344   size：981.5
total->1029177344   size：981.5
Heap
 PSYoungGen      total 305664K, used 20971K [0x00000000eab00000, 0x0000000100000000, 0x0000000100000000)
  eden space 262144K, 8% used [0x00000000eab00000,0x00000000ebf7afb8,0x00000000fab00000)
  from space 43520K, 0% used [0x00000000fd580000,0x00000000fd580000,0x0000000100000000)
  to   space 43520K, 0% used [0x00000000fab00000,0x00000000fab00000,0x00000000fd580000)
 ParOldGen       total 699392K, used 0K [0x00000000c0000000, 0x00000000eab00000, 0x00000000eab00000)
  object space 699392K, 0% used [0x00000000c0000000,0x00000000c0000000,0x00000000eab00000)
 Metaspace       used 3189K, capacity 4620K, committed 4864K, reserved 1056768K
  class space    used 340K, capacity 392K, committed 512K, reserved 1048576K
```



### 13.永久区

> 永久区(Metaspace)：内存的永久保存区域，主要存放 Class 和 Meta（元数据）的信息,Class 在被加载的时候被 放入永久区域，它和和存放实例的区域不同,GC 不会在主程序运行期对永久区域进行清理。所
>
> 当然这个永久区是在逻辑上存在的，在物理上不存在。

一般情况下，不会出现在永久区就把堆内存占满的情况，但是如下情况会：

​	1：加载了大量的第三方jar包；

​	2：tomcat里面部署了大量的应用。

​	3：有非常多的动态代理生成的类。

以上情况有可能出现把整个堆内存占满的情况，导致OOM错误。

疑惑(问题)：

【为什么以上情况会导致OOM错误？怎么就占了永久区？加载第三方jar信息是把内容都放在了永久区吗？比如说springboot启动时加载的mybatis、aop等实例化的对象，感觉不像啊，这些应该在新生区里吧。】



### 14.堆内存调优

> 什么是堆内存调优？
>
> 我的理解，就是通过调整堆内存的参数，使得程序运行的更稳定，并且把硬件的性能恰到好处的分配好。
>
> 
>
> 要想调优，你首先得找到哪里需要调优吧，你得知道你的代码的情况吧，哪里慢，出了什么问题。
>
> 可以借助dump文件进行分析。
>
> 那么dump文件怎么产生？又用什么分析呢？
>
> 答案是：JProfiler
>
> 
>
> JVM调优参数：
>
> https://juejin.cn/post/6844904127512723470

#### 14-1.JProfiler安装与使用

> JProfiler是一个性能诊断工具，可以定位因线程控制、磁盘读写、数据库访问、网络I/O、垃圾收集等以上问题导致的性能问题。

下载与安装：

​	1：需要在idea插件上安装JProfiler，安装插件后记得重启。

​	2：需要安装JProfiler软件。就是一路next，然后打开输入License即可。

​	3：以上两步做完之后，在idea的tools->JProfiler配置，第二步安装位置下bin目录里的.exe启动文件

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212105503.png" alt="image-20220529212105503" style="zoom:30%;" />

​	以上，完成安装！

使用：

​	场景：你遇到了一个OutOfMemory的问题。

```java
public class OutOfMemory02 {
    byte[] bytes = new byte[1024*1024];

    public static void main(String[] args) {
        List list = new ArrayList<OutOfMemory02>();
        int count = 0;
        try {
            while (true) {
                list.add(new OutOfMemory02());
                count++;
            }
        }catch (Error e){
            System.out.println(count);
            e.printStackTrace();
        }
    }
}
```

​	报错信息：

```java
...
    2
java.lang.OutOfMemoryError: Java heap space
	at com.xiu.jvm01.OutOfMemory02.<init>(OutOfMemory02.java:8)
	at com.xiu.jvm01.OutOfMemory02.main(OutOfMemory02.java:16)
...
```

​	遇到了这个错误后，你想要知道问题出在了哪里，此时你设置JVM参数

```shell
-Xms1m -Xmx3m -XX:+HeapDumpOnOutOfMemory
```

​	然后重新执行，你发现：有文件生成了：

```java
java_pid15644.hprof
```

​	使用JProfiler软件打开这个文件：

![image-20220529212144567](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212144567.png)

​	从上面的这些地方去找有用的可以帮助你定位问题的信息。

### 15.GC

> GC：垃圾回收，将堆内存中没有用的东西给清除掉，释放空间。

常用方法：

- 引用计数法

  ![image-20220529212209315](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212209315.png)

  

- 复制算法

  > 当一次GC发生时，会把存活下来的对象放到to区中，同时，把from区中的对象也放到to区中。这时，from区就成了空的了，那么from区会变为to区，以前的to区会变成from区，此时的情况是，Eden区和to区是空的，并且from和to交换了位置，然后清除没用的对象。
  >
  > MajorGC 的耗时比较长，因为要扫描再回收。MajorGC 会产生内存碎片，为了减 少内存损耗，我们一般需要进行合并或者标记出来方便下次直接分配。

  ![image-20220529212232070](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212232070.png)

​	**一个对象如果能经受住15次GC依然活了下来，那么它就会被放到old区中。**

​	**当然，至于是多少次，也可以进行设置：-XX:PretenureSizeThreshold**

​	

​	复制算法的优点：

​		1：不会存在内存碎片，因为to永远是空的，每次都会把东西复制到from中。

​	复制算法的缺点：

​		2：总有一半的空间是空的！会比较浪费

​	总结来说，复制算法适合在**存活率低**的场景 (因为都存活的话Eden区的东西要全复制到to区中，to区的东西也要全复制到from区中，这很开销资源)。**新生区就是存活率低的区，新生区适合用复制算法。**

- 标记清除/压缩法

  - 标记清除算法

    > 字面意思，两步走：第一次扫描，进行标记，把用到的对象都标记；第二次扫描，进行清除，把没有被标记的对象都清除

    ![image-20220529212256673](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212256673.png)

    ​	优点：

    ​		解决了复制算法有一半空间是空的的问题

    ​	缺点：

    ​		但是会产生内存碎片。

  - 标记压缩算法

    > 标记压缩算法可以解决标记清除算法的缺点，它会去掉内存碎片。

    ![image-20220529212317957](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529212317957.png)

  

  有最优的算法吗？

  没有！没有最优秀的，只有最适合的！（不同场景使用不同算法）

  **分代收集算法：**	

  > 这种算法会根据对象存活周期的不同将内存划分为几块, 如 JVM 中的 新生代、老年代、永久代，这样就可以根据 各年代特点分别采用最适当的 GC 算法

  新生区：

  ​	存活率低

  ​	使用复制算法

  老年区：

  ​	内存大，存活率高。

  ​	标记清除算法+标记压缩算法。多次标记清除后有多个内存碎片，然后执行一次标记压缩算法，这个可以调整。

### 16.Java中的四种引用类型

- 强引用

  > 在 Java 中最常见的就是强引用，把一个对象赋给一个引用变量，这个引用变量就是一个强引 用。当一个对象被强引用变量引用时，它处于可达状态，它是不可能被垃圾回收机制回收的，即 使该对象以后永远都不会被用到 JVM 也不会回收。因此强引用是造成 Java 内存泄漏的主要原因之一。

- 软引用

  > 软引用需要用 SoftReference 类来实现，对于只有软引用的对象来说，**当系统内存足够时它 不会被回收，当系统内存空间不足时它会被回收**。软引用通常用在对内存敏感的程序中

- 弱引用

  > 弱引用需要用 WeakReference 类来实现，它比软引用的生存期更短，对于只有弱引用的对象 来说，只要垃圾回收机制一运行，不管 JVM 的内存空间是否足够，总会回收该对象占用的内存。

- 虚引用

  > 虚引用需要 PhantomReference 类来实现，它不能单独使用，必须和引用队列联合使用。虚 引用的主要作用是跟踪对象被垃圾回收的状态。

### 18.执行引擎



### 20.JMM

> Java Memory Model java内存模型
>
> 
>
> **JMM模型下的线程间通信：**
>
> 线程间通信必须要经过主内存。
>
> 如下，如果线程A与线程B之间要通信的话，必须要经历下面2个步骤：
>
> 1）线程A把本地内存A中更新过的共享变量刷新到主内存中去。
>
> 2）线程B到主内存中去读取线程A之前已更新过的共享变量。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521090241881.png" alt="image-20220521090241881" style="zoom:57%;" />\

关于主内存与工作内存之间的具体交互协议，即一个变量如何从主内存拷贝到工作内存、如何从工作内存同步到主内存之间的实现细节，Java内存模型定义了以下八种操作来完成：

- **lock（锁定）**：作用于主内存的变量，把一个变量标识为一条线程独占状态。
- **unlock（解锁）**：作用于主内存变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
- **read（读取）**：作用于主内存变量，把一个变量值从主内存传输到线程的工作内存中，以便随后的load动作使用
- **load（载入）**：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。
- **use（使用）**：作用于工作内存的变量，把工作内存中的一个变量值传递给执行引擎，每当虚拟机遇到一个需要使用变量的值的字节码指令时将会执行这个操作。
- **assign（赋值）**：作用于工作内存的变量，它把一个从执行引擎接收到的值赋值给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。
- **store（存储）**：作用于工作内存的变量，把工作内存中的一个变量的值传送到主内存中，以便随后的write的操作。
- **write（写入）**：作用于主内存的变量，它把store操作从工作内存中一个变量的值传送到主内存的变量中。

Java内存模型还规定了在执行上述八种基本操作时，必须满足如下规则：

- 如果要把一个变量从主内存中复制到工作内存，就需要按顺寻地执行read和load操作， 如果把变量从工作内存中同步回主内存中，就要按顺序地执行store和write操作。但Java内存模型只要求上述操作必须按顺序执行，而没有保证必须是连续执行。
- 不允许read和load、store和write操作之一单独出现
- 不允许一个线程丢弃它的最近assign的操作，即变量在工作内存中改变了之后必须同步到主内存中。
- 不允许一个线程无原因地（没有发生过任何assign操作）把数据从工作内存同步回主内存中。
- 一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量。即就是对一个变量实施use和store操作之前，必须先执行过了assign和load操作。
- **一个变量在同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。lock和unlock必须成对出现**
- 如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前需要重新执行load或assign操作初始化变量的值
- 如果一个变量事先没有被lock操作锁定，则不允许对它执行unlock操作；也不允许去unlock一个被其他线程锁定的变量。
- 对一个变量执行unlock操作之前，必须先把此变量同步到主内存中（执行store和write操作）

**导致的问题：**

-  缓存不一致问题
-  指令重排

#### 20-1.volatile

> 一个关键字。

作用：

- 保证可见性
- 不保证原子型
- 禁止指令重排

#### 7-1-1.保证可见性

![image-20220529213250819](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213250819.png)



```java
public class tvolital {
    // 不保证可见性
   //  static int num=0;
    // 保证可见性
    volatile static int num = 0;
    public static void main(String[] args) throws InterruptedException {
        new Thread(()->{
            while (num == 0){}
        }).start();

        TimeUnit.SECONDS.sleep(2);
        num ++;
        System.out.println(num);
    }
}
这种情况会发生可见性无法保证的问题。
main线程修改了num的值，但是thread不知道，它还卡在那里，程序不会结束。
加上volatile可以解决。
```

#### 7-1-2.无法保证原子性

> volatile无法保证原子性，
>
> 也就是说当一个对象被用的时候，可能会被其他对象(线程)操作

```java
public class Tvolatile2 {

    volatile static int num = 0;
    public static void add() {
        num++;
    }
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                for (int i1 = 0; i1 < 1000; i1++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2) { //默认启动main和gc，保证能执行完
            Thread.yield();
        }
        System.out.println(num);
    }
}
// num到不了2w
```

javap -c Tvolatile2.class可以发现，add方法有很多步，而只是一个加的操作。

![image-20220529213317418](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213317418.png)

**如何解决这个问题呢：**

- 加锁，lock、synchronized

- 原子AtomicInteger  java.util.concurrent.atomic包下

  ```java
  static AtomicInteger num = new AtomicInteger();
  public static void add() {
      num.incrementAndGet();
  }
  ```

#### 7-1-3.指令重排

> 什么是指令重排：你写的程序，计算机并不是按照你写的顺序去执行的。

源代码--> 编译器优化重排 --> 指令并行也可能重排 --> 内存系统也可能重排--> 执行

**处理器在指令重排的时候会考虑数据之间的依赖性。**

![image-20220529213343904](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213343904.png)



可能造成的结果是：xyab都是0

![image-20220529213404815](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213404815.png)



volatile可以禁止指令重排：

有个内存屏障，不允许顺序发生改变

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213420489.png" alt="image-20220529213420489" style="zoom:58%;" />



### 21.深入理解CAS

> CAS全程为compare-and-swap，它是一条cpu并发原语；**比较并交换**
>
> 他的功能是判断某个位置的值是否是预期值，如果是，更新预期值，这个更新操作是原子性的
>
> 简单来讲就是，传入的值和初始值进行比较，如果等于就把传入的值set进去，如果不等于就返回false。
>
> CAS：比较当前工作内存中的值和主内存中的值，如果这个值是期望的，那就执行操作！如果不是，那就一直循环！（自旋锁）

```java
public static void main(String[] args) {
    AtomicInteger atomicInteger = new AtomicInteger(2020);
    System.out.println(atomicInteger.compareAndSet(2020, 2022));
    System.out.println(atomicInteger);
}

打印：
true
2022
```

**Unsafe**

> java无法直接操作内存
>
> java可以调用c++，native
>
> c++可以操作内存
>
> 但是可以通过Unsafe去操作内存。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213459009.png" alt="image-20220529213459009" style="zoom:53%;" />

> AtomicInteger是怎么实现+1的呢？
>
> 通过Unsafe调用内存底层去实现。

![image-20220529213533219](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213533219.png)



**CAS：比较当前工作内存中的值和主内存中的值，如果这个值是期望的，那就执行操作！如果不是，那就一直循环！（自旋锁**）

**CAS缺点：**

​	1、循环会耗时

​	2、一次性只能保证一个共享变量的原子性

​	3、ABA问题。

#### 21-1、ABA问题

> 什么是ABA问题：狸猫换太子

左边的为线程1，右边的为线程2.

左边获取到主内存中A=1，想要用CAS把1更新为2，但还没有执行，就因为某些原因阻塞了；

但是此时，右边线程也开始执行，从主内存中得到A=1，进行两次操作，先通过cas把1更新为3，然后再通过cas把3还原为1，

此时线程1不阻塞了，获得了cpu资源，那么它去加载A，发现是1，然后就更新为2，虽然线程1执行成功了，但是线程1并不知道之前1变成3又变成1的过程。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213551803.png" alt="image-20220529213551803" style="zoom:53%;" />



**`ABA`问题带来的危害**：
 小明在提款机，提取了50元，因为提款机问题，有两个线程，同时把余额从100变为50
 线程1（提款机）：获取当前值100，期望更新为50，
 线程2（提款机）：获取当前值100，期望更新为50，
 线程1成功执行，线程2某种原因block了，这时，某人给小明汇款50
 线程3（默认）：获取当前值50，期望更新为100，
 这时候线程3成功执行，余额变为100，
 线程2从Block中恢复，获取到的也是100，compare之后，继续更新余额为50！！！
 此时可以看到，实际余额应该为100（100-50+50），但是实际上变为了50（100-50+50-50）这就是ABA问题带来的成功提交。

**解决方案：**

> AtomicStampedReference，加版本号。
>
> 在变量前面加上版本号，每次变量更新的时候变量的**版本号都`+1`**，即`A->B->A`就变成了`1A->2B->3A`。
>
> 确保值的版本号是我需要的。

![image-20220529213635669](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213635669.png)

​	![image-20220529213734460](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529213734460.png)



### 22.推荐阅读

> https://cloud.tencent.com/developer/article/1827486

# JUC并发编程

---

## 1、初识JUC

> java util concurrent

### 1-1.线程与进程

> 一个进程包含多个线程。
>
> 比如说：一个微信是个进程。里面包含：聊天框、朋友圈、朋友列表等等多个线程

#### 1-1-1.创建线程的方式

- Thread
- Runnable
- Callable

#### 1-1-2.并发与并行

​	并发：就是一个cpu在多个线程之间来回切换。天下武功，唯快不破，感觉像一起执行。

​	并行：就是多个cpu同时执行多个线程。一起执行。

```java
查看你的电脑有几个cpu
public static void main(String[] args) {
    System.out.println(Runtime.getRuntime().availableProcessors());
}
```

#### 1-1-3.wait与sleep的区别

|   wait   |      sleep       |
| :------: | :--------------: |
| object类 |     Thread类     |
|  释放锁  | 抱着锁睡，不释放 |

### 1-2.Lock与Synchronized

#### 1-2-1.Synchronized

> 同步，队列+锁的方式实现线程安全。
>
> 它是一个关键字，可以放到方法上，也可以放到代码块上(同步代码块)。

#### 1-2-2.Lock

> Lock是一个接口，java.util.concurrent.locks包下的，有三个实现类：
>
>  [ReentrantLock](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/ReentrantLock.html)：可重入锁。
>
>  [ReentrantReadWriteLock.ReadLock](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/ReentrantReadWriteLock.ReadLock.html)：读锁
>
>  [ReentrantReadWriteLock.WriteLock](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/ReentrantReadWriteLock.WriteLock.html)：写锁

##### 1-2-2-1.ReentrantLock

> 一个可重入互斥[`Lock`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/Lock.html)具有与使用`synchronized`方法和语句访问的隐式监视锁相同的基本行为和语义，但具有扩展功能。
>
> 建议的做法是*始终*立即跟随`lock`与`try`块的通话，最常见的是在之前/之后的建设，如：
>
> ```java
> class X {
>  private final ReentrantLock lock = new ReentrantLock(); 
>  // ... 
>  public void m() { 
>      lock.lock(); 
>  // block until condition holds 
>  	try {} finally { lock.unlock() }
> 	}
> } 
> ```
>
> 在创建的时候可以指定是公平锁还是非公平锁，默认是非公平锁。

公平锁：需要排队，一个一个来。传入true

非公平锁：可以插队。默认是它，为了公平，比如说一个3h，一个3s，3s的排在了后面，不能说要等3h后再去执行它吧。



案例：

​	多个(>=3)个线程，一个+1，一个-1，让他们始终保持为0.

```java
class Data{
    private int num;
    public synchronized void increment() throws InterruptedException {
        if(num != 0) this.wait();
        num++;
        System.out.println("当前线程："+Thread.currentThread().getName()+"值："+num);
        this.notify();
    }

    public synchronized void decrement() {
        if(num == 0)  this.wait(); 
        num--;
        System.out.println("当前线程："+Thread.currentThread().getName()+"值："+num);
        this.notify();
    }
}
```

> 以上代码会导致**虚假唤醒**，因为当两个线程同时进来时，if其实只判断了一次，而num++或num--却执行了两次，这种就导致num的值成为2或者更高。

![image-20220529165409033](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529165409033.png)

```java
class Data{
    private int num;
    public synchronized void increment() throws InterruptedException {
        while(num != 0) this.wait();
        num++;
        System.out.println("当前线程："+Thread.currentThread().getName()+"值："+num);
        this.notify();
    }

    public synchronized void decrement() {
        while(num == 0)  this.wait(); 
        num--;
        System.out.println("当前线程："+Thread.currentThread().getName()+"值："+num);
        this.notify();
    }
}
```







##### Lock与Synchronized区别【重要】：

| Lock                                     | Synchronized                         |
| ---------------------------------------- | ------------------------------------ |
| java内置类                               | 一个关键字                           |
| 可以判断是否获得了锁                     | 不能判断锁的状态                     |
| 需手动释放锁，不然会死锁                 | 无需手动，自动释放                   |
| 不会一直等待                             | 线程1获得锁后阻塞，线程2就会一直等待 |
| 可重入锁，可以判断锁，可自动设置公平与否 | 可重入锁，不可以中断，非公平         |
| 适合大量的同步代码                       | 适合少量的同步代码                   |

##### 1-2-2-2.JUC版的生产者消费者问题

> 使用Lock、Condition来实现。
>
> 可以做到精准调用某个线程。

![image-20220529165427374](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220529165427374.png)

```java
class Oprate {

    private int num;
    final Lock lock = new ReentrantLock();
    final Condition notFull = lock.newCondition();
    final Condition notEmpty = lock.newCondition();

    public void increNum() {
        lock.lock();
        try {
            while (num > 0) {
                notEmpty.await();
            }
            System.out.println("当前线程是："+Thread.currentThread().getName()+"num进行加法：" + num++ + ",加完之后的值是：" + num);
            notFull.signal();
        } catch (Exception e) {
            System.out.println("报错了");
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decreNum() {
        lock.lock();
        try {
            while (num <= 0) {
                notFull.await();
            }
            System.out.println("当前线程是："+Thread.currentThread().getName()+"num进行-法：" + num-- + ",-完之后的值是：" + num);
            notEmpty.signal();
        } catch (Exception e) {
            System.out.println("报错了");
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

##### 1-2-2-3.8锁现象

> 8种关于锁对象的情况分析。
>
> 如果只有一把锁，那就谁拿锁谁执行，
>
> 如果有多把锁，就随机，谁抢到cpu资源谁执行。
>
> 关键在于：确定锁是谁。
>
> 那锁对象是谁？
>
> ​	1：普通方法(没有static)，那锁就是调用方法的对象，这种就有可能有多把锁。
>
> ​	1：static方法，类class是对象，那class只有一个，所以就只有一把锁。

### 1-3.集合不安全问题

#### 1-3-1.ArrayList

> **java.util.ConcurrentModificationException 并发修改异常**
>
> 多线程中更容易出现该异常，当你在一个线程中对一数据集合进行遍历，正赶上另外一个线程对该数据集合进行增删操作。
>
> 因为ArrayList或者HashMap不是线程安全的。

```java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 50; i++) {
    new Thread(() ->{
        list.add(new Random().nextInt());
        System.out.println(list);
    }).start();
}

java.util.ConcurrentModificationException
```

解决方案一：

> Collections.synchronizedList(new ArrayList<String>);
>
> 这样线程就安全了。

```java
List<String> list = Collections.synchronizedList(new ArrayList<String>());
for (int i = 0; i < 50; i++) {
    new Thread(() ->{
        list.add(String.valueOf(new Random().nextInt()));
        System.out.println(list);
    }).start();
}
```

解决方案二：

> CopyOnWrite：写入时复制。COW：计算机程序设计领域一种优化策略。
>
> 线程是安全的。用了ReentrantLock

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
for (int i = 0; i < 50; i++) {
    new Thread(() ->{
        list.add(String.valueOf(new Random().nextInt()));
        System.out.println(list);
    }).start();
}
```

> Vector也是线程安全的，为什么不用它呢？
>
> 因为Vector用的是synchronized，这种效率要比ReetrantLock低！

#### 1-3-2.HashSet

> hashset的底层其实就是个hashmap，它用hashmap的key做为数据，从而保证了不可重复。
>
> 但是它也有线程安全问题，也会报出并发修改异常，concurrentModifyException
>
> 也就是说HashMap也会报并发修改异常。

```java
HashSet<String> set = new HashSet<>();
for (int i = 0; i < 50; i++) {
    new Thread(() ->{
        set.add(String.valueOf(new Random().nextInt()));
        System.out.println(set);
    }).start();
}
```

解决方案：

```java
Set<String> set = Collections.synchronizedSet(new HashSet<String>());
```

#### 1-3-3.HashMap

> HashMap也是线程不安全的，会出现并发修改异常。

解决方案：

```java
ConcurrentHashMap
```

### 1-4.Callable

> 第三种实现线程启动方式。
>
> Thread里只能放Runnable，
>
> FutureTask<Callable callable>,它的构造函数可以传Callable
>
> FutureTask实现了RunnableFuture，
>
> RunnableFuture继承了Runnable，
>
> 所以可以使用FutureTask

```java
FutureTask<String> futureTask = new FutureTask<>(new CallableCall());
futureTask.run();
new Thread(futureTask).start();
// 会有缓存，所以只执行一次call方法
new Thread(futureTask).start();
String s = futureTask.get();
// 可能会阻塞，如果你的call方法执行很久的话。所以一般把它放在最后面。
System.out.println(s);
```

```java
class CallableCall implements Callable<String>{

    @Override
    public String call() throws Exception {
        System.out.println("进来执行call方法");
        return "执行了call方法";
    }
}
```

### 1-5.常用辅助类

#### 1-5-1.CountDownLatch

> 用来计数的，减法
>
> 常用方法：
>
> ​	countDown() -1
>
> ​	await() 等待减到0

```java
// 能够做到让6线程全部执行完之后再去执行await方法后面的代码
public static void main(String[] args) throws InterruptedException {

    CountDownLatch countDownLatch = new CountDownLatch(6);
    for (int i = 0; i < 6; i++) {
        new Thread(()->{
            System.out.println(Thread.currentThread().getName()+"out");
            countDownLatch.countDown();
        },i+"").start();
    }
    countDownLatch.await();
    System.out.println("close door");
}
```

#### 1-5-2.CyclicBarrier

> 也是用来计数的，加法
>
> 常用方法：
>
> ​	await()

```java
// 等到集齐七颗龙珠才会召唤神龙
public static void main(String[] args) {
    CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
        System.out.println("集齐七颗龙珠，召唤神龙！");
    });

    for (int i = 0; i < 7; i++) {
        final int temp = i;// 在常量池里，所以匿名内部类才能调用到。
        new Thread(()->{
            try {
                System.out.println("集到了第"+temp+"颗龙珠。");
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

#### 1-5-3.Semaphore

> 限制线程数量的。
>
> **能够起到削峰的作用**
>
> **多个共享资源互斥的使用**
>
> 常用方法：
>
> ​	acquire()
>
> ​	release()

```java
public static void main(String[] args) {
    // 最多可以让6个线程执行，剩下的要等待，等待有某个线程释放了之后就可以抢到资源执行逻辑了。
    // 先有6个线程抢到，可能6释放了，立刻被第七个线程抢到资源执行，也就是说，并不是让6个资源全部释放才进行下一轮的。
    Semaphore semaphore = new Semaphore(6);
    for (int i = 0; i < 12; i++) {
        new Thread(()->{
            try {
                // 获得资源
                semaphore.acquire();
                System.out.println(Thread.currentThread().getName()+"抢到了执行资源");
                System.out.println(Thread.currentThread().getName()+"执行我的逻辑");
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                // 释放资源
                semaphore.release();
                System.out.println(Thread.currentThread().getName()+"释放了执行资源");
            }
        },i+"").start();
    }
}
```

### 1-6.读写锁 ReadWriteLock

> 读可以多个线程同时，不用执行完再怎么样。
>
> 写必须要完全处理完下一个线程才能进入。
>
> 读锁（共享锁）：readLock
>
> 写锁（独占锁）：writeLock

```java
package com.xiu.readwrite;

import java.util.HashMap;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReadWriteLockDemo {
    public static void main(String[] args) {
//        MyCache myCache = new MyCache();
        LockMyCache myCache = new LockMyCache();
        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.write(String.valueOf(temp),String.valueOf(temp));
            },i+"").start();
        }

        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.read(String.valueOf(temp));
            },i+"").start();
        }
    }
}

class MyCache{
    private volatile HashMap<String,Object> cache = new HashMap();
    // 这种锁可能会导致，写的过程被读给插入。这种无所谓，不要被别的写给插入就行。
    private ReentrantLock lock = new ReentrantLock();

    public void write(String key,String value) {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName()+"开始写入。。");
            cache.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写入完成。。");
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }

    }

    public void read(String key) {
        System.out.println(Thread.currentThread().getName()+"开始读取");
        cache.get(key);
        System.out.println(Thread.currentThread().getName()+"读取完成");
    }
}


class LockMyCache{
    private volatile HashMap<String,Object> cache = new HashMap();
    // 这种锁可能会导致，写的过程被读给插入。
    ReentrantReadWriteLock.WriteLock writeLock = new ReentrantReadWriteLock().writeLock();
    ReentrantReadWriteLock.ReadLock readLock = new ReentrantReadWriteLock().readLock();

    public void write(String key,String value) {
        writeLock.lock();
        try {
            System.out.println(Thread.currentThread().getName()+"开始写入。。");
            cache.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写入完成。。");
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            writeLock.unlock();
        }
    }
    public void read(String key) {
        readLock.lock();
        System.out.println(Thread.currentThread().getName()+"开始读取");
        cache.get(key);
        System.out.println(Thread.currentThread().getName()+"读取完成");
        readLock.unlock();
    }
}
```

### 1-7.阻塞队列

#### 1-7-1.collection家族

> 队列，FIFO(First In First Out)，先进先出

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520153846617.png" alt="image-20220520153846617" style="zoom: 67%;" />

#### 1-7-2.四组API（重要）

> 以ArrayBlockingQueue举例
>
> 消息队列(MQ)底层与它相关。

![image-20220520161150371](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520161150371.png)



![image-20220520161443819](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520161443819.png)



![image-20220520161325100](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520161325100.png)



![](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520161359816.png)



![image-20220520161415162](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520161415162.png)

#### 1-7-3.同步队列

> SynchronousdQueue
>
> 其中每个插入操作必须等待另一个线程相应的删除操作，反之亦然。 同步队列没有任何内部容量，甚至没有一个容量。
>
> 常用方法：
>
> ​	put()
>
> ​	take()
>
> **一个线程put，必须等待另外一个线程take取走它才能再往里面put。**

## 2、线程池

> 池化技术：认为创建一个池子，需要的话从池里取，用完之后还回来。
>
> **核心技术：3大方法，7种  ，4种**

线程池的优点：	

​	1：避免资源过度消耗。创建和销毁很耗费资源

​	2：对线程进行集中管理

​	3：

### 2-1.3大方法

> ​	// 创建一个线程，池里只有一个
> ​    ExecutorService executorService = Executors.newSingleThreadExecutor();
> ​    // 指定池里有几个
> ​    ExecutorService executorService1 = Executors.newFixedThreadPool(5);
> ​    // 吃里有几个根据你的需求来，可伸缩的。
> ​    ExecutorService executorService2 = Executors.newCachedThreadPool();

```java
public static void main(String[] args) {
    // 创建一个线程，池里只有一个
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    // 指定池里有几个
    ExecutorService executorService1 = Executors.newFixedThreadPool(5);
    // 吃里有几个根据你的需求来，可伸缩的。
    ExecutorService executorService2 = Executors.newCachedThreadPool();

    try {
        for (int i = 0; i < 50; i++) {
            executorService2.execute(()->{
                System.out.println(Thread.currentThread().getName()+"执行了。");
            });
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        executorService2.shutdown();
    }
}

```

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520165859264.png" alt="image-20220520165859264" style="zoom:57%;" />

### 2-2.7大参数

> 自定以线程池，传入的参数

```java
public static void main(String[] args) {
    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
        3,     // 核心池大小，一直活着的
        5,     // 最大池大小
        30,   // 超过这个时间，非核心池以外的就会释放
        TimeUnit.SECONDS,  // 上面参数的单位
        new LinkedBlockingDeque<>(3),// 阻塞队列，如果超过3个了，多出来的3个可以再这里等待
        Executors.defaultThreadFactory(),    //默认工厂，创建线程用的，一般不变
        new ThreadPoolExecutor.AbortPolicy() // 队列满了后的拒绝策略
    );
}
```

### 2-3.4种拒绝类型

> 如果线程池种的队列满了，怎么拒绝？

![image-20220520174405294](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520174405294.png)

### 2-4.如何设置最大线程数(maxiumPoolSize)

> 两种方案：
>
> ​	CPU密集型
>
> ​	IO密集型

- CPU密集型

  > 就是判断服务器总共是多少核的。
  >
  > 按照这个数去配。

  ```java
  System.out.println(Runtime.getRuntime().availableProcessors());
  ```

- IO密集型

  > 去看项目种有多少比较耗IO的，
  >
  > 然后一般是它2倍。
  >
  > 比如说：
  >
  >  一个大型项目有15个耗IO的程序，
  >
  > 那我们线程池的最大线程数就设置为：15 * 2 = 30

## 3、四大函数式接口

> 函数式接口：只有一个方法的接口

### 3-1.Function 函数型接口

> 函数型接口
>
> 两个参数，一个是传入类型，一个是返回类型

```java
public static void main(String[] args) {
    Function<String, Integer> function = new Function<String, Integer>() {
        @Override
        public Integer apply(String s) {
            if (s.equals("a")) {
                return 1;
            } else if (s.equals("b")) {
                return 2;
            } else {
                return -1;
            }

        }
    };
	// 简写
    Function function1 = (str)->{return str;};
    System.out.println(function.apply("a"));
    System.out.println(function1.apply("bbbbb"));
}
```



### 3-2.Predicate 断定型接口

> 断定式接口，返回true或false
>
> 传入一个参数。

```java
public static void main(String[] args) {
    Predicate<String> predicate = new Predicate<String>() {

        @Override
        public boolean test(String s) {
            if (s.equals("success")) {
                return true;
            } else {
                return false;
            }
        }
    };

    // lambda简写
    Predicate<String> success = (str) -> {
        if (str.equals("success")) {
            return true;
        } else {
            return false;
        }
    };
    System.out.println(predicate.test("fail"));

}
```

### 3-3.Consumer 消费型接口

> 消费型接口，
>
> 只有入参，没有出参。

```java
public static void main(String[] args) {
    Consumer<String> consumer = new Consumer<String>() {
        @Override
        public void accept(String s) {
            System.out.println("s->" + s);
        }
    };
	
    Consumer<String> consumer = () -> {System.out.println("s->" + s);};
    consumer.accept("ssssss");
}
```



### 3-4.Supplier 提供型接口

> 提供型接口
>
> 没有入参，只有出参

```java
public static void main(String[] args) {
    Supplier<String> supplier = new Supplier<String>() {
        @Override
        public String get() {
            return "bbbbb";
        }
    };
	Supplier<String> supplier = () -> {return "bbb";}
    System.out.println(supplier.get());
}
```

## 4、Stream

> 流式计算

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520190010385.png" alt="image-20220520190010385" style="zoom:67%;" />



## 5、ForkJoin

> 把大人物拆成小任务，并行执行，可以提高效率。
>
> **这种适用于大数据量。**

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220520222310483.png" alt="image-20220520222310483" style="zoom: 50%;" />



特点：

​	工作窃取。

就是当A的任务执行完后，B的任务还没有执行完，那A就去找B的任务去做。

> RecursiveTask
>
> 这个还需要后续重新学习。此时着急面试，顾不上这个了。

## 6、异步调用

> Future

## 7、JMM

> java memory model java内存模型
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

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521121554615.png" alt="image-20220521121554615" style="zoom:67%;" />

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

### 7-1.volatile

> 一个关键字。

作用：

- 保证可见性
- 不保证原子型
- 禁止指令重排

#### 7-1-1.保证可见性

![image-20220521092234649](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521092234649.png)



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

![image-20220521094929841](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521094929841.png)

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

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521101712426.png" alt="image-20220521101712426" style="zoom:97%;" />



可能造成的结果是：xyab都是0

![image-20220521101816787](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521101816787.png)



volatile可以禁止指令重排：

有个内存屏障，不允许顺序发生改变

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521102308432.png" alt="image-20220521102308432" style="zoom:67%;" />

## 8、彻底理解单例模式

### 8-1.饿汉式

> 上来就创建

### 8-2.懒汉式

> 需要的时候才创建
>
> jad反编译工具

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521110945356.png" alt="image-20220521110945356" style="zoom:67%;" />



## 9、深入理解CAS

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

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521115305313.png" alt="image-20220521115305313" style="zoom:57%;" />

> AtomicInteger是怎么实现+1的呢？
>
> 通过Unsafe调用内存底层去实现。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521115514732.png" alt="image-20220521115514732" style="zoom:67%;" />



**CAS：比较当前工作内存中的值和主内存中的值，如果这个值是期望的，那就执行操作！如果不是，那就一直循环！（自旋锁**）

**CAS缺点：**

​	1、循环会耗时

​	2、一次性只能保证一个共享变量的原子性

​	3、ABA问题。

## 10、ABA问题

> 什么是ABA问题：狸猫换太子

左边的为线程1，右边的为线程2.

左边获取到主内存中A=1，想要用CAS把1更新为2，但还没有执行，就因为某些原因阻塞了；

但是此时，右边线程也开始执行，从主内存中得到A=1，进行两次操作，先通过cas把1更新为3，然后再通过cas把3还原为1，

此时线程1不阻塞了，获得了cpu资源，那么它去加载A，发现是1，然后就更新为2，虽然线程1执行成功了，但是线程1并不知道之前1变成3又变成1的过程。

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521150235480.png" alt="image-20220521150235480" style="zoom:57%;" />



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

![image-20220521152326113](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521152326113.png)

​	![image-20220521152306018](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521152306018.png)

## 11、各种锁总结

### 11-1.公平锁与非公平锁



### 11-2.可重入锁

> java中的都是颗重入锁。synchronized和lock等都是可重入锁。
>
> **lock锁必须配对，lock了一定要unlock，成对出现。**
>
> **所以可重入锁就是有了入户门的钥匙，里面房间门也能进去的意思。**

请问下面的打印结果？

<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521153206707.png" alt="image-20220521153206707" style="zoom:67%;" />

> 结果：
>
> Asms 
>
> Acall
>
> Bsms
>
> Bcall

> A线程拿到锁之后，会把两个都执行完才释放锁，B线程才能去获取锁。
>
> 所以可重入锁就是有了入户门的钥匙，里面房间门也能进去的意思。

### 11-3.自旋锁

> 不满足条件就无限制等待。
>
> 底层是CAS

```java
import java.util.concurrent.atomic.AtomicReference;

public class MySpinLock {

    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"lock");
        while(!atomicReference.compareAndSet(null,thread)){}
    }

    public void myUnlock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"unlock");
        atomicReference.compareAndSet(thread,null);
    }
}
```

```java
import java.util.concurrent.TimeUnit;

public class SpinLockTest {
    public static void main(String[] args) {
        MySpinLock mySpinLock = new MySpinLock();
        new Thread(()->{
            mySpinLock.myLock();
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            mySpinLock.myUnlock();
        },"A").start();

        new Thread(()->{
            mySpinLock.myLock();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            mySpinLock.myUnlock();
        },"B").start();
    }
}
```

### 11-4.乐观锁与悲观锁





## 12、死锁问题如何排查

> 死锁：互相拿着对方想要的锁，线程进入到等待中了。 

```java
public class DeadLockDemo {

    public static void main(String[] args) {

        DeadLock deadLock = new DeadLock("lock1", "lock2");
        new Thread(() -> {
            deadLock.wannaLock1();
        }).start();
        new Thread(() -> {
            deadLock.wannaLock2();
        }).start();
    }

}
@AllArgsConstructor
@NoArgsConstructor
class DeadLock{
    private String lock1;

    private String lock2;

    public void wannaLock2() {
        synchronized (lock1) {
            System.out.println(Thread.currentThread().getName()+"想要"+lock2);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lock2) {
                System.out.println(Thread.currentThread().getName()+"拿到了lock2");
            }
        }
    }

    public void wannaLock1() {
        synchronized (lock2){
            System.out.println(Thread.currentThread().getName()+"想要"+lock1);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lock1) {
                System.out.println(Thread.currentThread().getName()+"拿到了lock1");
            }
        }
    }
}
```

> 上面是一个死锁，但是呢，代码没有报错，只是卡死，如何分析到这个问题呢？

分析方案：

-  **jps -l 查看java进程号**

  <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521162508242.png" alt="image-20220521162508242" style="zoom:67%;" />

-  **jstack 进程号 分析问题**

  <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521162534063.png" alt="image-20220521162534063" style="zoom:62%;" />

  <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521162640541.png" alt="image-20220521162640541" style="zoom:57%;" />


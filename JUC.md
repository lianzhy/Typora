# JUC并发编程

> [狂神说：https://space.bilibili.com/95256449](https://space.bilibili.com/95256449)

## 1. 什么是JUC

JUC就是java.util.concurrent下面的类包，专门用于多线程的开发。

**源码 + 官方文档 面试高频问！**![在这里插入图片描述](https://img-blog.csdnimg.cn/20210129235524770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

java.util 工具包

- 业务：无法通过普通的线程代码 Thread实现。
- Runnable 没有返回值、效率相比于Callable相对较低！
- 企业开发中Callable 使用较多
    Callable ：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210129235638790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

锁：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210129235646413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

## 2、线程和进程

> 进程是操作系统中的应用程序、是资源分配的基本单位，线程是用来执行具体的任务和功能，是CPU调度和分派的最小单位
>
> 一个进程往往可以包含多个线程，至少包含一个

### 1）进程

**一个程序，QQ.EXE Music.EXE；程序的集合**
一个进程可以包含多个线程，至少包含一个线程！
Java默认有几个线程？2个线程！ main线程、GC线程

### 2）线程

**开了一个进程Typora，写字，等待几分钟会进行自动保存(线程负责的)**
对于Java而言：Thread、Runable、Callable进行开启线程的。

**提问？JAVA真的可以开启线程吗？ 开不了的！**

Java是没有权限去开启线程、操作硬件的，这是一个native的一个本地方法，它调用的底层的C++代码。

```java
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	//这是一个C++底层，Java是没有权限操作底层硬件的
    private native void start0();
```

### 3）并发

多线程操作同一个资源。

- CPU 只有一核，模拟出来多条线程，天下武功，唯快不破。那么我们就可以使用CPU快速交替，来模拟多线程。
- 并发编程的本质：**充分利用CPU的资源！**

### 4）并行

**并行：** 多个人一起行走

- CPU多核，多个线程可以同时执行。 我们可以使用线程池！

**获取cpu的核数**

```java
public class Test1 {
    public static void main(String[] args) {
        //获取cpu的核数
        System.out.println(Runtime.getRuntime().availableProcessors());
    }
}
```

### 5）线程的状态

```java
public enum State {	
	//新生
    NEW,

	//运行
    RUNNABLE,

	//阻塞
    BLOCKED,

	//等待
    WAITING,

	//超时等待
    TIMED_WAITING,

	//终止
    TERMINATED;
}
```

### 6）wait/sleep

**1、来自不同的类**

wait => Object
sleep => Thread
一般情况企业中使用休眠是：

```java
TimeUnit.DAYS.sleep(1); //休眠1天
TimeUnit.SECONDS.sleep(1); //休眠1s
```

**2、关于锁的释放**

wait 会释放锁；

sleep睡觉了，不会释放锁；

**3、使用的范围是不同的**

wait 必须在同步代码块中；

sleep 可以在任何地方睡；

**4、是否需要捕获异常**

wait是也需要捕获异常；（网上非常多的代码说不用抛出异常，应该是没去看源码和尝试吧，下面附图，其实文章上一张源码的图也显示需要抛出异常），notify和notifyAll不需要捕获异常。）中断

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210131150856789.png)

sleep必须要捕获异常；

## 3.Lock

### 1）传统的 synchronized

```java
package com.marchsoft.juctest;

import lombok.Synchronized;

/**
 * Description：synchronized
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 21:36
 **/

public class Demo01 {
    public static void main(String[] args) {
        final Ticket ticket = new Ticket();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();
    }
}
// 资源类 OOP 属性、方法
class Ticket {
    private int number = 30;

    //卖票的方式
    public synchronized void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "卖出了第" + (number--) + "张票剩余" + number + "张票");
        }
    }
}
```

### 2）Lock

![image-20200810221525974](https://img-service.csdnimg.cn/img_convert/77ca36442c3ced0659a5af7f06909bd2.png)

![image-20200810221731649](https://img-service.csdnimg.cn/img_convert/d0070945de646cdc612f11c99dd5fb7d.png)

**公平锁：** 十分公平，必须先来后到~；

**非公平锁：** 十分不公平，可以插队；**(默认为非公平锁)**

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 22:05
 **/

public class LockDemo {
    public static void main(String[] args) {
        final Ticket2 ticket = new Ticket2();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "C").start();
    }
}
//lock三部曲
//1、    Lock lock=new ReentrantLock();
//2、    lock.lock() 加锁
//3、    finally=> 解锁：lock.unlock();
class Ticket2 {
    private int number = 30;
	
    // 创建锁
    Lock lock = new ReentrantLock();
    //卖票的方式
    public synchronized void sale() {
        lock.lock(); // 开启锁
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "卖出了第" + (number--) + "张票剩余" + number + "张票");
            }
        }finally {
            lock.unlock(); // 关闭锁
        }

    }
}
```

### 3）Synchronized 与Lock 的区别

- Synchronized **内置的Java关键字**，Lock是一个**Java类**
- Synchronized 无法判断获取**锁的状态**，Lock可以判断
- Synchronized 会**自动释放锁**，lock必须要手动加锁和手动释放锁！可能会遇到死锁
- Synchronized 线程1(获得锁->阻塞)、线程2(等待)；lock就不一定会一直等待下去，lock会有一个**trylock**去尝试获取锁，不会造成长久的等待。
- Synchronized **是可重入锁**，不可以中断的，**非公平的**；Lock，可重入的，可以判断锁，可以自己设置公平锁和非公平锁；
- Synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步代码；

> 锁是什么，如何判断锁的是谁！

## 4、生产者和消费者问题

**面试：单例模式、排序算法、生产者和消费者、死锁问题**

### 1) Synchronized 版

```JAVA
package com.zzy.pc;

/**
 * 线程之间的通信问题：生产者和消费者问题！ 等待唤醒，通知唤醒
 * 线程交替执行 A B 操作同一个变量 num = 0
 * A num+1
 * B num-1
 */
//顺序：判断待->业务->通知
public class A {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }

        }, "A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            
        },"B").start();
    }
}

class Data {
    private int number = 0;

    //+1
    public synchronized void increment() throws InterruptedException {
        if (number != 0) {
            //等待
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        // 通知其他线程，我-1完毕了
        this.notifyAll();
    }

    //-1
    public synchronized void decrement() throws InterruptedException {
        if (number == 0) {
            //等待
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        // 通知其他线程，我-1完毕了
        this.notifyAll();
    }
}
```

### 2）存在问题（虚假唤醒）

**问题，如果有四个线程**，会出现虚假唤醒

![image-20200810224629273](https://img-service.csdnimg.cn/img_convert/1af314564aae84ed73a7ee90525f9d91.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130202723375.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

解决方式 ，**if 改为while即可，防止虚假唤醒**

> 结论：就是用if判断的话，唤醒后线程会从wait之后的代码开始运行，但是不会重新判断if条件，直接继续运行if代码块之后的代码，而如果使用while的话，也会从wait之后的代码运行，但是唤醒后会重新判断循环条件，如果不成立再执行while代码块之后的代码块，成立的话继续wait。
> 这也就是为什么用while而不用if的原因了，因为线程被唤醒后，执行开始的地方是wait之后

```java
package com.marchsoft.juctest;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 22:33
 **/

public class ConsumeAndProduct {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();
    }
}

class Data {
    private int num = 0;

    // +1
    public synchronized void increment() throws InterruptedException {
        // 判断等待
        while (num != 0) {
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 +1 执行完毕
        this.notifyAll();
    }

    // -1
    public synchronized void decrement() throws InterruptedException {
        // 判断等待
        while (num == 0) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 -1 执行完毕
        this.notifyAll();
    }
}
```

### 3）Lock版

![image-20200811094721678](https://img-service.csdnimg.cn/img_convert/dad7044c4b8b46648084823841cb6781.png)

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/11 9:48
 **/

public class LockCAP {
    public static void main(String[] args) {
        Data2 data = new Data2();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {

                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();
    }
}

class Data2 {
    private int num = 0;
    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();
    // +1
    public  void increment() throws InterruptedException {
        lock.lock();
        try {
            // 判断等待
            while (num != 0) {
                condition.await();
            }
            num++;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 通知其他线程 +1 执行完毕
            condition.signalAll();
        }finally {
            lock.unlock();
        }

    }

    // -1
    public  void decrement() throws InterruptedException {
        lock.lock();
        try {
            // 判断等待
            while (num == 0) {
                condition.await();
            }
            num--;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 通知其他线程 +1 执行完毕
            condition.signalAll();
        }finally {
            lock.unlock();
        }

    }
}
```

### 4）Condition的优势

精准的通知和唤醒的线程！

**如果我们要指定通知的下一个进行顺序怎么办呢？ 我们可以使用Condition来指定通知进程~**

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 * A 执行完 调用B
 * B 执行完 调用C
 * C 执行完 调用A
 *
 * @author jiaoqianjin
 * Date: 2020/8/11 9:58
 **/

public class ConditionDemo {
    public static void main(String[] args) {
        Data3 data3 = new Data3();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printA();
            }
        },"A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printB();
            }
        },"B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printC();
            }
        },"C").start();
    }

}
class Data3 {
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    private int num = 1; // 1A 2B 3C

    public void printA() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 1) {
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> AAAA" );
            num = 2;
            condition2.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void printB() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 2) {
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> BBBB" );
            num = 3;
            condition3.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void printC() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 3) {
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> CCCC" );
            num = 1;
            condition1.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
/*
A==> AAAA
B==> BBBB
C==> CCCC
A==> AAAA
B==> BBBB
C==> CCCC
...
*/
```

## 5. 8锁现象

如何判断锁的是谁！锁到底锁的是谁？

**锁会锁住：对象、Class**

深刻理解我们的锁

**问题1**

两个同步方法，先执行发短信还是打电话

```java
public class dome01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() -> { phone.sendMs(); }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() {
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

输出结果为

发短信

打电话

**为什么？ 如果你认为是顺序在前？ 这个答案是错误的！**

**问题2：**

**我们再来看：我们让发短信 延迟4s**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone = new Phone();

        new Thread(() -> {
            try {
                phone.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

TimeUnit

现在结果是什么呢？

结果：**还是先发短信，然后再打电话！**

**why？**

> 原因：并不是顺序执行，而是synchronized 锁住的对象是方法的调用！对于两个方法用的是同一个锁，谁先拿到谁先执行，另外一个等待

**问题三**

加一个普通方法

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone = new Phone();

        new Thread(() -> {
            try {
                phone.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.hello(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果为

hello

发短信

> 原因：hello是一个普通方法，不受synchronized锁的影响，不用等待锁的释放

**问题四**

**如果我们使用的是两个对象，一个调用发短信，一个调用打电话，那么整个顺序是怎么样的呢？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone2.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：两个对象两把锁，不会出现等待的情况，发短信睡了4s,所以先执行打电话

**问题五、六**

**如果我们把synchronized的方法加上static变成静态方法！那么顺序又是怎么样的呢？**

（1）我们先来使用一个对象调用两个方法！

答案是：**先发短信,后打电话**

（2）如果我们使用两个对象调用两个方法！

答案是：**还是先发短信，后打电话**

原因是什么呢？ 为什么加了static就始终前面一个对象先执行呢！为什么后面会等待呢？
原因是：**对于static静态方法来说，对于整个类Class来说只有一份，对于不同的对象使用的是同一份方法，相当于这个方法是属于这个类的，如果静态static方法使用synchronized锁定，那么这个synchronized锁会锁住整个对象！不管多少个对象，对于静态的锁都只有一把锁，谁先拿到这个锁就先执行，其他的进程都需要等待！**

**问题七**

**如果我们使用一个静态同步方法、一个同步方法、一个对象调用顺序是什么？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
//        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone1.call(); }).start();
    }
}

class Phone {
    public static synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：因为一个锁的是Class类的模板，一个锁的是对象的调用者。所以不存在等待，直接运行。

**问题八**

**如果我们使用一个静态同步方法、一个同步方法、两个对象调用顺序是什么？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone2.call(); }).start();
    }
}

class Phone {
    public static synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：两把锁锁的不是同一个东西

**小结**

- 锁对象：new this 具体的一个手机
- 锁class：static Class 唯一的一个模板

## 6. 集合不安全

### 1）List 不安全

```java
//java.util.ConcurrentModificationException 并发修改异常！
public class ListTest {
    public static void main(String[] args) {

        List<Object> arrayList = new ArrayList<>();

        for(int i=1;i<=10;i++){
            new Thread(()->{
                arrayList.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(arrayList);
            },String.valueOf(i)).start();
        }

    }
}
```

会导致java.util.ConcurrentModificationException 并发修改异常

**ArrayList 在并发情况下是不安全的**

解决方案：

```java
public class ListTest {
    public static void main(String[] args) {
        /**
         * 解决方案
         * 1. List<String> list = new Vector<>();
         * 2. List<String> list = Collections.synchronizedList(new ArrayList<>());
         * 3. List<String> list = new CopyOnWriteArrayList<>();
         */
        List<String> list = new CopyOnWriteArrayList<>();
        

        for (int i = 1; i <=10; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```

CopyOnWriteArrayList：写入时复制！ **COW 计算机程序设计领域的一种优化策略**

核心思想是，如果有多个调用者（Callers）同时要求相同的资源（如内存或者是磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者视图修改资源内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是透明的（transparently）。此做法主要的优点是如果调用者没有修改资源，就不会有副本（private copy）被创建，因此多个调用者只是读取操作时可以共享同一份资源。

读的时候不需要加锁，如果读的时候有多个线程正在向CopyOnWriteArrayList添加数据，读还是会读到旧的数据，因为写的时候不会锁住旧的CopyOnWriteArrayList。

**多个线程调用的时候，list，读取的时候，固定的，写入（存在覆盖操作）；在写入的时候避免覆盖，造成数据错乱的问题；**

> **CopyOnWriteArrayList**比**Vector**厉害在哪里？

**Vector**底层是使用**synchronized**关键字来实现的：效率特别低下。

![image-20200811144549151](https://img-service.csdnimg.cn/img_convert/ec0193ec03b4e67874350644b0f7b6ec.png)

**CopyOnWriteArrayList**使用的是Lock锁，效率会更加高效！

![image-20200811144447781](https://img-service.csdnimg.cn/img_convert/65d81d2d9f14913e79f91dcf23f8eea7.png)

### 2）set 不安全

**Set和List同理可得:** 多线程情况下，普通的Set集合是线程不安全的；

解决方案还是两种：

- 使用Collections工具类的**synchronized**包装的Set类
- 使用CopyOnWriteArraySet 写入复制的**JUC**解决方案

```java
public class SetTest {
    public static void main(String[] args) {
        /**
         * 1. Set<String> set = Collections.synchronizedSet(new HashSet<>());
         * 2. Set<String> set = new CopyOnWriteArraySet<>();
         */
//        Set<String> set = new HashSet<>();
        Set<String> set = new CopyOnWriteArraySet<>();

        for (int i = 1; i <= 30; i++) {
            new Thread(() -> {
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}
```

**HashSet底层是什么？**

hashSet底层就是一个**HashMap**；

![image-20200811150415187](https://img-service.csdnimg.cn/img_convert/35c7c667ffc7a2e2bb001a147d30b5f9.png)

### 3）Map不安全

```java、
//map 是这样用的吗？  不是，工作中不使用这个
//默认等价什么？ new HashMap<>(16,0.75);
Map<String, String> map = new HashMap<>();
//加载因子、初始化容量
```

默认**加载因子是0.75**,默认的**初始容量是16**

![image-20200811150700927](https://img-service.csdnimg.cn/img_convert/a35fac9739ec6cb78381f0a82c1a8927.png)

同样的HashMap基础类也存在**并发修改异常**！

```java
public class MapTest {
    public static void main(String[] args) {
        //map 是这样用的吗？  不是，工作中不使用这个
        //默认等价什么？ new HashMap<>(16,0.75);
        /**
         * 解决方案
         * 1. Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
         *  Map<String, String> map = new ConcurrentHashMap<>();
         */
        Map<String, String> map = new ConcurrentHashMap<>();
        //加载因子、初始化容量
        for (int i = 1; i < 100; i++) {
            new Thread(()->{
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0,5));
                System.out.println(map);
            },String.valueOf(i)).start();
        }
    }
}
```

**TODO:研究ConcurrentHashMap底层原理：**

## 7. Callable（简单）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210131173127786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**1、可以有返回值；
2、可以抛出异常；
3、方法不同，run()/call()**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130203506125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130203549739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130203553754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.callable;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * 1、探究原理
 * 2、觉自己会用
 */
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //new Thread(new Runnable()).start();
        //new Thread(new FutureTask<V>()).start();
        //new Thread(new FutureTask<V>( Callable )).start();

        //new Thread().start();// 怎么启动Callable

        MyThread thread = new MyThread();
        FutureTask futureTask = new FutureTask(thread);//适配类

        new Thread(futureTask,"A").start();
        new Thread(futureTask,"B").start();// 结果会被缓存，效率高，结果只打印一次

        //获取Callable的返回结果
        String o = (String) futureTask.get();//这个get 方法可能会产生阻塞！把他放到最后 或者使用异步通信来处理！

        System.out.println(o);
    }
}
class MyThread implements Callable<String> {

    @Override
    public String call() throws Exception {
        System.out.println("jjjj");
        // 耗时的操作
        return "hello";
    }
}
```

细节：
1、有缓存
2、结果可能需要等待，会阻塞！

## 8、常用的辅助类(高并发必会)

加法计数器，减法计数器和信号量

### 1）CountDownLatch

减法计数器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130203635895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**主要方法：**

- countDown 减一操作；
- await 等待计数器归零

await 等待计数器归零，就唤醒，再继续向下运行

### 2）CyclickBarrier

加法计数器

![image-20200811202603352](https://img-service.csdnimg.cn/img_convert/dee6ef3d75096d41547b6729fcce3037.png)

加法计数器

```java
package com.zzy.add;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * @author Zhao
 * @DATE 2021/1/31 - 17:54
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        /**
         * 集齐7颗龙珠召唤神龙
         */
        // 召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("召唤神龙。。。");
        });

        for (int i = 1; i <= 7; i++) {
            // lambda不能操作到 i
            int temp = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"收集第"+temp+"颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

CyclicBarrier 与 CountDownLatch 区别

- CountDownLatch 是一次性的，CyclicBarrier 是可循环利用的
- CountDownLatch 参与的线程的职责是不一样的，有的在倒计时，有的在等待倒计时结束。CyclicBarrier 参与的线程职责是一样的。

### 3）Semaphore（**信号量**）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130203800381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

抢车位！
6车—3个停车位置

```java
package com.zzy.add;

import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * @author Zhao
 * @DATE 2021/1/31 - 18:05
 */
public class SemaphoreDemo {
    public static void main(String[] args) {

        // 线程数量：停车位! 用来限流！
        Semaphore semaphore = new Semaphore(3);
        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                //acquire() 得到
                //release() 释放
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到车位！");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"离开车位！");

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }

            }, String.valueOf(i)).start();

        }
    }
}
```

**原理：**

- `semaphore.acquire();` **获得，假设如果已经满了，等待，等待被释放为止！**
- `semaphore.release();` **释放，会将当前的信号量释放 + 1，然后唤醒等待的线程！**

作用：

- **多个共享资源互斥的使用！并发限流，控制最大的线程数！**

## 9. 读写锁

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        int num = 6;
        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.write(String.valueOf(finalI), String.valueOf(finalI));

            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.read(String.valueOf(finalI));

            },String.valueOf(i)).start();
        }
    }
}

/**
 *  方法未加锁，导致写的时候被插队
 */
class MyCache {
    private volatile Map<String, String> map = new HashMap<>();

    public void write(String key, String value) {
        System.out.println(Thread.currentThread().getName() + "线程开始写入");
        map.put(key, value);
        System.out.println(Thread.currentThread().getName() + "线程写入ok");
    }

    public void read(String key) {
        System.out.println(Thread.currentThread().getName() + "线程开始读取");
        map.get(key);
        System.out.println(Thread.currentThread().getName() + "线程写读取ok");
    }
}
2线程开始写入
2线程写入ok
3线程开始写入
3线程写入ok
1线程开始写入    # 插入了其他线程的写入，导致数据不一致
4线程开始写入
4线程写入ok
1线程写入ok
6线程开始写入
6线程写入ok
5线程开始写入
5线程写入ok
1线程开始读取
1线程写读取ok
2线程开始读取
2线程写读取ok
3线程开始读取
3线程写读取ok
4线程开始读取
4线程写读取ok
5线程开始读取
6线程开始读取
6线程写读取ok
5线程写读取ok

Process finished with exit code 0
```

所以如果我们不加锁的情况，多线程的读写会造成数据不可靠的问题。

我们也可以采用**synchronized**这种重量锁和轻量锁 **lock**去保证数据的可靠。

但是这次我们采用更细粒度的锁：**ReadWriteLock** 读写锁来保证

![image-20200811213503631](https://img-service.csdnimg.cn/img_convert/8ee70d5ab31bef2c8afd95cdc32a381e.png)

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache2 myCache = new MyCache2();
        int num = 6;
        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.write(String.valueOf(finalI), String.valueOf(finalI));

            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.read(String.valueOf(finalI));

            },String.valueOf(i)).start();
        }
    }

}
class MyCache2 {
    private volatile Map<String, String> map = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock();

    public void write(String key, String value) {
        lock.writeLock().lock(); // 写锁
        try {
            System.out.println(Thread.currentThread().getName() + "线程开始写入");
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "线程写入ok");

        }finally {
            lock.writeLock().unlock(); // 释放写锁
        }
    }

    public void read(String key) {
        lock.readLock().lock(); // 读锁
        try {
            System.out.println(Thread.currentThread().getName() + "线程开始读取");
            map.get(key);
            System.out.println(Thread.currentThread().getName() + "线程写读取ok");
        }finally {
            lock.readLock().unlock(); // 释放读锁
        }
    }
}
1线程开始写入
1线程写入ok
6线程开始写入
6线程写入ok
3线程开始写入
3线程写入ok
2线程开始写入
2线程写入ok
5线程开始写入
5线程写入ok
4线程开始写入
4线程写入ok
    
1线程开始读取
5线程开始读取
2线程开始读取
1线程写读取ok
3线程开始读取
2线程写读取ok
6线程开始读取
6线程写读取ok
5线程写读取ok
4线程开始读取
4线程写读取ok
3线程写读取ok

Process finished with exit code 0
```

## 10. 阻塞队列

![image-20200812092316296](https://img-service.csdnimg.cn/img_convert/3b6b0b33e6e9b0f2261a89b6e42e78ea.png)

![image-20200812093115670](https://img-service.csdnimg.cn/img_convert/d651ccc40069352ee6c8b86ae2cee8eb.png)

### 1）BlockQueue

是Collection的一个子类

什么情况下我们会使用阻塞队列

> 多线程并发处理、线程池

![image-20200812093254651](https://img-service.csdnimg.cn/img_convert/cae49c50458adc0997d57b2044666ccb.png)

BlockingQueue 有四组api

| 方式         | 抛出异常 | 不会抛出异常，有返回值 | 阻塞，等待 | 超时等待                |
| ------------ | -------- | ---------------------- | ---------- | ----------------------- |
| 添加         | add      | offer                  | put        | offer(timenum.timeUnit) |
| 移出         | remove   | poll                   | take       | poll(timenum,timeUnit)  |
| 判断队首元素 | element  | peek                   | -          | -                       |

- **抛出异常**

```JAVA
    public static void test1() {
        // 队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));
		System.out.println(blockingQueue.element());//查看队首元素
        System.out.println("---------------------");

        //IllegalStateException: Queue full
        //System.out.println(blockingQueue.add("d"));

        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

        //java.util.NoSuchElementException 抛出异常！
        //System.out.println(blockingQueue.remove());
    }
```

- **有返回值，不抛出异常**

```java
/**
* 有返回值，没有异常
*/
    public static void test2() {
        // 队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));

        System.out.println("---------------------");
        System.out.println(blockingQueue.peek());//查看队首元素

		//System.out.println(blockingQueue.offer("d"));// false 不抛出异常！

        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());

        System.out.println(blockingQueue.poll()); // null 不抛出异常！
    }
```

- **等待，阻塞（一直阻塞）（用的较少）**

```java
	/**
     * 等待，阻塞（一直阻塞）
     */
    public static void test3() throws InterruptedException {
        // 队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);
        // 一直阻塞
        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");

        // blockingQueue.put("d"); // 队列没有位置了，会一直阻塞
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take()); // 没有这个元素，一直阻塞
    }
```

- **等待，阻塞（等待超时）（用的较多）**

```java
/**
     * 等待 超时阻塞
     *  这种情况也会等待队列有位置 或者有产品 但是会超时结束
     */
    public static void test4() throws InterruptedException {
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);
        blockingQueue.offer("a");
        blockingQueue.offer("b");
        blockingQueue.offer("c");
        System.out.println("开始等待");
        blockingQueue.offer("d",2, TimeUnit.SECONDS);  //超时时间2s 等待如果超过2s就结束等待
        System.out.println("结束等待");
        System.out.println("===========取值==================");
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println("开始等待");
        blockingQueue.poll(2,TimeUnit.SECONDS); //超过两秒 我们就不要等待了
        System.out.println("结束等待");
    }
```

### 2）同步队列

- 同步队列 没有容量，也可以视为**容量为1的队列**；
- 进去一个元素，必须等待取出来之后，才能再往里面放入一个元素；
- **put**方法 和 **take**方法；
- Synchronized 和 其他的BlockingQueue 不一样 它不存储元素；
- put了一个元素，就必须从里面先take出来，否则不能再put进去值！
- 并且SynchronousQueue 的take是使用了lock锁保证线程安全的

```java
package com.marchsoft.queue;

import java.util.concurrent.BlockingDeque;
import java.util.concurrent.BlockingQueue;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/12 10:02
 **/

public class SynchronousQueue {
    public static void main(String[] args) {
        BlockingQueue<String> synchronousQueue = new java.util.concurrent.SynchronousQueue<>();
        // 网queue中添加元素
        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + "put 01");
                synchronousQueue.put("1");
                System.out.println(Thread.currentThread().getName() + "put 02");
                synchronousQueue.put("2");
                System.out.println(Thread.currentThread().getName() + "put 03");
                synchronousQueue.put("3");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        // 取出元素
        new Thread(()-> {
            try {
                System.out.println(Thread.currentThread().getName() + "take" + synchronousQueue.take());
                System.out.println(Thread.currentThread().getName() + "take" + synchronousQueue.take());
                System.out.println(Thread.currentThread().getName() + "take" + synchronousQueue.take());
            }catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
Thread-0put 01
Thread-1take1
Thread-0put 02
Thread-1take2
Thread-0put 03
Thread-1take3

Process finished with exit code 0
```

## 11、线程池(重点)

**必会： 线程池：三大方法、7大参数、4种拒绝策略**

线程池：三大方式、七大参数、四种拒绝策略

> 池化技术

程序的运行，本质：占用系统的资源！我们需要去优化资源的使用 ===> 池化技术

线程池、JDBC的连接池、内存池、对象池 等等。。。。

资源的创建、销毁十分消耗资源

**池化技术**：事先准备好一些资源，如果有人要用，就来我这里拿，用完之后还给我，以此来提高效率。

### 1）线程池的好处：

1、降低资源的消耗；

2、提高响应的速度；

3、方便管理；

**线程复用、可以控制最大并发数、管理线程；**

### 2）线程池：三大方法

- ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程

- ExecutorService threadPool2 = Executors.newFixedThreadPool(5); //创建一个固定的线程池的大小

- ExecutorService threadPool3 = Executors.newCachedThreadPool(); //可伸缩的

    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204310456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.pool;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

//Executors 工具类， 3大方法
public class Demo01 {
    public static void main(String[] args) {
        ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);//创建一个固定的线程池大小
//        ExecutorService threadPool = Executors.newCachedThreadPool();//颗伸缩的，遇强则强，遇弱则弱

        try {
            for (int i = 0; i < 10; i++) {
                //            new Thread().start();

                // 使用了线程池之后，使用线程池来创建线程
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "  OK");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```

**newSingleThreadExecutor（）**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021013120510032.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)
**newFixedThreadPool(5)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210131205022621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**newCachedThreadPool()**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210131205042255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

### 3）七大参数

三者本质上都是调用`ThreadPoolExecutor`

源码分析

```java
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
    
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
    
// 本质ThreadPoolExecutor（）
 public ThreadPoolExecutor(int corePoolSize, // 核心线程池大小
                              int maximumPoolSize,  // 最大核心线程池大小
                              long keepAliveTime, // 超时了没有人调用就会释放
                              TimeUnit unit, // 超时单位
                              BlockingQueue<Runnable> workQueue,// 阻塞队列
                              ThreadFactory threadFactory,// 线程工厂：创建线程的，一般不用动
                              RejectedExecutionHandler handler// 拒绝策略
                              ) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204422440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

阻塞队列满了的时候，才会触发最大连接数
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204426818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

> 手动创建一个线程池

```java
package com.zzy.pool;

import java.util.concurrent.*;

//Executors 工具类， 3大方法
// Executors 工具类、3大方法

/**
 * new ThreadPoolExecutor.AbortPolicy() // 银行满了，还有人进来，不处理这个人的，抛出异常
 * new ThreadPoolExecutor.CallerRunsPolicy() // 哪来的去哪里！
 * new ThreadPoolExecutor.DiscardPolicy() //队列满了，丢掉任务，不会抛出异常！
 * new ThreadPoolExecutor.DiscardOldestPolicy() //队列满了，尝试去和最早的竞争，也不会抛出异常！
 */
public class Demo01 {
    public static void main(String[] args) {
        // 自定义线程池！工作 ThreadPoolExecutor
        ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                3, TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()//银行人满了，还有人进来，不处理这个人，抛出异常
        );

        try {
            // 最大承载：Deque + max
            // 超过 抛出异常： RejectedExecutionException
            for (int i = 1; i <= 8; i++) {
                // 使用了线程池之后，使用线程池来创建线程
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "  OK");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```

### 4）拒绝策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204506207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
/**
* new ThreadPoolExecutor.AbortPolicy() // 银行满了，还有人进来，不处理这个人的，抛出异常
* new ThreadPoolExecutor.CallerRunsPolicy() // 哪来的去哪里！
* new ThreadPoolExecutor.DiscardPolicy() //队列满了，丢掉任务，不会抛出异常！
* new ThreadPoolExecutor.DiscardOldestPolicy() //队列满了，尝试去和最早的竞争，也不会抛出异常！
*/
```

### 5）如何设置线程池的大小（调优）

**1、CPU密集型：电脑的核数是几核就选择几；选择maximunPoolSize的大小**

```java
// 获取cpu 的核数
        int max = Runtime.getRuntime().availableProcessors();
        ExecutorService service =new ThreadPoolExecutor(
                2,
                max,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
```

**2、I/O密集型：**

在程序中有15个大型任务，io十分占用资源；I/O密集型就是判断我们程序中十分耗I/O的线程数量，大约是最大I/O数的一倍到两倍之间。

## 12. 四大函数式接口

新时代的程序员：**lambda表达式、链式编程、函数式接口、Stream流式计算**

**函数式接口：只有一个方法的接口**

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}

// 传统技术必会：泛型、枚举、反射
// 新技术必会：lambda表达式、链式编程、函数式接口、Stream流式计算
// 超级多FunctionalInterface
// 简化编程模型，在新版本的框架底层大量应用！
// foreach(消费者类的函数式接口)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204603793.png)

### 1）Function 函数型接口

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204621549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.function;

import java.util.function.Function;

/**
 * Function 函数型接口, 有一个输入参数，有一个输出,
 * 只要是函数型接口，就可以用 lambda表达式简化
 */
public class Demo01 {
    public static void main(String[] args) {
        //工具类：输出输入的值
        Function function = new Function<String, String>() {
            @Override
            public String apply(String str) {
                return str;
            }
        };

        //只要是函数型接口，就可以用 lambda表达式简化
        Function function1 = (str) -> {
            return str;
        };

        System.out.println(function1.apply("123"));
    }
}
```

### 2）Predicate 断定型接口

有一个输入参数，返回值只能是 布尔值！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204643644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.function;

import java.util.function.Predicate;

/**
 * 断定型接口：有一个输入参数，返回值只能是 布尔值！
 */
public class Demo02 {

    public static void main(String[] args) {
        //判断字符串是否为空
        Predicate<String> predicate = new Predicate<String>() {
            @Override
            public boolean test(String str) {
                return str.isEmpty();
            }
        };
        //只要是函数型接口，就可以用 lambda表达式简化
        Predicate<String> predicate2 = (str) -> {
            return str.isEmpty();
        };

        System.out.println(predicate.test("asddd"));
    }
}
```

### 3）Suppier 供给型接口

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204745367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.function;

import java.util.function.Predicate;
import java.util.function.Supplier;

/**
 * Supplier 供给型接口 没有参数，只有返回值
 */
public class Demo04 {
    public static void main(String[] args) {
        Supplier supplier = new Supplier<Integer>() {
            @Override
            public Integer get() {
                return 1024;
            }
        };

        //只要是函数型接口，就可以用 lambda表达式简化
        Supplier supplier2 = () -> {
            return 1024;
        };
        
        System.out.println(supplier2.get());
    }
}
```

### 4）Consummer 消费型接口

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204721140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.function;

import com.zzy.pc.C;

import java.util.function.Consumer;

/**
 * Consumer 消费型接口: 只有输入，没有返回值
 */
public class Demo03 {
    public static void main(String[] args) {
        Consumer<String> consumer = new Consumer<String>() {
            @Override
            public void accept(String str) {
                System.out.println(str);
            }
        };


        //只要是函数型接口，就可以用 lambda表达式简化
        Consumer<String> consumer1 = (str)->{
            System.out.println(str);
        };
        consumer.accept("qwre");
    }
}
```

简化编程

## 13. Stream流式计算

> 什么是Stream流式计算

大数据：存储 + 计算

集合、MySQL 本质就是**存储**东西的；

计算都应该交给**流**来操作！

后续大数据scala也用得到。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204826543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
package com.zzy.stream;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

//有参，无参构造，get set toString方法
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;
}
package com.zzy.stream;

import java.util.Arrays;
import java.util.List;

/**
 * 题目要求：一分钟内完成此题，只能用一行代码实现！
 * 现在有5个用户！筛选：
 * 1、ID 必须是偶数
 * 2、年龄必须大于23岁
 * 3、用户名转为大写字母
 * 4、用户名字母倒着排序
 * 5、只输出一个用户！
 */
public class Test {
    public static void main(String[] args) {
        User u1 = new User(1, "a", 21);
        User u2 = new User(2, "b", 22);
        User u3 = new User(3, "c", 23);
        User u4 = new User(4, "d", 24);
        User u5 = new User(6, "e", 25);

        // 集合是存储
        List<User> list = Arrays.asList(u1, u2, u3, u4, u5);

        // 计算交给Stream流

        // lambda表达式、链式编程、函数式接口、Stream流式计算
        list.stream()
                .filter(u -> {
                    return u.getId() % 2 == 0;
                })
                .filter(u -> {
                    return u.getAge() > 23;
                })
                .map(u -> {
                    return u.getName().toUpperCase();
                })
                .sorted((o1, o2) -> {
                    return o2.compareTo(o1);
                })
                .limit(1)
                .forEach(System.out::println);
    }
}
```

## 14. ForkJoin

ForkJoin 在JDK1.7，并行执行任务！提高效率~。在大数据量速率会更快！

大数据中：**MapReduce 核心思想->把大任务拆分为小任务！**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204911181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

### 1）ForkJoin 特点： 工作窃取！

实现原理是：**双端队列**！从上面和下面都可以去拿到任务进行执行！

![image-20200812163701588](https://img-service.csdnimg.cn/img_convert/7ccffb99e41ec5a89ef2118cf0c4f0f2.png)

### 2）如何使用ForkJoin?

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204941807.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130204946159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

- 通过**ForkJoinPool**来执行

- 计算任务 **execute(ForkJoinTask<?> task)**

- 计算类要去继承ForkJoinTask；

    **ForkJoin 的计算类**

```java
package com.marchsoft.forkjoin;

import java.util.concurrent.RecursiveTask;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/13 8:33
 **/

public class ForkJoinDemo extends RecursiveTask<Long> {
    private long star;
    private long end;
    /** 临界值 */
    private long temp = 1000000L;

    public ForkJoinDemo(long star, long end) {
        this.star = star;
        this.end = end;
    }

    /**
     * 计算方法
     * @return
     */
    @Override
    protected Long compute() {
        if ((end - star) < temp) {
            Long sum = 0L;
            for (Long i = star; i < end; i++) {
                sum += i;
            }
            return sum;
        }else {
            // 使用ForkJoin 分而治之 计算
            //1 . 计算平均值
            long middle = (star + end) / 2;
            ForkJoinDemo forkJoinDemo1 = new ForkJoinDemo(star, middle);
            // 拆分任务，把线程压入线程队列
            forkJoinDemo1.fork();
            ForkJoinDemo forkJoinDemo2 = new ForkJoinDemo(middle, end);
            forkJoinDemo2.fork();

            long taskSum = forkJoinDemo1.join() + forkJoinDemo2.join();
            return taskSum;
        }
    }
}
```

**测试类**

```java
package com.marchsoft.forkjoin;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.stream.LongStream;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/13 8:43
 **/

public class ForkJoinTest {
    private static final long SUM = 20_0000_0000;

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        test1();
        test2();
        test3();
    }

    /**
     * 使用普通方法
     */
    public static void test1() {
        long star = System.currentTimeMillis();
        long sum = 0L;
        for (long i = 1; i < SUM ; i++) {
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println(sum);
        System.out.println("时间：" + (end - star));
        System.out.println("----------------------");
    }
    /**
     * 使用ForkJoin 方法
     */
    public static void test2() throws ExecutionException, InterruptedException {
        long star = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinDemo(0L, SUM);
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);
        Long along = submit.get();

        System.out.println(along);
        long end = System.currentTimeMillis();
        System.out.println("时间：" + (end - star));
        System.out.println("-----------");
    }
    /**
     * 使用 Stream 流计算
     */
    public static void test3() {
        long star = System.currentTimeMillis();

        long sum = LongStream.range(0L, 20_0000_0000L).parallel().reduce(0, Long::sum);
        System.out.println(sum);
        long end = System.currentTimeMillis();
        System.out.println("时间：" + (end - star));
        System.out.println("-----------");
    }
}
```

![image-20200813090527527](https://img-service.csdnimg.cn/img_convert/12579f3c5a94870c6a9f546390896e72.png)

**.parallel().reduce(0, Long::sum)使用一个并行流去计算整个计算，提高效率。**

![image-20200812164023833](https://img-service.csdnimg.cn/img_convert/44c24ea717d569f1966ec8f14f6ce39f.png)

## 15、异步回调

> Future 设计的初衷： 对将来的某个事件的结果进行建模

其实就是前端 --> 发送ajax异步请求给后端

但是我们平时都使用**CompletableFuture**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130205040360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

#### （1）没有返回值的runAsync异步回调

```java
package com.zzy.future;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

/**
 * 异步调用： CompletableFuture
 * // 异步执行
 * // 成功回调
 * // 失败回调
 */
public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //发起一个请求 void
        // 没有返回值的 runAsync 异步回调
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "runAsync=>void");
        });

        System.out.println("11111111");
        completableFuture.get();//获取执行结果
    }

}
```

#### （2）有返回值的异步回调supplyAsync

```java
package com.zzy.future;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

/**
 * 异步调用： CompletableFuture
 * // 异步执行
 * // 成功回调
 * // 失败回调
 */
public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // completableFuture.get(); // 获取阻塞执行结果
        // 有返回值的 supplyAsync 异步回调
        // ajax，成功和失败的回调
        // 失败返回的是错误信息；
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "supplyAsync=>Integer");
//            int i = 10 / 0;
            return 1024;
        });

        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("t=>" + t);// 正常的返回结果
            System.out.println("u=>" + u);// 错误信息：

        }).exceptionally((e) -> {
            System.out.println(e.getMessage());
            return 233;// 可以获取到错误的返回结果
        }).get());
    }
    /**
     * 工作中：
     * succee Code 200
     * error Code 404 500
     */
}
```

**whenComplete**: 有两个参数，一个是t 一个是u

T：是代表的 **正常返回的结果**；

U：是代表的 **抛出异常的错误信息**；

如果发生了异常，get可以获取到**exceptionally**返回的值；

## 16. JMM

### 1）对Volatile 的理解

**Volatile** 是 Java 虚拟机提供 **轻量级的同步机制**

**1、保证可见性
2、不保证原子性
3、禁止指令重排**

**如何实现可见性**

volatile变量修饰的共享变量在进行写操作的时候回多出一行汇编：

0x01a3de1d:movb $0×0，0×1104800（%esi）;0x01a3de24**:lock** addl $0×0,(%esp);

Lock前缀的指令在多核处理器下会引发两件事情。

 1）将当前处理器缓存行的数据写回到系统内存。

 2）这个写回内存的操作会使其他cpu里缓存了该内存地址的数据无效。

**多处理器总线嗅探：**

为了提高处理速度，处理器不直接和内存进行通信，而是先将系统内存的数据读到内部缓存后再进行操作，但操作不知道何时会写到内存。如果对声明了**volatile**的变量进行写操作，JVM就会向处理器发送一条lock前缀的指令，将这个变量所在缓存行的数据写回到系统内存。但是在**多处理器下**，为了保证各个处理器的缓存是一致的，就会实现缓存缓存一致性协议，**每个处理器通过嗅探在总线上传播的数据来检查自己的缓存值是不是过期了，如果处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置无效状态**，当处理器对这个数据进行修改操作的时候，会重新从系统内存中把数据库读到处理器缓存中。

### 2）什么是JMM？

**JMM：JAVA内存模型**，不存在的东西，是一个概念，也是一个约定！

**关于JMM的一些同步的约定：**

1、线程解锁前，必须把共享变量**立刻**刷回主存；

2、线程加锁前，必须**读取主存**中的最新值到工作内存中；

3、加锁和解锁是同一把锁；

线程中分为 **工作内存、主内存**

**8种操作:**

读->加载->使用->赋值（assign）->写->存储

加锁、解锁

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130205242466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130205246301.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

内存交互操作有8种，虚拟机实现必须保证每一个操作都是原子的，不可在分的（对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许例外）

- **Read（读取）**：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用；
- **load（载入）**：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中；
- **Use（使用）**：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令；
- **assign（赋值）**：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中；
- **store（存储）**：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用；
- **write（写入）**：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中；
- **lock（锁定）**：作用于主内存的变量，把一个变量标识为线程独占状态；
- **unlock（解锁）**：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定；

**JMM对这八种指令的使用，制定了如下规则：**

- 不允许read和load、store和write操作之一单独出现，必须成对使用。即使用了read必须load，使用了store必须write
- 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
- 不允许一个线程将没有assign的数据从工作内存同步回主内存
    一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作
- 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
- 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
- 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量对一个变量进行unlock操作之前，必须把此变量同步回主内存
    问题： 程序不知道主内存的值已经被修改过了
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021013020545399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

## 17. volatile

### 1）保证可见性

```java
public class JMMDemo01 {

    // 如果不加volatile 程序会死循环
    // 加了volatile是可以保证可见性的
    private volatile static Integer number = 0;

    public static void main(String[] args) {
        //main线程
        //子线程1
        new Thread(()->{
            while (number==0){
            }
        }).start();
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        //子线程2
        new Thread(()->{
            while (number==0){
            }

        }).start();
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        number=1;
        System.out.println(number);
    }
}
```

### 2）不保证原子性

原子性：不可分割；

线程A在执行任务的时候，不能被打扰的，也不能被分割的，要么同时成功，要么同时失败。

```java
/**
 * 不保证原子性
 * number <=2w
 * 
 */
public class VDemo02 {

    private static volatile int number = 0;

    public static void add(){
        number++; 
        //++ 不是一个原子性操作，是两个~3个操作
        //
    }

    public static void main(String[] args) {
        //理论上number  === 20000

        for (int i = 1; i <= 20; i++) {
            new Thread(()->{
                for (int j = 1; j <= 1000 ; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount()>2){
            //main  gc
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName()+",num="+number);
    }
}
```

**如果不加lock和synchronized ，怎么样保证原子性？**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130205614525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**使用原子类，解决原子性问题**，比锁高效很多倍！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130205626683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

```java
public class VDemo02 {

    private static volatile AtomicInteger number = new AtomicInteger();

    public static void add(){
//        number++;
        number.incrementAndGet();  //底层是CAS保证的原子性
    }

    public static void main(String[] args) {
        //理论上number  === 20000

        for (int i = 1; i <= 20; i++) {
            new Thread(()->{
                for (int j = 1; j <= 1000 ; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount()>2){
            //main  gc
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName()+",num="+number);
    }
}
```

这些类的底层都直接和操作系统挂钩！是在内存中修改值。

Unsafe类是一个很特殊的存在；

> 原子类为什么这么高级？

### 3）禁止指令重排

**什么是指令重排？**

我们写的程序，计算机并不是按照我们自己写的那样去执行的

源代码–>编译器优化重排–>指令并行也可能会重排–>内存系统也会重排–>执行

**处理器在进行指令重排的时候，会考虑数据之间的依赖性！**

```java
int x=1; //1
int y=2; //2
x=x+5;   //3
y=x*x;   //4

//我们期望的执行顺序是 1_2_3_4  可能执行的顺序会变成2134 1324
//可不可能是 4123？ 不可能的
```

可能造成的影响结果：前提：a b x y这四个值 默认都是0

| 线程A | 线程B |
| ----- | ----- |
| x=a   | y=b   |
| b=1   | a=2   |

正常的结果： x = 0; y =0;

| 线程A | 线程B |
| ----- | ----- |
| b=1   | a=2   |
| x=a   | y=b   |

可能在线程A中会出现，先执行b=1,然后再执行x=a；

在B线程中可能会出现，先执行a=2，然后执行y=b；

那么就有可能结果如下：x=2; y=1.

**volatile可以避免指令重排：**

**volatile中会加一道内存的屏障，这个内存屏障可以保证在这个屏障中的指令顺序。**

内存屏障：CPU指令。作用：

1、保证特定的操作的执行顺序；

2、可以保证某些变量的内存可见性（利用这些特性，就可以保证volatile实现的可见性）

volatile在上下加上两层内存屏障，防止指令重排。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021013021045412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

### 4）总结

- **volatile可以保证可见性；**
- **不能保证原子性**
- **由于内存屏障，可以保证避免指令重排的现象产生**

面试官：那么你知道在哪里用这个内存屏障用得最多呢？**单例模式**

## 18. 玩转单例模式

为什么枚举可以避免单例模式被破坏？

饿汉式 DCL懒汉式，深究！

### 1）饿汉式

```java
package com.zzy.single;
//饿汉式单例
public class Hungry {
    //一上来就实例化，可能会浪费空间
    private byte[] data1 =new byte[1024*1024];
    private byte[] data2 =new byte[1024*1024];
    private byte[] data3 =new byte[1024*1024];
    private byte[] data4 =new byte[1024*1024];

    //私有化构造器
    private Hungry() {

    }
    private final static Hungry HUNGRY = new Hungry();

    public Hungry getInstance() {
        return HUNGRY;
    }
}
```

### 2）DCL懒汉式

```java
package com.zzy.single;

// 懒汉式单例
// 道高一尺，魔高一丈！
public class LazyMan {
    //私有化构造器
    private LazyMan() {
        System.out.println(Thread.currentThread().getName()+"OK");
    }

    private static LazyMan lazyMan;

    public static LazyMan getInstance() {
        if (lazyMan == null) {
            lazyMan = new LazyMan();
        }
        return lazyMan;
    }


    //多线程并，会有隐患！
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                lazyMan.getInstance();
            }).start();
        }

    }
}
```

> 双重检测锁模式的懒汉式单例（DCL懒汉式）

```java
package com.zzy.single;

// 懒汉式单例
// 道高一尺，魔高一丈！
public class LazyMan {
    //私有化构造器
    private LazyMan() {
        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private static LazyMan lazyMan;

    // 双重检测锁模式的 懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();
                }
            }
        }
        return lazyMan;
    }

    //多线程并发
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                lazyMan.getInstance();
            }).start();
        }

    }
}
```

> 加volatile，防止指令重排

```java
package com.zzy.single;

// 懒汉式单例
// 道高一尺，魔高一丈！
public class LazyMan {
    //私有化构造器
    private LazyMan() {
        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private volatile static LazyMan lazyMan;

    // 双重检测锁模式的 懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();// 不是一个原子性操作
                    /**
                     * 1. 分配内存空间
                     * 2、执行构造方法，初始化对象
                     * 3、把这个对象指向这个空间
                     * 执行顺序123,132都有可能
                     * A：123
                     * B：132
                     * B把这个对象指向这个空间，发现不为空执行return
                     * // 但是此时在线程A中，lazyMan还没有完成构造，lazyMan要加volatile，防止指令重排
                     */
                }
            }
        }
        return lazyMan;
    }


    //多线程并发
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                lazyMan.getInstance();
            }).start();
        }
    }
}
```

> 静态内部类

```java
package com.zzy.single;

/**
 * @author Zhao
 * @DATE 2021/2/1 - 23:08
 */
public class Holder {
    private Holder() {
    }

    private static Holder getInstance() {
        return InnerClass.HOLDER;
    }

    public static class InnerClass {
        private static final Holder HOLDER = new Holder();
    }
}
```

> 以上都不安全，可以通过反射破坏！

```java
package com.zzy.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

// 懒汉式单例
// 道高一尺，魔高一丈！
public class LazyMan {
    //私有化构造器
    private LazyMan() {
        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private volatile static LazyMan lazyMan;

    // 双重检测锁模式的 懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();// 不是一个原子性操作
                }
            }
        }
        return lazyMan;
    }

    //多线程并发
    public static void main(String[] args) throws Exception {
        LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);//无视私有
        LazyMan instance2 = declaredConstructor.newInstance();

        System.out.println(instance);
        System.out.println(instance2);
       
    }
}
```

> 防止反射破坏异常

```java
package com.zzy.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

// 懒汉式单例
// 道高一尺，魔高一丈！
public class LazyMan {
    //私有化构造器
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (lazyMan != null) {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private volatile static LazyMan lazyMan;

    // 双重检测锁模式的 懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();// 不是一个原子性操作
                }
            }
        }
        return lazyMan;
    }

    //多线程并发
    public static void main(String[] args) throws Exception {
        LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);//无视私有
        LazyMan instance2 = declaredConstructor.newInstance();
        System.out.println(instance);
        System.out.println(instance2);
    }
}
```

> 但是仍然可以通过如下方式破坏：

```java
 //多线程并发
    public static void main(String[] args) throws Exception {
//        LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);//无视私有
        LazyMan instance1 = declaredConstructor.newInstance();
        LazyMan instance2 = declaredConstructor.newInstance();

        System.out.println(instance1);
        System.out.println(instance2);
    }
```

> 设置一个别人不知道的变量

```java
package com.zzy.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

// 懒汉式单例
public class LazyMan {

    //定义一个别人不知道的变量
    private static boolean hello = false;

    //私有化构造器
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (hello == false) {
                hello = true;
            } else {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private volatile static LazyMan lazyMan;

    // 双重检测锁模式的 懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();// 不是一个原子性操作
                }
            }
        }
        return lazyMan;
    }

    //多线程并发
    public static void main(String[] args) throws Exception {
//        LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);//无视私有
        LazyMan instance1 = declaredConstructor.newInstance();
        LazyMan instance2 = declaredConstructor.newInstance();

        System.out.println(instance1);
        System.out.println(instance2);
    }
}
```

继续破坏，道高一尺，魔高一丈！

```java
 //多线程并发
    public static void main(String[] args) throws Exception {
//        LazyMan instance = LazyMan.getInstance();
        Field hello = LazyMan.class.getDeclaredField("hello");
        hello.setAccessible(true);

        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);//无视私有
        LazyMan instance1 = declaredConstructor.newInstance();
        hello.set(instance1,false);
        LazyMan instance2 = declaredConstructor.newInstance();

        System.out.println(instance1);
        System.out.println(instance2);
    }
```

### 3）静态内部类

```java
//静态内部类
public class Holder {
    private Holder(){

    }
    public static Holder getInstance(){
        return InnerClass.holder;
    }
    public static class InnerClass{
        private static final Holder holder = new Holder();
    }
}
```

> 单例不安全, 因为反射

### 4）枚举

使用枚举，我们就可以防止反射破坏了。

```java
package com.zzy.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

// enum 是一个什么？ 本身也是一个Class类
public enum EnumSingle {
    INSTANCE;

    public  EnumSingle getInstance(){
        return INSTANCE;
    }
}


class Test{
    public static void main(String[] args) throws Exception {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);

        //java.lang.NoSuchMethodException: com.zzy.single.EnumSingle.<init>() 没有空参构造方法
        EnumSingle instance2 = declaredConstructor.newInstance();
        
        System.out.println(instance1);
        System.out.println(instance2);
    }
}
```

反编译

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210201233130398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130210713789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**使用jad工具反编译为java**

> 枚举类型的最终反编译源码：

```java
// Decompiled by Jad v1.5.8g. Copyright 2001 Pavel Kouznetsov.
// Jad home page: http://www.kpdus.com/jad.html
// Decompiler options: packimports(3)
// Source File Name: EnumSingle.java
public final class EnumSingle extends Enum
{
    public static EnumSingle[] values()
    {
        return (EnumSingle[])$VALUES.clone();
    }
    public static EnumSingle valueOf(String name)
    {
        return (EnumSingle)Enum.valueOf(com/kuang/single/EnumSingle, name);
    }
    private EnumSingle(String s, int i)
    {
        super(s, i);
    }
    public EnumSingle getInstance()
    {
        return INSTANCE;
    }
    public static final EnumSingle INSTANCE;
    private static final EnumSingle $VALUES[];
    static
    {
        INSTANCE = new EnumSingle("INSTANCE", 0);
        $VALUES = (new EnumSingle[] {
                INSTANCE
        });
    }
}
```

> 源码骗了我们，用了一个有参构造器

```java
class Test{
    public static void main(String[] args) throws Exception {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        declaredConstructor.setAccessible(true);

        //java.lang.IllegalArgumentException: Cannot reflectively create enum objects
        EnumSingle instance2 = declaredConstructor.newInstance();
        
        System.out.println(instance1);
        System.out.println(instance2);
    }
}
```

抛出异常： `java.lang.IllegalArgumentException: Cannot reflectively create enum objects`。

## 19、深入理解CAS

### 1）什么是CAS？

进大厂必须要深入研究底层！否则进不去！
有所突破！ 修内功，**操作系统，计算机网络原理**。

```java
public class casDemo {
    //CAS : compareAndSet 比较并交换
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);

        //boolean compareAndSet(int expect, int update)
        //期望值、更新值
        //如果实际值 和 我的期望值相同，那么就更新
        //如果实际值 和 我的期望值不同，那么就不更新
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

        //因为期望值是2020  实际值却变成了2021  所以会修改失败
        //CAS 是CPU的并发原语
        atomicInteger.getAndIncrement(); //++操作
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
    }
}
```

**Unsafe 类**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130210811882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130210816240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130210820672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

### 2）总结

**CAS**：比较当前工作内存中的值 和 主内存中的值，如果这个值是期望的，那么则执行操作！如果不是就一直循环，使用的是自旋锁。

**缺点：**

- 循环会耗时；
- 一次性只能保证一个共享变量的原子性；
- 它会存在ABA问题

> CAS：ABA问题？(狸猫换太子)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130210837700.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

线程1：期望值是1，要变成2；

线程2：两个操作：

- 1、期望值是1，变成3
- 2、期望是3，变成1

所以对于线程1来说，A的值还是1，所以就出现了问题，骗过了线程1；

```java
package com.zzy.cas;

import java.util.concurrent.atomic.AtomicInteger;


public class CASDemo {
    // CAS compareAndSet : 比较并交换！
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);


        //平时写的SQL：乐观锁
        
        //期望 更新
        //public final boolean compareAndSet(int expect, int update)
        // 如果我期望的值达到了，那么就更新，否则，就不更新, CAS 是CPU的并发原语！
        // ============== 捣乱的线程 ==================
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(2021, 2020));
        System.out.println(atomicInteger.get());

        // ============== 期望的线程 ==================
        System.out.println(atomicInteger.compareAndSet(2020, 77777));
        System.out.println(atomicInteger.get());
    }
}
```

## 20、原子引用

> 解决ABA 问题：引入原子引用，对应的思想：乐观锁！

**带版本号 的原子操作！**

```java
package com.marchsoft.lockdemo;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicStampedReference;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/12 22:07
 **/

public class CASDemo {
    /**AtomicStampedReference 注意，如果泛型是一个包装类，注意对象的引用问题
     * 正常在业务操作，这里面比较的都是一个个对象
     */
    static AtomicStampedReference<Integer> atomicStampedReference = new
            AtomicStampedReference<>(1, 1);

    // CAS compareAndSet : 比较并交换！
    public static void main(String[] args) {
        new Thread(() -> {
            int stamp = atomicStampedReference.getStamp(); // 获得版本号
            System.out.println("a1=>" + stamp);
            
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // 修改操作时，版本号更新 + 1
            atomicStampedReference.compareAndSet(1, 2,
                    atomicStampedReference.getStamp(),
                    atomicStampedReference.getStamp() + 1);
            
            System.out.println("a2=>" + atomicStampedReference.getStamp());
            // 重新把值改回去， 版本号更新 + 1
            System.out.println(atomicStampedReference.compareAndSet(2, 1,
                    atomicStampedReference.getStamp(),
                    atomicStampedReference.getStamp() + 1));
            System.out.println("a3=>" + atomicStampedReference.getStamp());
        }, "a").start();
        
        // 乐观锁的原理相同！
        new Thread(() -> {
            int stamp = atomicStampedReference.getStamp(); // 获得版本号
            System.out.println("b1=>" + stamp);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicStampedReference.compareAndSet(1, 3,
                    stamp, stamp + 1));
            System.out.println("b2=>" + atomicStampedReference.getStamp());
        }, "b").start();
    }
}
```

**注意：**

**Integer 使用了对象缓存机制，默认范围是 -128 ~ 127 ，推荐使用静态工厂方法 valueOf 获取对象实例，而不是 new，因为 valueOf 使用缓存，而 new 一定会创建新的对象分配新的内存空间；**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021013021094151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

## 21、各种锁的理解

### 1）公平锁，非公平锁

1. 公平锁： 非常公平， 不能够插队，必须先来后到！
2. 非公平锁：非常不公平，可以插队 （默认都是非公平）

```java
public ReentrantLock() {
	sync = new NonfairSync();
}

public ReentrantLock(boolean fair) {
	sync = fair ? new FairSync() : new NonfairSync();
}
```

### 2）可重入锁

可重入锁（递归锁）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130211017693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

> **Synchronized**

```java
package com.zzy.lock;


//Synchronized
public class Demo01 {

    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B").start();
    }
}
class Phone{
    public synchronized void sms(){
        System.out.println(Thread.currentThread().getName()+"：sms");
        call();//这里也有锁
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName()+"：call");
    }
}
```

> **Lock 版**

```java
package com.zzy.lock;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

//Synchronized
public class Demo02 {

    public static void main(String[] args) {
        Phone2 phone = new Phone2();
        new Thread(() -> {
            phone.sms();
        }, "A").start();

        new Thread(() -> {
            phone.sms();
        }, "B").start();
    }
}

class Phone2 {
    Lock lock = new ReentrantLock();

    public synchronized void sms() {
        lock.lock();// 细节问题：lock.lock(); lock.unlock(); 
        // lock锁必须配对，lock与unlock成对出现，否则就会死在里面
        try {
            System.out.println(Thread.currentThread().getName() + "：sms");
            call();//这里也有锁
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public synchronized void call() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "：call");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

- lock锁必须配对，相当于lock和 unlock 必须数量相同；
- 在外面加的锁，也可以在里面解锁；在里面加的锁，在外面也可以解锁；

### 3）自旋锁

> spinlock

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130211117949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

> 我们来自定义一个锁测试

```java
package com.zzy.lock;


import java.util.concurrent.atomic.AtomicReference;

/**
 * 自旋锁
 */
public class SpinlockDemo {
    //初始： int -> 0; 引用类型 Thread -> null
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    // 加锁
    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "==>myLock");
        //自旋锁
        while (!atomicReference.compareAndSet(null, thread)) {

        }
    }

    // 解锁
    public void myUnlock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "==>myUnlock");
        //自旋锁
        atomicReference.compareAndSet(thread, null);

    }
}
```

> 测试

```java
package com.zzy.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @author Zhao
 * @DATE 2021/2/3 - 23:29
 */
public class TestSpinLock {
    public static void main(String[] args) {
        //ReentrantLock
        ReentrantLock reentrantLock = new ReentrantLock();
        reentrantLock.lock();
        reentrantLock.unlock();

        // 底层使用的自旋锁CAS
        SpinlockDemo lock = new SpinlockDemo();

        new Thread(()->{
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.myUnlock();
            }

        },"T1").start();

        new Thread(()->{
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.myUnlock();
            }

        },"T2").start();

    }
}
```

运行结果：

**t2进程必须等待t1进程Unlock后，才能Unlock，在这之前进行自旋等待。。。。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021020323352695.png)

### 4）死锁

> 死锁是什么

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210130211610635.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

死锁测试，怎么排除死锁：

```java
package com.zzy.lock;
import java.util.concurrent.TimeUnit;

public class DeadLockDemo {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new MyThread(lockA, lockB), "T1").start();

        new Thread(new MyThread(lockB, lockA), "T2").start();

    }
}

class MyThread implements Runnable {
    private String lockA;
    private String lockB;

    public MyThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + "lock:" + lockA + "=>get" + lockB);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + "lock:" + lockB + "=>get" + lockA);
            }
        }
    }
}
```

> **如何解开死锁**

**1、使用jps定位进程号，jdk的bin目录下： 有一个jps**

命令：`jps -l`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210203234752484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

**2、使用`jstack 进程号`，找到死锁问题**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210203234936640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc1ODYzMw==,size_16,color_FFFFFF,t_70)

面试：工作中排查问题：

1. 查看日志
2. 堆栈信息
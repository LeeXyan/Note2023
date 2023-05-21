### Java实现多线程

---

方法一：

- 定义一个类MyThread继承Thread类
- 在MyThread类中重写run()方法
- 创建MyThread类的对象
- 启动线程

```java
// 将需要被线程执行的让我们封装对线程执行的代码
public class MyTread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++){
            System.out.println(i);
        }
    }
    
}
```

测试类

```java
public class MyTreadDemo {
    public static void main(String[] args) {
        MyTread my1 = new MyTread();
        MyTread my2 = new MyTread();
        
        my1.start();
        my2.start();
    }
}

```

两个小问题

- 为什么要重写run方法？
  - 因为run()是用来封装被线程执行的代码

- run()和start()方法的区别？
  - run()：封装线程执行的代码，直接调用，相当于普通方法的调用
  - start()：启动线程，然后有JVM调用此线程的run()方法

### 设置线程名称

---

文档描述

```java
public class Thread extends Object implements Runnable
    线程是程序中执行的线程。 Java虚拟机允许应用程序同时执行多个执行线程。
```

构造方法

```java
Thread​(String name) 分配一个新的 Thread对象。  
Thread​() 分配一个新的 Thread对象。  
```

方法摘要

```java
String getName​() 返回此线程的名称。  
```

如果使用的是Thread(String name)这个构造方法，那么我们的线程类也要实行带参数的构造方法

```java
public class MyThread extends Thread{
    public MyThread(){

    }

    public MyThread(String name) {
        super(name);
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++){
            System.out.println(getName()+"  : "+i);
        }
    }
}
```

在我们的测试类中，我们可以单独给线程取一个名字，也可使用在实例化一个类的时候给他一个名字

```java
public class MyThreadDemo {
    public static void main(String[] args) {
//        MyTread my1 = new MyTread();
//        MyTread my2 = new MyTread();
//
//
//        my1.setName("高铁");
//        my2.setName("飞机");

        MyThread my1 = new MyThread("高铁");
        MyThread my2 = new MyThread("飞机");
        my1.start();
        my2.start();
    }
}
```

---

### 线程优先级

首先我们先来聊一下**线程调度**：

有两种调度模型：分时调度模型和[抢占式](https://baike.baidu.com/item/抢占式)调度模型。

分时调度模型是指让所有的[线程](https://baike.baidu.com/item/线程)轮流获得cpu的使用权,并且平均分配每个线程占用的CPU的时间片这个也比较好理解。

JAVA虚拟机采用抢占式调度模型，是指优先让可运行池中优先级高的线程占用CPU，如果可运行池中的线程优先级相同，那么就随机选择一个线程，使其占用CPU。处于运行状态的线程会一直运行，直至它不得不放弃CPU。

Thread类中设置和获取线程优先级的方法

```java
int getPriority​() 返回此线程的优先级。 
void setPriority​(int newPriority) 更改此线程的优先级。  
```

获取优先级的方法

```java
System.out.println(my1.getPriority()); // 5
System.out.println(my2.getPriority()); // 5
System.out.println(my3.getPriority()); // 5
```

我们通过实例来看一看

```java
public class MyThread extends Thread{
    @Override
    public void run() {
        for(int i=0;i<100;i++){
            System.out.println(getName()+"  "+i);
        }
    }

}
```

测试类

```java
public class MyThreadDemo {
    public static void main(String[] args) {
        MyThread my1 = new MyThread();
        MyThread my2 = new MyThread();
        MyThread my3 = new MyThread();

        my1.setName("高铁");
        my2.setName("飞机");
        my3.setName("汽车");

        System.out.println(my1.getPriority()); // 5
        System.out.println(my2.getPriority()); // 5
        System.out.println(my3.getPriority()); // 5

        System.out.println(Thread.MAX_PRIORITY); // 10
        System.out.println(Thread.MIN_PRIORITY); // 1
        System.out.println(Thread.NORM_PRIORITY); // 5

        my1.setPriority(10);
        my2.setPriority(1);
        my3.setPriority(1);

        my1.start();
        my2.start();
        my3.start();
    }
}
```

### 线程的控制

---

**线程睡眠**

在线程的run方法內使用sleep方法就可以让线程睡着

```java
public class ThreadSleep extends Thread{
    @Override
    public void run() {
        for(int i=0;i<100;i++){
            System.out.println(getName()+" : "+i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**线程的阻塞**

其他线程必须要等待这个线程结束才能继续

```java
public class ThreadJoinDemo {
    public static void main(String[] args) {
        ThreadJoin tj1 = new ThreadJoin();
        ThreadJoin tj2 = new ThreadJoin();
        ThreadJoin tj3 = new ThreadJoin();

        tj1.setName("hello");
        tj2.setName("Java");
        tj3.setName("World");

        tj1.start();
        try {
            tj1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        tj2.start();
        tj3.start();

    }
}
```

**守护线程**

守护线程会随着主线程的结束而结束

```java
public class ThreadDaemonDemo {
    public static void main(String[] args) {
        ThreadDeamon td1 = new ThreadDeamon();
        ThreadDeamon td2 = new ThreadDeamon();

        td1.setName("关羽");
        td2.setName("张飞");

        // 主线程为刘备

        Thread.currentThread().setName("刘备");

        td1.setDaemon(true); // 守护线程
        td2.setDaemon(true);

        td1.start();
        td2.start();

        for(int i=0;i<10;i++){
            System.out.println(Thread.currentThread().getName()+" : "+i);
        }

    }
}

```

---


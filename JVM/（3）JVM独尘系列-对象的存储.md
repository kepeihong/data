# 对象的存储
## 1.对象内存分配方式
指针碰撞、空闲列表（并发安全，cas机制、分配缓冲）

Java堆是被所有线程共享的一块内存区域，主要用于存放对象实例，为对象分配内存就是把一块大小确定的内存从堆内存中划分出来，通常有指针碰撞和空闲列表两种实现方式。

### 1.指针碰撞法
假设Java堆中内存时完整的，已分配的内存和空闲内存分别在不同的一侧，通过一个指针作为分界点，需要分配内存时，仅仅需要把指针往空闲的一端移动与对象大小相等的距离。使用的GC收集器：Serial、ParNew，适用堆内存规整（即没有内存碎片）的情况下。

### 2.空闲列表法
事实上，Java堆的内存并不是完整的，已分配的内存和空闲内存相互交错，JVM通过维护一个列表，记录可用的内存块信息，当分配操作发生时，从列表中找到一个足够大的内存块分配给对象实例，并更新列表上的记录。使用的GC收集器：CMS，适用堆内存不规整的情况下。

## 内存分配并发问题

在创建对象的时候有一个很重要的问题，就是线程安全，因为在实际开发过程中，创建对象是很频繁的事情，作为虚拟机来说，必须要保证线程是安全的，通常来讲，虚拟机采用两种方式来保证线程安全：

CAS： CAS 是乐观锁的一种实现方式。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。虚拟机采用 CAS 配上失败重试的方式保证更新操作的原子性。
TLAB： 为每一个线程预先分配一块内存，JVM在给线程中的对象分配内存时，首先在TLAB分配，当对象大于TLAB中的剩余内存或TLAB的内存已用尽时，再采用上述的CAS进行内存分配。
如果想多了，创建一个对象还是挺麻烦的，需要这么多步骤，那么我们在开发过程中尽量非必须的对象创建呢？

## 创建对象要点：

类加载机制检查：JVM首先检查一个new指令的参数是否能在常量池中定位到一个符号引用，并且检查该符号引用代表的类是否已被加载、解析和初始化过

* 分配内存：把一块儿确定大小的内存从Java堆中划分出来
* 初始化零值：对象的实例字段不需要赋初始值也可以直接使用其默认零值，就是这里起得作用
* 设置对象头：存储对象自身的运行时数据，类型指针
* 执行<init>：为对象的字段赋值
* 栈上分配：方法内的对象只在当前方法使用，没有其他地方使用，没必要创建在堆里，这是分配在栈桢里的私有空间
```
public void a(){
    Test t = new Test();
}
```
内存逃逸：对象太大，或者会被其他方法调用，都会将对象存储在堆中，也就是从栈里逃到了堆中。
```
public Test a(){
    return new Test();
}
```
## 2.对象的内存布局
对象头（运行时数据、gc标志、对象分代年龄、锁状态）、实例数据、对其填充

在 HotSpot 虚拟机中，对象在内存中的布局主要分为三部分：对象头（Header）、实例数据（Instance Data）、对齐填充（Padding）。



图一：对象内存布局模型示例

![](https://img-blog.csdnimg.cn/img_convert/ced382bc7c1e2e3116268bfc3b7449c9.png)

如图所示，对象头主要包含两部分数据： MarkWord、类型指针。其中第一部分数据 MarkWord 用于存储哈希码（HashCode）、GC分代年龄、锁状态标志位、线程持有的锁、偏向线程ID等信息。这部分数据长度在32位和64位虚拟机中的长度为32bit和64bit。
对象头的另外一部分是类型指针，即对象指向他的类元数据指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。另外，如果对象是一个 Java 数组，那么对象头中还必须有一块用于记录数组长度的数据，因为虚拟机可以通过普通 Java 对象的元数据信息来确定 Java 对象的大小，但是从数组的元数据中却无法确定数组的大小 。

 

接下来的实例数据（Instance Data）是对象真正存储的有效信息，也是代码中所定义的各种类型的字段信息。

第三部分对其填充并不是一定存在的，也没有特别的意义，只是起到了占位的作用。存在的原因是因为 HotSpot 虚拟机要求对象的起始地址必须是8的整数倍，换句话说就是对象的大小必须是8字节的整数倍，又因为对象头部分正好是8字节的整数倍，所以当实例数据部分没有对齐时，就需要通过对齐填充来补全。

对象头的 MarkWord

为了能够更加具体形象的看到对象的内存布局，我们使用 OpenJDK 的 JOL 包来做实验，先添加 maven 依赖。

 
```
<dependency>
      <groupId>org.openjdk.jol</groupId>
      <artifactId>jol-core</artifactId>
      <version>0.9</version>
</dependency>
先定义一个普通的 Java 对象，代码如下：

 

public class Person {
    String str = "test";
    Son son = new Son();
}
 
class Son{
 
}
```
新定义的Person有两个属性，一个是普通的字符串，还有一个是自定义的类对象。接下来我们利用 JOL 包下的 ClassLayout 来输出他的内存布局，代码如下：

 
```
public class SeeBin {
    public static void main(String[] args) throws InterruptedException {
        Person person = new Person();
        System.out.println(ClassLayout.parseInstance(person).toPrintable());
 
    }
}
```
代码中初始化了一个 Person 对象，随后便直接打印了他的内存布局，输出结果如下：



 

图中的1、2、3、4分别对应 MarkWord、类型指针、实例数据、对齐填充。

MarkWord：共8字节，该对象刚新建，还处于无锁状态，所以锁标识位是 01
类型指针：共4字节，标识新建的 person 属于哪个类
实例数据：共8字节，定义的 Person 有两个属性，str 和 son，他们对应的类型分别为 String 和 Son，这两个属性每个占4个字节
对齐填充：共4个字节，前三个部分所占大小相加 8+ 4+ 8 = 20，不是8的整数倍，所以得填充4个字节凑齐24字节。描述信息中也有说明： loss due to the next object aligment
MarkWord 的锁信息

前面我们提到对象头 MarkWord 中主要保存有锁信息、GC信息、HashCode。那么我们现在就看下 MarkWord 中的锁信息。锁主要有偏向锁、轻量级锁、重量级锁，锁之间的升级过程我们就不在这里说明了，可以参考《深入理解 Java 虚拟机》，各种锁在 MarkWord 中锁标识位的使用情况如下图所示：



 ![](https://img-blog.csdnimg.cn/20210127220647189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70)
  


图三：对象 MarkWord 字节说明


对照上图，再结合之前图二中对新建对象的内存模型输出结果，结果中表示锁信息的二进制是 001，再对照图三，是一个无锁状态。

接下来再试一种情况，我们知道 HotSpot 虚拟机在启动后有个 4s 的延迟才会对每个新建的对象开启偏向锁，为此，我们先让程序 sleep 5s后再去输出对象的内存模型，修改后的代码如下：

 
```
public class SeeBin {
    public static void main(String[] args) throws InterruptedException {
        //线程sleep 5s，确保虚拟机中偏向锁开启
        Thread.sleep(5000);
        new Thread(new Runnable() {
            @Override
            public void run() {
                Person person = new Person();
                synchronized (person){
                    //当前锁对象第一次被线程获取
                    System.out.println(ClassLayout.parseInstance(person).toPrintable());
                }
            }
        }).start();
 
    }
}
```

 


![](https://i.loli.net/2019/07/19/5d313829b468683360.jpg)
 

在上一段代码中，我们让线程 sleep 5s并新建一个线程去获取锁对象，因为当前只有一个线程去获取这个锁对象，所以虚拟机会把对象头中的标志位设置为偏向锁（101），并将获取这个锁的线程 ID 放到对象的 MarkWord 中。

下面是一些能够模拟重量级锁的代码，就不细说了。

 
```
/**
 * 重量级锁
 */
public class SeeBin {
    static Person person = new Person();
    public static void main(String[] args) throws InterruptedException {
        Thread.sleep(5000);
        //三个线程去竞争访问一个锁对象
        for (int i = 0; i < 3; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    synchronized (person){
                        System.out.println(ClassLayout.parseInstance(person).toPrintable());
                    }
                }
            }).start();
        }
    }
} 
```

 

 

## 3.对象分配

  1.Eden,survivor(from),survivor(to)8：1：1，为了减少送到老年代的对象、设置两个survivor是为了解决碎片化的问题（复制回收算法）

  2.新生代：老年代1：2 eden空间不足进行yougGC，每回收一次年龄加1
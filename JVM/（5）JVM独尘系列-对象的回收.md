# 对象的回收
## 1、垃圾回收算法
垃圾收集算法：（复制算法（年轻代）、标记清除算法、标记整理算法（老年代）、分代收集）

 

### 1.1 标记-清除算法（Mark-Sweep） 
这是最基础的垃圾回收算法，之所以说它是最基础的是因为它最容易实现，思想也是最简单的。标记-清除算法分为两个阶段：标记阶段和清除阶段。标记阶段的任务是标记出所有需要被回收的对象，清除阶段就是回收被标记的对象所占用的空间。具体过程如下图所示：
![](
https://img-blog.csdnimg.cn/img_convert/c8bb4b31f800bddb651ac4eda934bc06.png
)
从图中可以很容易看出标记-清除算法实现起来比较容易，但是有一个比较严重的问题就是容易产生内存碎片，碎片太多可能会导致后续过程中需要为大对象分配空间时无法找到足够的空间而提前触发新的一次垃圾收集动作。  

标记-清除算法采用从根集合（GC Roots）进行扫描，对存活的对象进行标记，标记完毕后，再扫描整个空间中未被标记的对象，进行回收，如下图所示。标记-清除算法不需要进行对象的移动，只需对不存活的对象进行处理，在存活对象比较多的情况下极为高效，但由于标记-清除算法直接回收不存活的对象，因此会造成内存碎片。

优点：
算法简单，可以解决循环引用问题
缺点：
a、回收时应用需要挂起（STW：stop the word）
b、对象多的时候，标记和清除效率不高
c、地址不连续，造成内存碎片
![](
 https://img-blog.csdnimg.cn/img_convert/15d62cf52928dd14f130da2358d7bf23.png)
### 1.2 复制算法(Copying)
为了解决Mark-Sweep算法的缺陷，Copying算法就被提了出来。它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用的内存空间一次清理掉，这样一来就不容易出现内存碎片的问题。具体过程如下图所示：
![]()
https://img-blog.csdnimg.cn/img_convert/2a7918f9f367d10145fd05c2d074e52d.png
这种算法虽然实现简单，运行高效且不容易产生内存碎片，但是却对内存空间的使用做出了高昂的代价，因为能够使用的内存缩减到原来的一半。

很显然，Copying算法的效率跟存活对象的数目多少有很大的关系，如果存活对象很多，那么Copying算法的效率将会大大降低。  

复制算法的提出是为了克服句柄的开销和解决内存碎片的问题。它开始时把堆分成 一个对象 面和多个空闲面， 程序从对象面为对象分配空间，当对象满了，基于copying算法的垃圾 收集就从根集合（GC Roots）中扫描活动对象，并将每个 活动对象复制到空闲面(使得活动对象所占的内存之间没有空闲洞)，这样空闲面变成了对象面，原来的对象面变成了空闲面，程序会在新的对象面中分配内存。

优点：
在存活对象不多的情况下，性能高，能解决内存碎片和java垃圾回收算法之-标记清除中导致的引用更新问题。

缺点：
会造成一部分的内存浪费。不过可以根据实际情况，将内存块大小比例适当调整；如果存活对象的数量比较大，coping的性能会变得很差。

 
![](
https://img-blog.csdnimg.cn/img_convert/48139ee5fd564c20dcbbf4d627c7b843.png
)
### 1.3 标记-整理算法(Mark-compact)
为了解决Copying算法的缺陷，充分利用内存空间，提出了Mark-Compact算法。该算法标记阶段和Mark-Sweep一样，但是在完成标记之后，它不是直接清理可回收对象，而是将存活对象都向一端移动(美团面试题目，记住是完成标记之后，先不清理，先移动再清理回收对象)，然后清理掉端边界以外的内存(美团问过)   

标记-整理算法采用标记-清除算法一样的方式进行对象的标记，但在清除时不同，在回收不存活的对象占用的空间后，会将所有的存活对象往左端空闲空间移动，并更新对应的指针。标记-整理算法是在标记-清除算法的基础上，又进行了对象的移动，因此成本更高，但是却解决了内存碎片的问题。

### 优点：
a、解决了内存碎片问题
b、内存空间更加具有连续性
缺点：
a、压缩阶段移动了可用对象，需要更新引用信息，速度慢，但不占CPU
b、在移动过程中其他线程无法访问堆内存

具体流程见下图：
![](
https://img-blog.csdnimg.cn/img_convert/d4c1fac1f24fd2a99743f674b1bd1210.png
)

### 1.4 分代收集算法 Generational Collection（分代收集）算法   
分代收集算法是目前大部分JVM的垃圾收集器采用的算法。它的核心思想是根据对象存活的生命周期将内存划分为若干个不同的区域。一般情况下将堆区划分为老年代（Tenured Generation）和新生代（Young Generation），在堆区之外还有一个代就是永久代（Permanet Generation）。老年代的特点是每次垃圾收集时只有少量对象需要被回收，而新生代的特点是每次垃圾回收时都有大量的对象需要被回收，那么就可以根据不同代的特点采取最适合的收集算法。

目前大部分垃圾收集器对于新生代都采取Copying算法，因为新生代中每次垃圾回收都要回收大部分对象，也就是说需要复制的操作次数较少，但是实际中并不是按照1：1的比例来划分新生代的空间的，一般来说是将新生代划分为一块较大的Eden空间和两块较小的Survivor空间（一般为8:1:1），每次使用Eden空间和其中的一块Survivor空间，当进行回收时，将Eden和Survivor中还存活的对象复制到另一块Survivor空间中，然后清理掉Eden和刚才使用过的Survivor空间。

而由于老年代的特点是每次回收都只回收少量对象，一般使用的是Mark-Compact算法。
![](
https://img-blog.csdnimg.cn/img_convert/1ebfeb69b5986eefade16d0e70ef5dcb.png
)
### 年轻代（Young Generation）的回收算法 (回收主要以Copying为主)

a) 所有新生成的对象首先都是放在年轻代的。年轻代的目标就是尽可能快速的收集掉那些生命周期短的对象。

b) 新生代内存按照8:1:1的比例分为一个eden区和两个survivor(survivor0,survivor1)区。一个Eden区，两个 Survivor区(一般而言)。大部分对象在Eden区中生成。回收时先将eden区存活对象复制到一个survivor0区，然后清空eden区，当这个survivor0区也存放满了时，则将eden区和survivor0区存活对象复制到另一个survivor1区，然后清空eden和这个survivor0区，此时survivor0区是空的，然后将survivor0区和survivor1区交换，即保持survivor1区为空(美团面试，问的太细，为啥保持survivor1为空，答案：为了让eden和survivor0 交换存活对象)， 如此往复。当Eden没有足够空间的时候就会 触发jvm发起一次Minor GC

c) 当survivor1区不足以存放 eden和survivor0的存活对象时，就将存活对象直接存放到老年代。若是老年代也满了就会触发一次Full GC(Major GC)，也就是新生代、老年代都进行回收。

d) 新生代发生的GC也叫做Minor GC，MinorGC发生频率比较高(不一定等Eden区满了才触发)。

### 年老代（Old Generation）的回收算法（回收主要以Mark-Compact为主）

a) 在年轻代中经历了N次垃圾回收后仍然存活的对象，就会被放到年老代中。因此，可以认为年老代中存放的都是一些生命周期较长的对象。

b) 内存比新生代也大很多(大概比例是1:2)，当老年代内存满时触发Major GC即Full GC，Full GC发生频率比较低，老年代对象存活时间比较长，存活率标记高。

### 持久代（Permanent Generation）(也就是方法区)的回收算法

  用于存放静态文件，如Java类、方法等。持久代对垃圾回收没有显著影响，但是有些应用可能动态生成或者调用一些class，例如Hibernate 等，在这种时候需要设置一个比较大的持久代空间来存放这些运行过程中新增的类。持久代也称方法区，具体的回收可参见上文2.5节。

  再写一遍：

        方法区存储内容是否需要回收的判断可就不一样咯。方法区主要回收的内容有：废弃常量和无用的类。对于废弃常量也可通过引用的可达性来判断，但是对于无用的类则需要同时满足下面3个条件：

该类所有的实例都已经被回收，也就是Java堆中不存在该类的任何实例；
加载该类的ClassLoader已经被回收；
该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。
 

### 新生代和老年代的区别(阿里面试官的题目)： 

**所谓的新生代和老年代是针对于分代收集算法来定义的，新生代又分为Eden和Survivor两个区。加上老年代就这三个区。数据会首先分配到Eden区 当中（当然也有特殊情况，如果是大对象那么会直接放入到老年代（大对象是指需要大量连续内存空间的java对象）。），当Eden没有足够空间的时候就会 触发jvm发起一次Minor GC。如果对象经过一次Minor GC还存活，并且又能被Survivor空间接受，那么将被移动到Survivor空 间当中。并将其年龄设为1，对象在Survivor每熬过一次Minor GC，年龄就加1，当年龄达到一定的程度（默认为15）时，就会被晋升到老年代 中了，当然晋升老年代的年龄是可以设置的。如果老年代满了就执行：Full GC 因为不经常执行，因此采用了 Mark-Compact算法清理

其实新生代和老年代就是针对于对象做分区存储，更便于回收等等

## 2、对象能否回收
对象是否存活（引用计数法、可达性分析）

###  2.1 引用计数算法
2.1.1 算法分析

  引用计数是垃圾收集器中的早期策略。在这种方法中，堆中每个对象实例都有一个引用计数。当一个对象被创建时，就将该对象实例分配给一个变量，该变量计数设置为1。当任何其它变量被赋值为这个对象的引用时，计数加1（a = b,则b引用的对象实例的计数器+1），但当一个对象实例的某个引用超过了生命周期或者被设置为一个新值时，对象实例的引用计数器减1。任何引用计数器为0的对象实例可以被当作垃圾收集。当一个对象实例被垃圾收集时，它引用的任何对象实例的引用计数器减1。

2.1.2 优缺点

优点：引用计数收集器可以很快的执行，交织在程序运行中。对程序需要不被长时间打断的实时环境比较有利。

缺点：无法检测出循环引用。如父对象有一个对子对象的引用，子对象反过来引用父对象。这样，他们的引用计数永远不可能为0。

2.2 可达性分析算法
  可达性分析算法是从离散数学中的图论引入的，程序把所有的引用关系看作一张图，从一个节点GC ROOT开始，寻找对应的引用节点，找到这个节点以后，继续寻找这个节点的引用节点，当所有的引用节点寻找完毕之后，剩余的节点则被认为是没有被引用到的节点，即无用的节点，无用的节点将会被判定为是可回收的对象。
![](
https://img-blog.csdnimg.cn/img_convert/6a1cc8131547afffdf531440484555b3.png)

 在Java语言中，可作为GC Roots的对象包括下面几种：（京东）

  a) 虚拟机栈中引用的对象（栈帧中的本地变量表）；

  b) 方法区中类静态属性引用的对象；

  c) 方法区中常量引用的对象；

  d) 本地方法栈中JNI（Native方法）引用的对象。

 

这个算法的基本思路就是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），当一个对象到GC Roots没有任何引用链相连（用图论的话来说，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。如图所示，对象object 5、object 6、object 7虽然互相有关联，但是它们到GC Roots是不可达的，所以它们将会被判定为是可回收的对象。 

现在问题来了，可达性分析算法会不会出现对象间循环引用问题呢？答案是肯定的，那就是不会出现对象间循环引用问题。GC Root在对象图之外，是特别定义的“起点”，不可能被对象图内的对象所引用。

对象生存还是死亡(To Die Or Not To Die)

即使在可达性分析算法中不可达的对象，也并非是“非死不可”的，这时候它们暂时处于“缓刑”阶段，要真正宣告一个对象死亡，至少要经历两次标记过程：如果对象在进行可达性分析后发现没有与GC Roots相连接的引用链，那它将会被第一次标记并且进行一次筛选，筛选的条件是此对象是否有必要执行finapze()方法。当对象没有覆盖finapze()方法，或者finapze()方法已经被虚拟机调用过，虚拟机将这两种情况都视为“没有必要执行”。程序中可以通过覆盖finapze()来一场"惊心动魄"的自我拯救过程，但是，这只有一次机会呦。
```
/**
 * 此代码演示了两点：
 * 1.对象可以在被GC时自我拯救。
 * 2.这种自救的机会只有一次，因为一个对象的finapze()方法最多只会被系统自动调用一次 
 * @author zzm
 */
public class FinapzeEscapeGC {
  
 public static FinapzeEscapeGC SAVE_HOOK = null;
  
 pubpc void isApve() {
  System.out.println("yes, i am still apve :)");
 }
  
 @Override
 protected void finapze() throws Throwable {
  super.finapze();
  System.out.println("finapze mehtod executed!");
  FinapzeEscapeGC.SAVE_HOOK = this;
 }
  
 pubpc static void main(String[] args) throws Throwable {
  SAVE_HOOK = new FinapzeEscapeGC();
  
  //对象第一次成功拯救自己
  SAVE_HOOK = null;
  System.gc();
  //因为finapze方法优先级很低，所以暂停0.5秒以等待它
  Thread.sleep(500);
  if (SAVE_HOOK != null) {
SAVE_HOOK.isApve();
  } else {
System.out.println("no, i am dead :(");
  }
  
  //下面这段代码与上面的完全相同，但是这次自救却失败了
  SAVE_HOOK = null;
  System.gc();
  //因为finapze方法优先级很低，所以暂停0.5秒以等待它
  Thread.sleep(500);
  if (SAVE_HOOK != null) {
SAVE_HOOK.isApve();
  } else {
System.out.println("no, i am dead :(");
  }
 }
}
 运行结果为：

finapze mehtod executed! 
yes, i am still apve :)
no, i am dead :(
  
```
### 2.3 对象死亡（被回收）前的最后一次挣扎

  即使在可达性分析算法中不可达的对象，也并非是“非死不可”，这时候它们暂时处于“缓刑”阶段，要真正宣告一个对象死亡，至少要经历两次标记过程。

  第一次标记：如果对象在进行可达性分析后发现没有与GC Roots相连接的引用链，那它将会被第一次标记；

  第二次标记：第一次标记后接着会进行一次筛选，筛选的条件是此对象是否有必要执行finalize()方法。在finalize()方法中没有重新与引用链建立关联关系的，将被进行第二次标记。

  第二次标记成功的对象将真的会被回收，如果对象在finalize()方法中重新与引用链建立了关联关系，那么将会逃离本次回收，继续存活。猿们还跟的上吧，嘿嘿。

### 2.4 元空间如何判断是否需要回收

  元空间存储内容是否需要回收的判断可就不一样咯。元空间主要回收的内容有：废弃常量和无用的类。对于废弃常量也可通过引用的可达性来判断，但是对于无用的类则需要同时满足下面3个条件：

该类所有的实例都已经被回收，也就是Java堆中不存在该类的任何实例；
加载该类的ClassLoader已经被回收；
该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。
关于类加载的原理，也是阿里面试的主角，面试官也问过我比如：能否自己定义String，答案是不行，因为jvm在加载类的时候会执行双亲委派，

 2.5何时进入老年代

 1.survivor放不下，

 2.对象存活年龄达到阈值；

3.动态年龄判断（相同年龄对象大小之和大于survivor一半以上，年龄大于这个值的对象都会进入老年代）；

4.大对象

### 2.6FULLGC

1.老年代内存使用率达到阈值

2.空间担保（YGC之前，检查老年代最大可用连续空间是否大于新生代所有对象之和，小于则YGC不安全，如果不允许担保失败，进行fullGC，允许，检查老年代是否大于历次晋升平均值大小，小于则触发fullGC,）

3.元空间不足进行扩容，达到指定值，触发FGC

4.system.gc()

       原因：

系统一次性加载过多大对象
内存泄漏，频繁创建大对象，无法被回收
生成长生命周期对象
参数设置问题
调用GC方法
## 3.垃圾回收器
 

### 一、常见垃圾收集器
现在常见的垃圾收集器有如下几种：

新生代收集器：

* Serial
* ParNew
* Parallel Scavenge

老年代收集器：

* Serial Old
* CMS
* Parallel Old

堆内存垃圾收集器：G1

每种垃圾收集器之间有连线，表示他们可以搭配使用。
![](
https://img-blog.csdnimg.cn/20190331203408467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
### 二、新生代垃圾收集器
* （1）Serial 收集器

Serial 是一款用于新生代的单线程收集器，采用复制算法进行垃圾收集。Serial 进行垃圾收集时，不仅只用一条线程执行垃圾收集工作，它在收集的同时，所有的用户线程必须暂停（Stop The World）。

就比如妈妈在家打扫卫生的时候，肯定不会边打扫边让儿子往地上乱扔纸屑，否则一边制造垃圾，一遍清理垃圾，这活啥时候也干不完。

如下是 Serial 收集器和 Serial Old 收集器结合进行垃圾收集的示意图，当用户线程都执行到安全点时，所有线程暂停执行，Serial 收集器以单线程，采用复制算法进行垃圾收集工作，收集完之后，用户线程继续开始执行。
![](
https://img-blog.csdnimg.cn/20190331203435401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
适用场景：Client 模式（桌面应用）；单核服务器。

可以用 -XX:+UserSerialGC 来选择 Serial 作为新生代收集器。

* （2）ParNew 收集器

ParNew 就是一个 Serial 的多线程版本，其它与Serial并无区别。ParNew 在单核 CPU 环境并不会比 Serial 收集器达到更好的效果，它默认开启的收集线程数和 CPU 数量一致，可以通过 -XX:ParallelGCThreads 来设置垃圾收集的线程数。

如下是 ParNew 收集器和 Serial Old 收集器结合进行垃圾收集的示意图，当用户线程都执行到安全点时，所有线程暂停执行，ParNew 收集器以多线程，采用复制算法进行垃圾收集工作，收集完之后，用户线程继续开始执行。
![](
https://img-blog.csdnimg.cn/20190331203455553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
适用场景：多核服务器；与 CMS 收集器搭配使用。当使用 -XX:+UserConcMarkSweepGC 来选择 CMS 作为老年代收集器时，新生代收集器默认就是 ParNew，也可以用 -XX:+UseParNewGC 来指定使用 ParNew 作为新生代收集器。

* （3）Parallel Scavenge 收集器

Parallel Scavenge 也是一款用于新生代的多线程收集器，与 ParNew 的不同之处是ParNew 的目标是尽可能缩短垃圾收集时用户线程的停顿时间，Parallel Scavenge 的目标是达到一个可控制的吞吐量。

吞吐量就是 CPU 执行用户线程的的时间与 CPU 执行总时间的比值【吞吐量 = 运行用户代代码时间/（运行用户代码时间+垃圾收集时间）】，比如虚拟机一共运行了 100 分钟，其中垃圾收集花费了 1 分钟，那吞吐量就是 99% 。比如下面两个场景，垃圾收集器每 100 秒收集一次，每次停顿 10 秒，和垃圾收集器每 50 秒收集一次，每次停顿时间 7 秒，虽然后者每次停顿时间变短了，但是总体吞吐量变低了，CPU 总体利用率变低了。


可以通过 -XX:MaxGCPauseMillis 来设置收集器尽可能在多长时间内完成内存回收，可以通过 -XX:GCTimeRatio 来精确控制吞吐量。

如下是 Parallel 收集器和 Parallel Old 收集器结合进行垃圾收集的示意图，在新生代，当用户线程都执行到安全点时，所有线程暂停执行，ParNew 收集器以多线程，采用复制算法进行垃圾收集工作，收集完之后，用户线程继续开始执行；在老年代，当用户线程都执行到安全点时，所有线程暂停执行，Parallel Old 收集器以多线程，采用标记整理算法进行垃圾收集工作。
![](
https://img-blog.csdnimg.cn/20190331203525465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
适用场景：注重吞吐量，高效利用 CPU，需要高效运算且不需要太多交互。

可以使用 -XX:+UseParallelGC 来选择 Parallel Scavenge 作为新生代收集器，jdk7、jdk8 默认使用 Parallel Scavenge 作为新生代收集器。

### 三、老年代垃圾收集器
* （1）Serial Old 收集器

Serial Old 收集器是 Serial 的老年代版本，同样是一个单线程收集器，采用标记-整理算法。

如下图是 Serial 收集器和 Serial Old 收集器结合进行垃圾收集的示意图：
![](
https://img-blog.csdnimg.cn/20190331203546455.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
适用场景：Client 模式（桌面应用）；单核服务器；与 Parallel Scavenge 收集器搭配；作为 CMS 收集器的后备预案。

* （2）CMS(Concurrent Mark Sweep) 收集器

CMS 收集器是一种以最短回收停顿时间为目标的收集器，以 “ 最短用户线程停顿时间 ” 著称。整个垃圾收集过程分为 4 个步骤：

① 初始标记：标记一下 GC Roots 能直接关联到的对象，速度较快。

② 并发标记：进行 GC Roots Tracing，标记出全部的垃圾对象，耗时较长。

③ 重新标记：修正并发标记阶段引用户程序继续运行而导致变化的对象的标记记录，耗时较短。

④ 并发清除：用标记-清除算法清除垃圾对象，耗时较长。

整个过程耗时最长的并发标记和并发清除都是和用户线程一起工作，所以从总体上来说，CMS 收集器垃圾收集可以看做是和用户线程并发执行的。
![](
https://img-blog.csdnimg.cn/20190331203605892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
* CMS 收集器也存在一些缺点：

对 CPU 资源敏感：默认分配的垃圾收集线程数为（CPU 数+3）/4，随着 CPU 数量下降，占用 CPU 资源越多，吞吐量越小

无法处理浮动垃圾：在并发清理阶段，由于用户线程还在运行，还会不断产生新的垃圾，CMS 收集器无法在当次收集中清除这部分垃圾。同时由于在垃圾收集阶段用户线程也在并发执行，CMS 收集器不能像其他收集器那样等老年代被填满时再进行收集，需要预留一部分空间提供用户线程运行使用。当 CMS 运行时，预留的内存空间无法满足用户线程的需要，就会出现 “ Concurrent Mode Failure ”的错误，这时将会启动后备预案，临时用 Serial Old 来重新进行老年代的垃圾收集。

因为 CMS 是基于标记-清除算法，所以垃圾回收后会产生空间碎片，可以通过 -XX:UserCMSCompactAtFullCollection 开启碎片整理（默认开启），在 CMS 进行 Full GC 之前，会进行内存碎片的整理。还可以用 -XX:CMSFullGCsBeforeCompaction 设置执行多少次不压缩（不进行碎片整理）的 Full GC 之后，跟着来一次带压缩（碎片整理）的 Full GC。

适用场景：重视服务器响应速度，要求系统停顿时间最短。可以使用 -XX:+UserConMarkSweepGC 来选择 CMS 作为老年代收集器。

* （3）Parallel Old 收集器

Parallel Old 收集器是 Parallel Scavenge 的老年代版本，是一个多线程收集器，采用标记-整理算法。可以与 Parallel Scavenge 收集器搭配，可以充分利用多核 CPU 的计算能力。
![](
https://img-blog.csdnimg.cn/20190331203624531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
适用场景：与Parallel Scavenge 收集器搭配使用；注重吞吐量。jdk7、jdk8 默认使用该收集器作为老年代收集器，使用 -XX:+UseParallelOldGC 来指定使用 Paralle Old 收集器。

### 四、新生代和老年代垃圾收集器
* G1 收集器

G1 收集器是 jdk1.7 才正式引用的商用收集器，现在已经成为 jdk9 默认的收集器。前面几款收集器收集的范围都是新生代或者老年代，G1 进行垃圾收集的范围是整个堆内存，它采用 “ 化整为零 ” 的思路，把整个堆内存划分为多个大小相等的独立区域（Region），在 G1 收集器中还保留着新生代和老年代的概念，它们分别都是一部分 Region，如下图：
![](
https://img-blog.csdnimg.cn/20190331203642962.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
每一个方块就是一个区域，每个区域可能是 Eden、Survivor、老年代，每种区域的数量也不一定。JVM 启动时会自动设置每个区域的大小（1M ~ 32M，必须是 2 的次幂），最多可以设置 2048 个区域（即支持的最大堆内存为 32M*2048 = 64G），假如设置 -Xmx8g -Xms8g，则每个区域大小为 8g/2048=4M。

为了在 GC Roots Tracing 的时候避免扫描全堆，在每个 Region 中，都有一个 Remembered Set 来实时记录该区域内的引用类型数据与其他区域数据的引用关系（在前面的几款分代收集中，新生代、老年代中也有一个 Remembered Set 来实时记录与其他区域的引用关系），在标记时直接参考这些引用关系就可以知道这些对象是否应该被清除，而不用扫描全堆的数据。

G1 收集器可以 “ 建立可预测的停顿时间模型 ”，它维护了一个列表用于记录每个 Region 回收的价值大小（回收后获得的空间大小以及回收所需时间的经验值），这样可以保证 G1 收集器在有限的时间内可以获得最大的回收效率。

如下图所示，G1 收集器收集器收集过程有初始标记、并发标记、最终标记、筛选回收，和 CMS 收集器前几步的收集过程很相似：
![](
https://img-blog.csdnimg.cn/20190331203700732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzIyODgxNA==,size_16,color_FFFFFF,t_70)
① 初始标记：标记出 GC Roots 直接关联的对象，这个阶段速度较快，需要停止用户线程，单线程执行。

② 并发标记：从 GC Root 开始对堆中的对象进行可达新分析，找出存活对象，这个阶段耗时较长，但可以和用户线程并发执行。

③ 最终标记：修正在并发标记阶段引用户程序执行而产生变动的标记记录。

④ 筛选回收：筛选回收阶段会对各个 Region 的回收价值和成本进行排序，根据用户所期望的 GC 停顿时间来指定回收计划（用最少的时间来回收包含垃圾最多的区域，这就是 Garbage First 的由来——第一时间清理垃圾最多的区块），这里为了提高回收效率，并没有采用和用户线程并发执行的方式，而是停顿用户线程。

适用场景：要求尽可能可控 GC 停顿时间；内存占用较大的应用。可以用 -XX:+UseG1GC 使用 G1 收集器，jdk9 默认使用 G1 收集器。

###  五、JVM垃圾收集器总结
CMS(标记清除--初始标记（停顿）、并发标记、重新标记（停顿）、并发清除)

优点：最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作

缺点：因为并发阶段多线程占据 CPU 资源，如果 CPU 资源不足，效率会明显降低、清理阶段用户线程还在运行，产生浮动垃圾、产生空间碎片

G1（标记整理--初始标记（停顿）、并发标记、最终标记（停顿）、筛选回收）

优点：不会产生内存碎片 

本文主要介绍了JVM中的垃圾回收器，主要包括串行回收器、并行回收器以及CMS回收器、G1回收器。他们各自都有优缺点，通常来说你需要根据你的业务，进行基于垃圾回收器的性能测试，然后再做选择。下面给出配置回收器时，经常使用的参数：

-XX:+UseSerialGC：在新生代和老年代使用串行收集器

-XX:+UseParNewGC：在新生代使用并行收集器

-XX:+UseParallelGC ：新生代使用并行回收收集器，更加关注吞吐量

-XX:+UseParallelOldGC：老年代使用并行回收收集器

-XX:ParallelGCThreads：设置用于垃圾回收的线程数

-XX:+UseConcMarkSweepGC：新生代使用并行收集器，老年代使用CMS+串行收集器

-XX:ParallelCMSThreads：设定CMS的线程数量

-XX:+UseG1GC：启用G1垃圾回收器
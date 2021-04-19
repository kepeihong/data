调优
1、监控工具
jps命令
作用：查看所有的java进程
用法： jps

jstat命令
![](https://img-blog.csdnimg.cn/img_convert/ab18a3ac8d8d41510fe248514783c2a2.png)

程序中有大对象，GC没办法回收，所以E和O特别高，会影响到代码的其他接口

作用：可查看堆的使用情况及垃圾回收情况
用法： jstat –gcutil pid
```
jstat -gcutil 397116--总结垃圾回收统计
S0：幸存1区当前使用比例
S1：幸存2区当前使用比例
E：伊甸园区使用比例
O：老年代使用比例
M：元数据区使用比例
CCS：压缩使用比例
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间
```
```
jstat -gc 397116
S0C：第一个幸存区的大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小
OC：老年代大小
OU：老年代使用大小
MC：方法区大小
MU：方法区使用大小
CCSC:压缩类空间大小
CCSU:压缩类空间使用大小
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间
```
jmap命令
作用：可打印当前内存存储快照
用法： jmap -dump:format=b,file=#输出dump地址 pid

jstack命令
作用：栈信息查看和输出
用法： jstack pid >#输出文件名称
jconsole工具
JDK自带工具，可查看本地及远程的所有jvm信息，含类、各种内存、线程等信息

![](https://img-blog.csdnimg.cn/img_convert/57573d81416eeb512c5d7ba342c9b815.png
)

![](https://img-blog.csdnimg.cn/img_convert/d648f44d45cd184e30f1b85877de4772.png
)

jvisualvm工具
JDK自带工具，查看本地及远程的所有jvm信息，还能导入分析堆栈日志信息，比jconsole更加直观
![]( https://img-blog.csdnimg.cn/img_convert/a4aa94a425a07d1cda6d9a18dfc9c8ed.png
)



2、调优案例
1、JVM常见死锁问题产生原因和多种诊断方式
产生原因:互相等待对方的锁释

问题定位

方法一、导出栈信息后定位

a、使用jps查看所有的java pid
b、jstack pid >aaa.log--------------将pid的栈信息输出到当前目录的aaa.log
搜索deadlock，找到对应锁信息，以及等待的锁信息
![](https://img-blog.csdnimg.cn/img_convert/ccd7564b1768eaa6b3424583fcd392ae.png)
![](https://img-blog.csdnimg.cn/img_convert/55a9e480f6c35d6830a402c20880c967.png)



方法二、使用JDK自带的jvisualvm工具直接检测

![](https://img-blog.csdnimg.cn/img_convert/91cd7e334780d44592213f99fe5a2e33.png)
![](https://img-blog.csdnimg.cn/img_convert/5d7161d8c5ac1b51376fd035f3eed1c6.png
)



方法三、使用JDK自带的jconsole工具直接检测
![](https://img-blog.csdnimg.cn/img_convert/d33e9ea2c05df25792acc00bee198c33.png)


2、服务器CPU飙升为100%问题排查
1.定位哪个程序占用的CPU较高

linux命令：top
windows：任务管理器
![](https://img-blog.csdnimg.cn/img_convert/b9079f5e40186e79553c8632d5bb7f1c.png)


2.栈信息输出

命令格式：jstack pid > 文件信息
eg：jstack 5115 > a.txt

3.定位哪一个线程占用率高

常用命令：按shift+p可排序
ps -mp pid -o THREAD,tid,time
ps -Lfp pid
top -H ----直接查看高CPU的线程

 

 

4.线程ID转为16进制,访问转换地址

将子线程TID的5158做进制转换为16进制：1426

5.通过16进制的线程ID在栈信息中查找定位代码行

 ![](https://img-blog.csdnimg.cn/img_convert/9b9fe5617a8809bedc2091685e5800d3.png)



```
public String loop() {
    boolean b = true;
    while (b) {
    }
    return "123";
}
```
CPU飙升问题产生的背景
```
1、代码中存在死循环
2、定时任务跑批量
3、tomcat高并发项目的时候，所有线程都处在运行状态，消耗CPU资源
4、Redis的端口6379被注入挖矿程序
5、分布式锁的重试机制
a、乐观锁：能够保证用户线程一直在用户态，缺点是消耗CPU的资源
b、CAS自旋锁
```
如何避免CPU飙升问题
```
1、检查代码的死循环情况
2、定时任务项目要喝业务逻辑项目分开部署
a、降低业务逻辑项目CPU资源消耗
b、更好实现定时分片执行
3、接口比较耗时的代码不要写成同步，改为使用mq
4、对服务器接口实现限流、熔断和降级
5、端口号不要随意放开，要结合nginx、LVS等
6、写自旋锁一定要控制死循环次数
tips:预警系统很重要
```
3、调优指标
```
1、整体评估指标说明

1.吞吐量: 运行用户代码占总时间的比例
总运行时间：用户线程程序的运行时间(100s)+GC内存回收的时间 (1s)
比如程序运行时间100s/内存回收时间 垃圾回收1s 则吞吐量为100/101=99%

2.GC负荷：与吞吐量相反，指应用花在GC上的时间百分比
上例GC负荷为：1/101=1%

3.暂停时间：应用线程花在GC stop-the-world 的时间
暂时时间越小越好

4.GC频率：次数/GC频率越多，stw暂停时间越短；GC回收频率次数越少、stw暂停时间越长

5.反应速度：从一个对象变成垃圾道这个对象被回收的时间
吞吐量优先的收集器：Parallel并行收集器【Jdk8默认收集器】
响应时间优先的收集器：CMS（老年代）/ParNew(新生代)-注重stw时间越少
G1/ZGC同时注重吞吐量和响应时间优先
```
## 2、JVM调优方案
优化核心思路：
* 1、通过堆内存设置减少老年代垃圾回收的次数
* 2、配置垃圾回收器，减少STW的时间

### 1.避免用户线程暂停时间STW比较短

a.堆内存空间一定要充足，垃圾回收和最大堆内存无关，只和初始内存有关。

b.项目启动堆内存初始值与最大值一定保持一致，可减少垃圾回收的次数，提高吞吐量;

c.不建议调用System.gc()，容易造成STW;

d.不要在堆内存中存放大对象和全局变量，容易触发fullgc

e.合理根据项目堆内存情况，选择收集器

### 2.合理设定堆的初始大小和选择合理的垃圾收集器

a.起步阶段的个人网站,建议堆内存1GB 可以串行SerialGC,建议使用并行Parallel GC

b.有一定访问量的网站或APP,建议堆内存2g 建议使用Parallel GC

c.并发适中的APP或普通数据处理,建议堆内存4g 老年代CMS/新生代parnew

d.适用于并发要求较高的APP,建议堆内存8G（要16G的可以集群）建议G1收集器 注重低延迟和吞吐量

![](https://img-blog.csdnimg.cn/img_convert/9182be5d88211b70c8898dc9a8cb7d42.png)


必填参数
```
-Xmx 堆最大可用值 测试结果：默认4G----物理内存的1/4
-Xms 堆初始值 测试结果：最大内存的1/16-----物理内存的1/64
-XX:+HeapDumpOnOutOfMemoryError 内存溢出的时候打印内存快照
-XX:HeapDumpPath=hdpserver_oom.hprof 内存溢出的时候内存快照保存路径

tips：
a、-Xmx和-Xms在内存不大的时候建议相同，内存很大的时候，建议配成2:1
b、内存溢出的快照配置很可能几个月不出现，但建议配置上，方便问题出现时的排查
c、垃圾回收器在JDK8之前建议用CMS，JDK8及之后建议用G1。

```
![](https://img-blog.csdnimg.cn/img_convert/9182be5d88211b70c8898dc9a8cb7d42.png)


优化参数
```
-XX:+PrintGC 每次触发GC的时候打印相关日志
-XX:+PrintGCDetails 更详细的GC日志
堆设置
-Xmn 新生代堆最大可用值 默认是堆的1/3，官方推荐配置为整个堆的3/8
-XX:NewRatio 配置新生代与老年代占比 默认1:2 建议值：1:2或1:3 -XX:NewRatio=3
-XX:SurvivorRatio 用来设置新生代中eden空间和from/to空间的比例=eden/from=den/to 默认8:1
-XX:PermSize 初始化永久内存区域大小，默认4M，超出的话可能出现PermGen space错误。
-XX:MaxPermSize 设置永久内存区域最大大小
-XX:NewSize 作用跟-XX:NewRatio相似，不同的是精确的数值
-XX:MaxNewSize 设置最大Java新对象生产堆内存，
NewSize和MaxNewSize最好设成一致，数值都是1024的整数倍并且大于1MB。
-XX:MaxTenuringThreshold 设置垃圾最大年龄
-XX:GCTimeRatio 设置垃圾回收时间占程序运行时间的百分比，垃圾回收时间占程序运行时间百分比的公式为1/(1+n) ，如果n=19表示java可以用5%的时间来做垃圾回收，1/(1+19)=1/20=5%。
-Xss 我们线程栈空间大小 –Xss1m，jdk5.0前是256k---配置不适合太高
1.8元空间设置大小
-XX:MetaspaceSize 初始空间大小，达到该值就会触发垃圾收集进行类型卸载，同时GC会对该值进行调整：如果释放了大量的空间，就适当降低该值；如果释放了很少的空间，那么在不超过MaxMetaspaceSize时，适当提高该值。
-XX:MaxMetaspaceSize 最大空间，默认是没有限制的。
```
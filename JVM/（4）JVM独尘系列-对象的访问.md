# 对象的访问
## 对象访问：句柄（reference句柄地址）、直接指针(reference对象地址)

 

### 句柄访问
Java 堆中会分配一块内存作为句柄池。reference 存储的是句柄地址。详情见图。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZWJmMDBlZDI2YzM1YWVmZDkzZDVhM2EzNmIzYjE2MTM_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ
)
 

### 直接指针访问
reference 中直接存储对象地址



![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGU2OTI0YjZlOWQ1NzYxMDViYTI0NzAwZjFmMzU3ZjQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)
  

### 比较：
使用句柄的最大好处是 reference 中存储的是稳定的句柄地址，在对象移动(GC)是只改变实例数据指针地址，reference 自身不需要修改。直接指针访问的最大好处是速度快，节省了一次指针定位的时间开销。如果是对象频繁 GC 那么句柄方法好，如果是对象频繁访问则直接指针访问好。

 

### 内存间交互操作：
（1）lock（锁定）：作用于主内存的变量，把一个变量标识为一条线程独占状态。

（2）unlock（解锁）：作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。

（3）read（读取）：作用于主内存变量，把主内存的一个变量读取到工作内存中。

（4）load（载入）：作用于工作内存，把read操作读取到工作内存的变量载入到工作内存的变量副本中

（5）use（使用）：作用于工作内存的变量，把工作内存中的变量值传递给一个执行引擎。

（6）assign（赋值）：作用于工作内存的变量。把执行引擎接收到的值赋值给工作内存的变量。

（7）store（存储）：把工作内存的变量的值传递给主内存

（8）write（写入）：把store操作的值入到主内存的变量中
### 注意：
（1）不允许read、load、store、write操作之一单独出现

（2）不允许一个线程丢弃assgin操作

（3）不允许一个线程不经过assgin操作，就把工作内存中的值同步到主内存中

（4）一个新的变量只能在主内存中生成

（5）一个变量同一时刻只允许一条线程对其进行lock操作。但lock操作可以被同一条线程执行多次，只有执行相同次数的unlock操作，变量才会解锁

（6）如果对一个变量进行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或者assgin操作初始化变量的值。

（7）如果一个变量没有被锁定，不允许对其执行unlock操作，也不允许unlock一个被其他线程锁定的变量

（8）对一个变量执行unlock操作之前，需要将该变量同步回主内存中
# 对锁的一些认知 有哪些锁

## 同一进程

### 重入锁
使用 `ReentrantLock` 获取锁的时候会判断当前线程是否为获取锁的线程，如果是则将同步的状态 +1 ,释放锁的时候则将状态 -1。只有将同步状态的次数置为 0 的时候才会最终释放锁。

### 读写锁
使用 `ReentrantReadWriteLock` ,同时维护一对锁：读锁和写锁。当写线程访问时则其他所有锁都将阻塞，读线程访问时则不会。通过读写锁的分离可以很大程度的提高并发量和吞吐量。


## 不同进程

分布式锁：

### 基于数据库
可以创建一张表，将其中的某个字段设置为`唯一索引`，当多个请求过来的时候只有新建记录成功的请求才算获取到锁，当使用完毕删除这条记录的时候即释放锁。

存在的问题:
- 数据库单点问题，挂了怎么办？
- 不是重入锁，同一进程无法在释放锁之前再次获得锁，因为数据库中已经存在了一条记录了。
- 锁是非阻塞的，一旦 `insert` 失败则会立即返回，并不会进入阻塞队列只能下一次再次获取。
- 锁没有失效时间，如果那个进程解锁失败那就没有请求可以再次获取锁了。

解决方案:
- 数据库切换为主从，不存在单点。
- 在表中加入一个同步状态字段，每次获取锁的是加 1 ，释放锁的时候`-1`，当状态为 0 的时候就删除这条记录，即释放锁。
- 非阻塞的情况可以用 `while` 循环来实现，循环的时候记录时间，达到 X 秒记为超时，`break`。
- 可以开启一个定时任务每隔一段时间扫描找出多少 X 秒都没有被删除的记录，主动删除这条记录。

### 基于 Redis

使用 `setNX(key) setEX(timeout)` 命令，只有在该 `key` 不存在的时候创建这个 `key`，就相当于获取了锁。由于有超时时间，所以过了规定时间会自动删除，这样也可以避免死锁。


### 基于 ZK
主要基于zk的临时节点和watch机制，但是临时节点会产生羊群效应，参考AQS中实现clh队列，由后续节点监听前驱节点，避免羊群效应产生。

# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 
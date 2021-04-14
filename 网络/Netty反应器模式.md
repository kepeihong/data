![](https://i.loli.net/2019/07/19/5d313935e4ef253589.jpg)


## 传统 IO

在 Netty 以及 NIO 出现之前，我们写 IO 应用其实用的都是用 `java.io.*` 下所提供的包。  


比如下面的伪代码：

```
ServeSocket serverSocket = new ServeSocket(8080);
Socket socket = serverSocket.accept() ;
BufferReader in = .... ;

String request ;
 
while((request = in.readLine()) != null){
	new Thread(new Task()).start()
}
```

<!--more-->

大概是这样，其实主要想表达的是：**这样一个线程只能处理一个连接**。

如果是 100 个客户端连接那就得开 100 个线程，1000 那就得 1000 个线程。

要知道线程资源非常宝贵，每次的创建都会带来消耗，而且每个线程还得为它分配对应的栈内存。

即便是我们给 JVM 足够的内存，大量线程所带来的上下文切换也是受不了的。

> 并且传统 IO 是阻塞模式，每一次的响应必须的是发起 IO 请求，处理请求完成再同时返回，直接的结果就是性能差，吞吐量低。

## Reactor 模型

因此业界常用的高性能 IO 模型是 `Reactor`。

它是一种异步、非阻塞的事件驱动模型。

通常也表现为以下三种方式：

### 单线程

![](https://i.loli.net/2019/07/19/5d3139369e57d74023.jpg)

从图中可以看出：

它是由一个线程来接收客户端的连接，并将该请求分发到对应的事件处理 handler 中，整个过程完全是异步非阻塞的；并且完全不存在共享资源的问题。所以理论上来说吞吐量也还不错。

> 但由于是一个线程，对多核 CPU 利用率不高，一旦有大量的客户端连接上来性能必然下降，甚至会有大量请求无法响应。
> 最坏的情况是一旦这个线程哪里没有处理好进入了死循环那整个服务都将不可用！

### 多线程

![](https://i.loli.net/2019/07/19/5d313937667e941981.jpg)

因此产生了多线程模型。

其实最大的改进就是将原有的事件处理改为了多线程。

可以基于 Java 自身的线程池实现，这样在大量请求的处理上性能提示是巨大的。

虽然如此，但理论上来说依然有一个地方是单点的；那就是处理客户端连接的线程。

因为大多数服务端应用或多或少在连接时都会处理一些业务，如鉴权之类的，当连接的客户端越来越多时这一个线程依然会存在性能问题。

于是又有了下面的线程模型。

### 主从多线程

![](https://i.loli.net/2019/07/19/5d313937f2dbd55910.jpg)

该模型将客户端连接那一块的线程也改为多线程，称为主线程。

同时也是多个子线程来处理事件响应，这样无论是连接还是事件都是高性能的。


## Netty 实现

以上谈了这么多其实 Netty 的线程模型与之的类似。

我们回到之前 [SpringBoot 整合长连接心跳机制](https://crossoverjie.top/2018/05/24/netty/Netty(1)TCP-Heartbeat/) 中的服务端代码：

```
    private EventLoopGroup boss = new NioEventLoopGroup();
    private EventLoopGroup work = new NioEventLoopGroup();


    /**
     * 启动 Netty
     *
     * @return
     * @throws InterruptedException
     */
    @PostConstruct
    public void start() throws InterruptedException {

        ServerBootstrap bootstrap = new ServerBootstrap()
                .group(boss, work)
                .channel(NioServerSocketChannel.class)
                .localAddress(new InetSocketAddress(nettyPort))
                //保持长连接
                .childOption(ChannelOption.SO_KEEPALIVE, true)
                .childHandler(new HeartbeatInitializer());

        ChannelFuture future = bootstrap.bind().sync();
        if (future.isSuccess()) {
            LOGGER.info("启动 Netty 成功");
        }
    }
```

其实这里的 boss 就相当于 Reactor 模型中处理客户端连接的线程池。

work 自然就是处理事件的线程池了。

那么如何来实现上文的三种模式呢？其实也很简单：


单线程模型：

```
private EventLoopGroup group = new NioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap()
                .group(group)
                .childHandler(new HeartbeatInitializer());
```

多线程模型：

```
private EventLoopGroup boss = new NioEventLoopGroup(1);
private EventLoopGroup work = new NioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap()
                .group(boss,work)
                .childHandler(new HeartbeatInitializer());
```

主从多线程：

```
private EventLoopGroup boss = new NioEventLoopGroup();
private EventLoopGroup work = new NioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap()
                .group(boss,work)
                .childHandler(new HeartbeatInitializer());
```

相信大家一看也明白。

## 总结

其实看过了 Netty 的线程模型之后能否对我们平时做高性能应用带来点启发呢？

我认为是可以的：

- 接口同步转异步处理。
- 回调通知结果。
- 多线程提高并发效率。

无非也就是这些，只是做了这些之后就会带来其他问题：

- 异步之后事务如何保证？
- 回调失败的情况？
- 多线程所带来的上下文切换、共享资源的问题。


# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 
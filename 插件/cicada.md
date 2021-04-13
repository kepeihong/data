
## 简介

基于 Netty4 实现的快速、轻量级 WEB 框架；没有过多的依赖，核心 jar 包仅 `30KB`。



## 特性

- [x] 代码简洁，没有过多依赖。
- [x] 一行代码即可启动 HTTP 服务。
- [x] 自定义拦截器。
- [x] 灵活的传参方式。
- [x] `json` 响应格式。
- [x] 自定义配置。
- [x] 多种响应方式。
- [ ] `Cookie` 支持。
- [ ] 文件上传。


## 快速启动

创建一个 maven 项目，引入核心依赖。

```
<dependency>
    <groupId>top.crossoverjie.opensource</groupId>
    <artifactId>cicada-core</artifactId>
    <version>1.0.3</version>
</dependency>
```

启动类：

```
public class MainStart {

    public static void main(String[] args) throws InterruptedException {
        CicadaServer.start(MainStart.class,"/cicada-example") ;
    }
}
```

### 配置业务 Action

创建业务 Action 实现 `top.crossoverjie.cicada.server.action.WorkAction` 接口。

```
@CicadaAction(value = "demoAction")
public class DemoAction implements WorkAction {


    private static final Logger LOGGER = LoggerBuilder.getLogger(DemoAction.class) ;

    private static AtomicLong index = new AtomicLong() ;

    @Override
    public void execute(CicadaContext context,Param paramMap) throws Exception {
        String name = paramMap.getString("name");
        Integer id = paramMap.getInteger("id");
        LOGGER.info("name=[{}],id=[{}]" , name,id);

        DemoResVO demoResVO = new DemoResVO() ;
        demoResVO.setIndex(index.incrementAndGet());
        WorkRes<DemoResVO> res = new WorkRes();
        res.setCode(StatusEnum.SUCCESS.getCode());
        res.setMessage(StatusEnum.SUCCESS.getMessage());
        res.setDataBody(demoResVO) ;
        context.json(res);
    }

}
```

启动应用访问 [http://127.0.0.1:7317/cicada-example/demoAction?name=12345&id=10](http://127.0.0.1:7317/cicada-example/demoAction?name=12345&id=10)

```json
{
    "code": "9000",
    "dataBody": {
        "index": 1
    },
    "message": "成功"
}
```

## Cicada 上下文

通过 `context.json(),context.text()` 方法可以选择不同的响应方式。

```
@CicadaAction("textAction")
public class TextAction implements WorkAction {
    @Override
    public void execute(CicadaContext context, Param param) throws Exception {
        String url = context.request().getUrl();
        String method = context.request().getMethod();
        context.text("hello world url=" + url + " method=" + method);
    }
}
```

![](https://ws1.sinaimg.cn/large/006tNbRwly1fvxvvo8yioj313i0tudij.jpg)

同时也可以根据 `context.request()` 获得请求上下文中的其他信息。

![](https://ws2.sinaimg.cn/large/006tNbRwly1fvxvxmpsjcj30yy0yo77h.jpg)

## 自定义配置

`cicada` 默认会读取 classpath 下的 `application.properties` 配置文件。

同时也可以自定义配置文件。

只需要继承 `top.crossoverjie.cicada.server.configuration.AbstractCicadaConfiguration`

并传入配置文件名称即可。比如：


```
public class RedisConfiguration extends AbstractCicadaConfiguration {


    public RedisConfiguration() {
        super.setPropertiesName("redis.properties");
    }

}

public class KafkaConfiguration extends AbstractCicadaConfiguration {

    public KafkaConfiguration() {
        super.setPropertiesName("kafka.properties");
    }


}
```

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv5mw7p5nvj31by0fo76t.jpg)

### 获取配置

按照如下方式即可获取自定义配置：

```
KafkaConfiguration configuration = (KafkaConfiguration) getConfiguration(KafkaConfiguration.class);
RedisConfiguration redisConfiguration = (RedisConfiguration) ConfigurationHolder.getConfiguration(RedisConfiguration.class);
ApplicationConfiguration applicationConfiguration = (ApplicationConfiguration) ConfigurationHolder.getConfiguration(ApplicationConfiguration.class);

String brokerList = configuration.get("kafka.broker.list");
String redisHost = redisConfiguration.get("redis.host");
String port = applicationConfiguration.get("cicada.port");

LOGGER.info("Configuration brokerList=[{}],redisHost=[{}] port=[{}]",brokerList,redisHost,port);
```

### 外置配置文件

当然在特殊环境中(`dev/test/pro`)也可以读取外置配置文件。只需要加上启动参数，保证参数名称和文件名一致即可。

```shell
-Dapplication.properties=/xx/application.properties
-Dkafka.properties=/xx/kakfa.properties
-Dredis.properties=/xx/redis.properties
```

## 自定义拦截器

实现 `top.crossoverjie.cicada.example.intercept.CicadaInterceptor` 接口。

```
@Interceptor(value = "executeTimeInterceptor")
public class ExecuteTimeInterceptor implements CicadaInterceptor {

    private static final Logger LOGGER = LoggerBuilder.getLogger(ExecuteTimeInterceptor.class);

    private Long start;

    private Long end;

    @Override
    public void before(Param param) {
        start = System.currentTimeMillis();
    }

    @Override
    public void after(Param param) {
        end = System.currentTimeMillis();

        LOGGER.info("cast [{}] times", end - start);
    }
}
```

### 拦截适配器

同样也可以只实现其中一个方法，只需要继承 `top.crossoverjie.cicada.server.intercept.AbstractCicadaInterceptorAdapter` 抽象类。

```
@Interceptor(value = "loggerInterceptor")
public class LoggerInterceptorAbstract extends AbstractCicadaInterceptorAdapter {

    private static final Logger LOGGER = LoggerBuilder.getLogger(LoggerInterceptorAbstract.class) ;

    @Override
    public void before(Param param) {
        LOGGER.info("logger param=[{}]",param.toString());
    }

}
```

## 性能测试

![](https://ws4.sinaimg.cn/large/006tNbRwly1fv4luap7w0j31kw0iwdnu.jpg)

> 测试条件：100 threads and 100 connections ;1G RAM/4 CPU。

**每秒将近 10W 请求。**

## 更新记录

### v1.0.3

- 修复 [#9](https://github.com/TogetherOS/cicada/issues/9)
- 修复 [#8](https://github.com/TogetherOS/cicada/issues/8),多种响应方式。
- 重构了核心代码，新增上下文环境。
- 优雅停机。

### v1.0.2

- 修复 [#6](https://github.com/TogetherOS/cicada/issues/6)
- 自定义配置文件。
- 灵活使用配置。
- 重构代码。


简单记录下，解决的一个问题，Cglib的invoke和invokeSuper的区别：

　　简而言之，invoke方法调用的对象没有增强过，invokeSuper方法调用的对象已经是增强了的，所以会再走一遍 MyMethodInterceptor的 interceptor方法，如果是个拦截器链条，就会重新在走一次拦截器链；

## 一.准备环境 Gglib的两个jar包，因为Cglib使用了ASM生成子类；



## 二.代码准备
```
public class Target {
    
    public void a() {
        System.out.println(" a 方法");
    }
    
    public void b() {
        System.out.println(" b 方法");
    }
}
 

import java.lang.reflect.Method;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
public class MyMethodInterceptor implements MethodInterceptor{

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
　　　　//obj是代理后的子类  ，method是调用方法 ，args是方法入参 ， proxy是MethodProxy代理对象
        System.out.println("myMethodInterceptor go ");
        Object res = proxy.invokeSuper(obj, args);
        return res;
    }
}
 

测试类：

public class TestApp {

    public static void main(String[] args) {
        Enhancer e = new Enhancer();
        e.setSuperclass(Target.class);     
        e.setCallback(new MyMethodInterceptor());
        Target t=(Target) e.create();
        t.a();
    }
}
 ```

测试结果：
```
myMethodInterceptor go 
 a 方法
 
```
 

## 三

3.1  先解决一个问题，Target这个类里面方法写 this 就是 指的生成的Cglib子类 ，

测试在a方法中添加一句输出this   ，结论：Cglib代理的时候target对象中的this就是Cglib子类  （你可能觉得我说的是废话，子类对象在父类的this指的不是自身吗？ 你知道Spring Aop里this方法无法增强自身调用，这时候你就开始怀疑人生了）



 

3.2 既然知道了this对象就是指代的自身，那我比如 this.b() 或者 b() 应该也被回调一次了 。
```
public class Target {
    
    public void a() {
        System.out.println(" a 方法");
        b();
    }
    
    public void b() {
        System.out.println(" b 方法");
    }
}
 ```

 

其他类不改动代码，测试结果如下：  果然 this.b()方法也被增强了；
```
myMethodInterceptor go 
 a 方法
myMethodInterceptor go 
 b 方法
 ```

你在 b()  打个断点，下一步就跳进入 MyMethodInterceptor 的 intercept 方法里了 ；这个似乎也没有毛病，其实原因就是 invokeSuper；invokeSuper传入的参数是Cglib代理的子类 ，就相当于 调用 Target$$EnhanceredByCGLIB这个子类的b()方法，肯定会再次进入回调；

 

3.3 如何实现像AOP一样 调用自身无法增强呢？

修改代码如下： 改动的地方已经标红了 ：）
```
public class Target {
    
    public void a() {
        System.out.println(" a 方法");
        b();
    }
    
    public void b() {
        System.out.println(" b 方法");
    }
}


public class MyMethodInterceptor implements MethodInterceptor{
    private Object target;
    
    public MyMethodInterceptor(Object target) {
        super();
        this.target = target;
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("myMethodInterceptor go ");
//      Object res = proxy.invokeSuper(obj, args);
        Object res = proxy.invoke(target, args);
        return res;
    }

}

public class TestApp {

    public static void main(String[] args) {
//        System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, "E:\\api");
        Target target = new Target();
        Enhancer e = new Enhancer();
        e.setSuperclass(Target.class);
        e.setCallback(new MyMethodInterceptor(target));
        Target t=(Target) e.create();
        t.a();
    }
}
 ```

 

测试结果如下：
```
myMethodInterceptor go 
 a 方法
 b 方法
 ```

这就和AOP的功能一毛一样了吧 ；  区别就在于 invoke 和 invokeSuper ： 在我理解看来，invoke方法调用的对象没有增强过，invokeSuper方法调用的对象已经是增强了的，所以会再走一遍 MyMethodInterceptor的 interceptor方法，如果是个拦截器链条，就会重新在走一次拦截器链；

 

##  四

查看下Spring CGLIB的Aop  ，  这个就是执行完 环绕通知 、 前置通知 之后执行业务方法的地方 ，target对象存的是原生的bean，没有被CGLIB代理的对象，所以就无法实现自身调用增强；

该方法是 AopUtils的invokeJoinpointUsingReflection 



 

而与之相反的则是，@Configuration注解下类中 @Bean注解标注方法里的 方法调用，得到的是同一个@Bean对象；

因为 BeanMethodInterceptor 的 interceptor方法  调用的invokeSuper方法 ，比如 getMan2方法调用getMan方法，那个getMan方法调用的是 CGLIB子类的getMan方法 ，此时getMan是增强后的getMan方法，这时候就会检测ThreadLocal当前线程和当前方法是否一致了，不一致尝试从容器中获取该bean对象                                      戳我查看原文



 

##  五。查看Cglib生成子类的方案思路

* 方案一

测试类上加上这样一句话：
```
public class TestApp {

    public static void main(String[] args) {
        System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, "E:\\api");
        Target target = new Target();
        Enhancer e = new Enhancer();
        e.setSuperclass(Target.class);
        e.setCallback(new MyMethodInterceptor(target));
        Target t=(Target) e.create();
        t.a();
    }
}
 
```
 看到控制台输出这样的：
```
CGLIB debugging enabled, writing to 'E:\api'
myMethodInterceptor go 
 a 方法
 b 方法
 可以看到确实生成了CGLIB子类class文件；
```
 

我的class文件通过JD-GUI查看却是有些问题  有的地方有很多label ，有知道的怎么解决的评论告诉我 多谢 ：）
```
 public final void a()
  {
    MethodInterceptor tmp4_1 = this.CGLIB$CALLBACK_0;
    if (tmp4_1 == null)
    {
      tmp4_1;
      CGLIB$BIND_CALLBACKS(this);
    }
    if (this.CGLIB$CALLBACK_0 != null)
      return;
    super.a();
  }
  ```
  * 方案二。提供一种思路，java  探针技术 还是 agent技术 
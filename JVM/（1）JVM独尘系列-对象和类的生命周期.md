# Java 对象生命周期
JVM主要就是围绕着对象的创建、存储、访问、回收进行一系列操作，而开发主要关心的就是对象的创建与回收，通常我们遇到的OOM等问题都是出现在对象的回收上。

想要了解JVM就要了解对象的整个生命周期以及生命周期中所做的事。

在JVM运行空间中，对象的整个生命周期大致可以分为7个阶段：

创建阶段（Creation）、应用阶段（Using）、不可视阶段（Invisible）、不可到达阶段（Unreachable）、可收集阶段（Collected）、终结阶段（Finalized）与释放阶段（Free）。

上面的这7个阶段，构成了 JVM中对象的完整的生命周期。下面分别介绍对象在处于这7个阶段时的不同情形。 

### 创建阶段


在对象创建阶段，系统要通过下面的步骤，完成对象的创建过程：

（1）为对象分配存储空间。

（2）开始构造对象。

（3）递归调用其超类的构造方法。

（4）进行对象实例初始化与变量初始化。

（5）执行构造方法体。

上面的5个步骤中的第3步就是指递归地调用该类所扩展的所有父类的构造方法，一个Java类（除Object类外）至少有一个父类（Object），这个规则既是强制的，也是隐式的。你可能已经注意到在创建一个Java类的时候，并没有显式地声明扩展（extends）一个Object父类。实际上，在 Java程序设计中，任何一个Java类都直接或间接的是Object类的子类。例如下面的代码：
```
public class A { 
     … 
} 
```
这个声明等同于下面的声明： 
```
public class A extends java.lang.Object { 
     … 
} 
 
```
上面讲解了对象处于创建阶段时，系统所做的一些处理工作，其中有些过程与应用的性能密切相关，因此在创建对象时，我们应该遵循一些基本的规则，以提高应用的性能。


下面是在创建对象时的几个关键应用规则：

（1）避免在循环体中创建对象，即使该对象占用内存空间不大。

（2）尽量及时使对象符合垃圾回收标准。

（3）不要采用过深的继承层次。

（4）访问本地变量优于访问类中的变量。

关于规则（1）避免在循环体中创建对象，即使该对象占用内存空间不大，需要提示一下，这种情况在我们的实际应用中经常遇到，而且我们很容易犯类似的错误，例如下面的代码：

 

```
for (int i = 0; i < 10000; ++i) { 
    Object obj = new Object(); 
    System.out.println("obj= "+ obj); 
} 
```
上面代码的书写方式相信对你来说不会陌生，也许在以前的应用开发中你也这样做过，尤其是在枚举一个Vector对象中的对象元素的操作中经常会这样书写，但这却违反了上述规则（1），因为这样会浪费较大的内存空间，正确的方法如下所示

```
Object obj = null; 
for (int i = 0; i < 10000; ++i) { 
    obj = new Object(); 
    System.out.println("obj= "+ obj); 
} 
```
采用上面的第二种编写方式，仅在内存中保存一份对该对象的引用，而不像上面的第一种编写方式中代码会在内存中产生大量的对象应用，浪费大量的内存空间，而且增大了系统做垃圾回收的负荷。因此在循环体中声明创建对象的编写方式应该尽量避免。

另外，不要对一个对象进行多次初始化，这同样会带来较大的内存开销，降低系统性能，如：
```
public class A { 
    private Hashtable table = new Hashtable (); 
    public A() { 
        // 将Hashtable对象table初始化了两次 
        table = new Hashtable(); 
    } 
} 
```
正确的方式为：
```
public class B { 
     private Hashtable table = new Hashtable (); 
     public B() { 
     } 
} 
```
不要小看这个差别，它却使应用软件的性能相差甚远，如图2-5所示。

 ![](https://img-blog.csdnimg.cn/img_convert/869e7f2e356c2df120d7986228a15a5f.png)




 

图2-5  初始化对象多次所带来的性能差别

看来在程序设计中也应该遵从“勿以恶小而为之”的古训，否则我们开发出来的应用也是低效的应用，有时应用软件中的一个极小的失误，就会大幅度地降低整个系统的性能。因此，我们在日常的应用开发中，应该认真对待每一行代码，采用最优化的编写方式，不要忽视细节，不要忽视潜在的问题。

 

### 应用阶段

当对象的创建阶段结束之后，该对象通常就会进入对象的应用阶段。这个阶段是对象得以表现自身能力的阶段。也就是说对象的应用阶段是对象整个生命周期中证明自身“存在价值”的时期。在对象的应用阶段，对象具备下列特征：

* 系统至少维护着对象的一个强引用（Strong Reference）；

* 所有对该对象的引用全部是强引用（除非我们显式地使用了：软引用（Soft Reference）、弱引用（Weak Reference）或虚引用（Phantom Reference））。

上面提到了几种不同的引用类型。可能一些读者对这几种引用的概念还不是很清楚，下面分别对之加以介绍。在讲解这几种不同类型的引用之前，我们必须先了解一下Java中对象引用的结构层次。

Java对象引用的结构层次示意如图2-6所示。

  ![](https://img-blog.csdnimg.cn/img_convert/f81c783286345bed7fcbb2103a2fb0f6.png)

图1  对象引用的结构层次示意

由图1我们不难看出，上面所提到的几种引用的层次关系，其中强引用处于顶端，而虚引用则处于底端。下面分别予以介绍。

#### 1．强引用

强引用（Strong Reference）是指JVM内存管理器从根引用集合（Root Set）出发遍寻堆中所有到达对象的路径。当到达某对象的任意路径都不含有引用对象时，对这个对象的引用就被称为强引用。

#### 2．软引用

软引用（Soft Reference）的主要特点是具有较强的引用功能。只有当内存不够的时候，才回收这类内存，因此在内存足够的时候，它们通常不被回收。另外，这些引用对象还能保证在Java抛出OutOfMemory 异常之前，被设置为null。它可以用于实现一些常用资源的缓存，实现Cache的功能，保证最大限度的使用内存而不引起OutOfMemory。再者，软可到达对象的所有软引用都要保证在虚拟机抛出OutOfMemoryError之前已经被清除。否则，清除软引用的时间或者清除不同对象的一组此类引用的顺序将不受任何约束。然而，虚拟机实现不鼓励清除最近访问或使用过的软引用。下面是软引用的实现代码：

 
```
import java.lang.ref.SoftReference; 
… 
A a = new A(); 
… 
// 使用 a 
… 
// 使用完了a，将它设置为soft 引用类型，并且释放强引用； 
SoftReference sr = new SoftReference(a); 
a = null; 
… 
// 下次使用时 
if (sr!=null) { 
     a = sr.get(); 
} 
else{ 
     // GC由于内存资源不足，可能系统已回收了a的软引用， 
     // 因此需要重新装载。 
     a = new A(); 
     sr=new SoftReference(a); 
} 
```
软引用技术的引进，使Java应用可以更好地管理内存，稳定系统，防止系统内存溢出，避免系统崩溃（crash）。因此在处理一些占用内存较大而且声明周期较长，但使用并不频繁的对象时应尽量应用该技术。正像上面的代码一样，我们可以在对象被回收之后重新创建（这里是指那些没有保留运行过程中状态的对象），提高应用对内存的使用效率，提高系统稳定性。但事物总是带有两面性的，有利亦有弊。在某些时候对软引用的使用会降低应用的运行效率与性能，例如：应用软引用的对象的初始化过程较为耗时，或者对象的状态在程序的运行过程中发生了变化，都会给重新创建对象与初始化对象带来不同程度的麻烦，有些时候我们要权衡利弊择时应用。

#### 3．弱引用

弱引用（Weak Reference）对象与Soft引用对象的最大不同就在于：GC在进行回收时，需要通过算法检查是否回收Soft引用对象，而对于Weak引用对象， GC总是进行回收。因此Weak引用对象会更容易、更快被GC回收。虽然，GC在运行时一定回收Weak引用对象，但是复杂关系的Weak对象群常常需要好几次GC的运行才能完成。Weak引用对象常常用于Map数据结构中，引用占用内存空间较大的对象，一旦该对象的强引用为null时，对这个对象引用就不存在了，GC能够快速地回收该对象空间。与软引用类似我们也可以给出相应的应用代码：
```
import java.lang.ref.WeakReference; 
… 
A a = new A(); 
… 
// 使用 a 
… 
// 使用完了a，将它设置为weak 引用类型，并且释放强引用； 
WeakReference wr = new WeakReference (a); 
a = null; 
… 
// 下次使用时 
if (wr!=null) { 
    a = wr.get(); 
} 
else{ 
    a = new A(); 
    wr = new WeakReference (a); 
} 
```
弱引用技术主要适用于实现无法防止其键（或值）被回收的规范化映射。另外，弱引用分为“短弱引用（Short Week Reference）”和“长弱引用（Long Week Reference）”，其区别是长弱引用在对象的Finalize方法被GC调用后依然追踪对象。基于安全考虑，不推荐使用长弱引用。因此建议使用下面的方式创建对象的弱引用。
```
WeakReference wr = new WeakReference(obj); 
```
或 
```
WeakReference wr = new WeakReference(obj, false); 
```
虚引用（Phantom Reference）的用途较少，主要用于辅助finalize函数的使用。Phantom对象指一些执行完了finalize函数，并且为不可达对象，但是还没有被GC回收的对象。这种对象可以辅助finalize进行一些后期的回收工作，我们通过覆盖Reference的clear()方法，增强资源回收机制的灵活性。虚引用主要适用于以某种比 java 终结机制更灵活的方式调度 pre-mortem 清除操作。

#### 4．虚引用

注意  在实际程序设计中一般很少使用弱引用与虚引用，使用软引用的情况较多，这是因为软引用可以加速JVM对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生。

不可视阶段

在一个对象经历了应用阶段之后，那么该对象便处于不可视阶段，说明我们在其他区域的代码中已经不可以再引用它，其强引用已经消失，例如，本地变量超出了其可视范围，如下所示。
```
public void process () { 
    try { 
         Object obj = new Object(); 
         obj.doSomething(); 
     } catch (Exception e) { 
     e.printStackTrace(); 
     } 
     while (isLoop) { // ... loops forever 
      // 这个区域对于obj对象来说已经是不可视的了 
         // 因此下面的代码在编译时会引发错误 
         obj.doSomething();  
     } 
} 
```
不可到达阶段如果一个对象已使用完，而且在其可视区域不再使用，此时应该主动将其设置为空（null）。可以在上面的代码行obj.doSomething();下添加代码行obj = null;，这样一行代码强制将obj对象置为空值。这样做的意义是，可以帮助JVM及时地发现这个垃圾对象，并且可以及时地回收该对象所占用的系统资源。

处于不可到达阶段的对象，在虚拟机所管理的对象引用根集合中再也找不到直接或间接的强引用，这些对象通常是指所有线程栈中的临时变量，所有已装载的类的静态变量或者对本地代码接口（JNI）的引用。这些对象都是要被垃圾回收器回收的预备对象，但此时该对象并不能被垃圾回收器直接回收。其实所有垃圾回收算法所面临的问题是相同的——找出由分配器分配的，但是用户程序不可到达的内存块。

#### 可收集阶段、终结阶段与释放阶段

对象生命周期的最后一个阶段是可收集阶段、终结阶段与释放阶段。当对象处于这个阶段的时候，可能处于下面三种情况：

（1）垃圾回收器发现该对象已经不可到达。

（2）finalize方法已经被执行。

（3）对象空间已被重用。

当对象处于上面的三种情况时，该对象就处于可收集阶段、终结阶段与释放阶段了。虚拟机就可以直接将该对象回收了。

 

## Java 类生命周期 
### 类的生命周期
java类的生命周期就是指一个class文件从加载到卸载的全过程。

类的完整生命周期包括7个部分：加载（双亲委派）、链接（验证：（文件格式验证、元数据验证、字节码验证、符号引用验证）、准备（分配内存，初始化变量）、解析）、初始化、使用、卸载。，如下图所示

![](https://img-blog.csdn.net/20180709000245847?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmxpZ3VveWlmYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  其中，验证——准备——解析  称为连接阶段，除了解析外，其他阶段是顺序发生的，而解析可以与这些阶段交叉进行，因为Java支持动态绑定（晚期绑定），需要运行时才能确定具体类型；在使用阶段实例化对象。

### 类的初始化触发
     类的加载机制没有明确的触发条件，但是有5种情况下必须对类进行初始化，那么 加载——验证——准备 就必须在此之前完成了。

   1：通过new关键字实例化对象、读取或设置类的静态变量、调用类的静态方法。

   2：通过反射方式执行以上三种行为。

   3：初始化子类的时候，会触发父类的初始化。

   4：虚拟机启动时，初始化一个执行主类；（作为程序入口直接运行时（也就是直接调用main方法）。）

   5、使用jdk1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、RE_invokeStatic的方法句柄，并且这个方法句柄对应的类没有进行初始化，则需要先触发其初始化。

   注意，有且只有五种情况必须对类进行初始化，这五种情况被称为“主动引用”，除了这五种情况，所有其他的类引用方式都不会触发类初始化，被称为“被动引用”。

请看主动引用的示例代码：

 
```
import java.lang.reflect.Field;  
import java.lang.reflect.Method;  
  
class InitClass{  
    static {  
        System.out.println("初始化InitClass");  
    }  
    public static String a = null;  
    public static void method(){}  
}  
  
class SubInitClass extends InitClass{}  
  
public class Test1 {  
  
    /** 
     * 主动引用引起类的初始化的第四种情况就是运行Test1的main方法时 
     * 导致Test1初始化，这一点很好理解，就不特别演示了。 
     * 本代码演示了前三种情况，以下代码都会引起InitClass的初始化， 
     * 但由于初始化只会进行一次，运行时请将注解去掉，依次运行查看结果。 
     * @param args 
     * @throws Exception 
     */  
    public static void main(String[] args) throws Exception{  
    //  主动引用引起类的初始化一: new对象、读取或设置类的静态变量、调用类的静态方法。  
    //  new InitClass();  
    //  InitClass.a = "";  
    //  String a = InitClass.a;  
    //  InitClass.method();  
          
    //  主动引用引起类的初始化二：通过反射实例化对象、读取或设置类的静态变量、调用类的静态方法。  
    //  Class cls = InitClass.class;  
    //  cls.newInstance();  
          
    //  Field f = cls.getDeclaredField("a");  
    //  f.get(null);  
    //  f.set(null, "s");  
      
    //  Method md = cls.getDeclaredMethod("method");  
    //  md.invoke(null, null);  
              
    //  主动引用引起类的初始化三：实例化子类，引起父类初始化。  
    //  new SubInitClass();  
  
    }  
}  
```

请看被动引用的示例代码：

### 类的加载过程
从用户角度来说，类（对象）的生命周期只需笼统理解为“加载——使用——卸载”即可，无需太过深入。所以，这里的类加载过程就是我们说的 加载——验证——准备——解析——初始化  这五个使用前的阶段。

1：加载

       加载阶段，虚拟机需要完成三件事：通过类名字获取类的二进制字节流——将字节流的内容转存到方法区——在内存中生成一个Class对象作为该类方法区数据的访问入口。

 其中，第一步：通过类名获取类的二进制字节流是通过类加载器来完成的。其加载过程使用“双亲委派模型”： 

类加载器的层次结构为：

https://img-blog.csdn.net/20180709063420606?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmxpZ3VveWlmYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

 

启动类加载器：加载系统环境变量下JAVA_HOME/lib目录下的类库。

       扩展类加载器：加载JAVA_HOME/lib/ext目录下的类库。

       应用程序类加载器（系统类加载器）：加载用户类路径Class_Path指定的类库。（我们可以在使用第三方插件时，把jar包添加到ClassPath后就是使用了这个加载器）

       自定义加载器：如果需要自定义加载时的规则（比如：指定类的字节流来源、动态加载时性能优化等），可以自己实现类加载器。

       双亲委派模型是指：当一个类加载器收到类加载请求时，不会直接加载这个类，而是把这个加载请求委派给自己父加载器去完成。如果父加载器无法加载时，子加载器才会去尝试加载。

       采用双亲委派模型的原因：避免同一个类被多个类加载器重复加载。

2：验证

当一个类被加载之后，必须要验证一下这个类是否合法，比如这个类是不是符合字节码的格式、变量与方法是不是有重复、数据类型是不是有效、继承与实现是否合乎标准等等。总之，这个阶段的目的就是保证加载的类是能够被jvm所运行。

3：准备

       为类变量（静态变量）在方法区分配内存，并设置零值。注意：这里是类变量，不是实例变量，实例变量是对象分配到堆内存时根据运行时动态生成的。

4：解析

       把常量池中的符号引用解析为直接引用：根据符号引用所作的描述，在内存中找到符合描述的目标并把目标指针指针返回。

 5：初始化

     类的初始化过程是这样的：按照顺序自上而下运行类中的变量赋值语句和静态语句，如果有父类，则首先按照顺序运行父类中的变量赋值语句和静态语句。先看一个例子，首先建两个类用来显示赋值操作：

下面是演示初始化顺序的代码：

 
```
public class Field1{  
    public Field1(){  
        System.out.println("Field1构造方法");  
    }  
}  
```
```
public class Field2{  
    public Field2(){  
        System.out.println("Field2构造方法");  
    }  
}  
```

下面的代码：

```
class InitClass2{  
    public static Field1 f1 = new Field1();  
    public static Field1 f2;  
    static{  
        System.out.println("运行父类静态代码");  
    }  
}  
  
class SubInitClass2 extends InitClass2{  
    public static Field2 f2 = new Field2();  
    static{  
        System.out.println("运行子类静态代码");  
    }  
}  
  
public class Test2 {  
    public static void main(String[] args) throws ClassNotFoundException{  
        new SubInitClass2();  
    }  
}  
```
 初始化顺序为：第02行、第05行、第10行、第12行，各位可以运行程序查看结果。

       在类的初始化阶段，只会初始化与类相关的静态赋值语句和静态语句，也就是有static关键字修饰的信息，而没有static修饰的赋值语句和执行语句在实例化对象的时候才会运行。

### 四使用

类的使用包括主动引用和被动引用，主动引用在初始化的章节中已经说过了，下面我们主要来说一下被动引用：

引用父类的静态字段，只会引起父类的初始化，而不会引起子类的初始化。
定义类数组，不会引起类的初始化。
引用类的常量，不会引起类的初始化
被动引用的示例代码：
```
class InitClass{  
    static {  
        System.out.println("初始化InitClass");  
    }  
    public static String a = null;  
    public final static String b = "b";  
    public static void method(){}  
}  
  
class SubInitClass extends InitClass{  
    static {  
        System.out.println("初始化SubInitClass");  
    }  
}  
  
public class Test4 {  
  
    public static void main(String[] args) throws Exception{  
    //  String a = SubInitClass.a;// 引用父类的静态字段，只会引起父类初始化，而不会引起子类的初始化  
    //  String b = InitClass.b;// 使用类的常量不会引起类的初始化  
        SubInitClass[] sc = new SubInitClass[10];// 定义类数组不会引起类的初始化  
    }  
} 
```
  最后总结一下使用阶段：使用阶段包括主动引用和被动引用，主动饮用会引起类的初始化，而被动引用不会引起类的初始化。

  当使用阶段完成之后，java类就进入了卸载阶段。

### 五卸载

在类使用完之后，如果满足下面的情况，类就会被卸载：

* 该类所有的实例都已经被回收，也就是java堆中不存在该类的任何实例。
* 加载该类的ClassLoader已经被回收。
* 该类对应的java.lang.Class对象没有任何地方被引用，无法在任何地方通过反射访问该类的方法。

如果以上三个条件全部满足，jvm就会在方法区垃圾回收的时候对类进行卸载，类的卸载过程其实就是在方法区中清空类信息，java类的整个生命周期就结束了。
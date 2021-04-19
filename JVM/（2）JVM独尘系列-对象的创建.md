# 对象的创建
       当一个对象被创建时，虚拟机就会为其分配内存来存放对象自己的实例变量及其从父类继承过来的实例变量(即使这些从超类继承过来的实例变量有可能被隐藏也会被分配空间)。在为这些实例变量分配内存的同时，这些实例变量也会被赋予默认值(零值)。在内存分配完成之后，Java虚拟机就会开始对新创建的对象按照程序猿的意志进行初始化。

在Java对象初始化过程中，主要涉及三种执行对象初始化的结构，分别是 实例变量初始化、实例代码块初始化 以及 构造函数初始化。

![](https://img-blog.csdnimg.cn/img_convert/c79cdc54db9a7c624f4323741e3f1e64.png)

## 创建对象的方式：

### 1). 使用new关键字创建对象
　　这是我们最常见的也是最简单的创建对象的方式，通过这种方式我们可以调用任意的构造函数（无参的和有参的）去创建对象。比如：
```
　　Student student = new Student();
```
### 2). 使用Class类的newInstance方法(反射机制)
　　我们也可以通过Java的反射机制使用Class类的newInstance方法来创建对象，事实上，这个newInstance方法调用无参的构造器创建对象，比如：
```
　　Student student2 = (Student)Class.forName("Student类全限定名").newInstance();　
或者：
　　Student stu = Student.class.newInstance();
```
### 3). 使用Constructor类的newInstance方法(反射机制)
　　java.lang.relect.Constructor类里也有一个newInstance方法可以创建对象，该方法和Class类中的newInstance方法很像，但是相比之下，Constructor类的newInstance方法更加强大些，我们可以通过这个newInstance方法调用有参数的和私有的构造函数，比如：
```
public class Student {
 
    private int id;
 
    public Student(Integer id) {
        this.id = id;
    }
 
    public static void main(String[] args) throws Exception {
 
        Constructor<Student> constructor = Student.class
                .getConstructor(Integer.class);
        Student stu3 = constructor.newInstance(123);
    }
}
```
　　使用newInstance方法的这两种方式创建对象使用的就是Java的反射机制，事实上Class的newInstance方法内部调用的也是Constructor的newInstance方法。

### 4). 使用Clone方法创建对象
　　无论何时我们调用一个对象的clone方法，JVM都会帮我们创建一个新的、一样的对象，特别需要说明的是，用clone方法创建对象的过程中并不会调用任何构造函数。关于如何使用clone方法以及浅克隆/深克隆机制，简单而言，要想使用clone方法，我们就必须先实现Cloneable接口并实现其定义的clone方法，这也是原型模式的应用。比如：
```
public class Student implements Cloneable{
 
    private int id;
 
    public Student(Integer id) {
        this.id = id;
    }
 
    @Override
    protected Object clone() throws CloneNotSupportedException {
        // TODO Auto-generated method stub
        return super.clone();
    }
 
    public static void main(String[] args) throws Exception {
 
        Constructor<Student> constructor = Student.class
                .getConstructor(Integer.class);
        Student stu3 = constructor.newInstance(123);
        Student stu4 = (Student) stu3.clone();
    }
}
```
### 5). 使用(反)序列化机制创建对象
　　当我们反序列化一个对象时，JVM会给我们创建一个单独的对象，在此过程中，JVM并不会调用任何构造函数。为了反序列化一个对象，我们需要让我们的类实现Serializable接口
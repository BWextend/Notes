# Java前期（静态）绑定和后期（动态）绑定 
[Java前期（静态）绑定和后期（动态）绑定](https://www.cnblogs.com/jstarseven/articles/4631586.html)

## 一、程序绑定的概念：
**绑定** 指的是一个方法的调用与方法所在的类(方法主体)关联起来。对Java来说，绑定分为静态绑定和动态绑定；或者叫做前期绑定和后期绑定.

## 二、静态绑定：
在程序执行前方法已经被绑定（也就是说在编译过程中就已经知道这个方法到底是哪个类中的方法），此时由编译器或其它连接程序实现。例如：C。
针对Java简单的可以理解为程序编译期的绑定；这里特别说明一点，**Java当中的方法只有final，static，private和构造方法是前期绑定**

## 三、动态绑定：
后期绑定：在运行时根据具体对象的类型进行绑定。
若一种语言实现了后期绑定，同时必须提供一些机制，可在运行期间判断对象的类型，并分别调用适当的方法。也就是说，编译器此时依然不知道对象的类型，但方法调用机制能自己去调查，找到正确的方法主体。不同的语言对后期绑定的实现方法是有所区别的。但我们至少可以这样认为：**它们都要在对象中安插某些特殊类型的信息。**

**动态绑定的过程：**
1.  虚拟机提取对象的实际类型的方法表；
2.  虚拟机搜索方法签名；
3.  调用方法。

### （一）关于final，static，private和构造方法是前期绑定的理解

- 对于private的方法，首先一点它不能被继承，既然不能被继承那么就没办法通过它子类的对象来调用，而只能通过这个类自身的对象来调用。因此就可以说private方法和定义这个方法的类绑定在了一起。

- final方法虽然可以被继承，但不能被重写（覆盖），虽然子类对象可以调用，但是调用的都是父类中所定义的那个final方法，（由此我们可以知道将方法声明为final类型，一是为了防止方法被覆盖，二是为了有效地关闭Java中的动态绑定)。

- 构造方法也是不能被继承的（网上也有说子类无条件地继承父类的无参数构造函数作为自己的构造函数，不过个人认为这个说法不太恰当，因为我们知道子类是通过super()来调用父类的无参构造方法，来完成对父类的初始化, 而我们使用从父类继承过来的方法是不用这样做的，因此不应该说子类继承了父类的构造方法），因此编译时也可以知道这个构造方法到底是属于哪个类。

- 对于static方法，具体的原理我也说不太清。不过根据网上的资料和我自己做的实验可以得出结论：static方法可以被子类继承，但是不能被子类重写（覆盖），但是可以被子类隐藏。（这里意思是说如果父类里有一个static方法，它的子类里如果没有对应的方法，那么当子类对象调用这个方法时就会使用父类中的方法。而如果子类中定义了相同的方法，则会调用子类的中定义的方法。唯一的不同就是，当子类对象上转型为父类对象时，不论子类中有没有定义这个静态方法，该对象都会使用父类中的静态方法。因此这里说静态方法可以被隐藏而不能被覆盖。这与子类隐藏父类中的成员变量是一样的。隐藏和覆盖的区别在于，子类对象转换成父类对象后，能够访问父类被隐藏的变量和方法，而不能访问父类被覆盖的方法）

由上面我们可以得出结论，**如果一个方法不可被继承或者继承后不可被覆盖，那么这个方法就采用的静态绑定**。

## 四、 Java的编译与运行

**Java的编译过程**是将Java源文件编译成字节码（jvm可执行代码，即.class文件）的过程，在这个过程中Java是不与内存打交道的，在这个过程中编译器会进行语法的分析，如果语法不正确就会报错。

**Java的运行过程**是指jvm（Java虚拟机）装载字节码文件并解释执行。在这个过程才是真正的创立内存布局，执行Java程序。

- Java字节码的执行有两种方式： 
    - （1）即时编译方式：解释器先将字节编译成机器码，然后再执行该机器码；
    - （2）解释执行方式：解释器通过每次解释并执行一小段代码来完成Java字节码程序的所有操作。（这里我们可以看出Java程序在执行过程中其实是进行了两次转换，先转成字节码再转换成机器码。这也正是Java能一次编译，到处运行的原因。在不同的平台上装上对应的Java虚拟机，就可以实现相同的字节码转换成不同平台上的机器码，从而在不同的平台上运行）


## 动态绑定具体过程细节如下：

前面已经说了对于Java当中的方法而言，除了final，static，private和构造方法是前期绑定外，其他的方法全部为动态绑定。

而动态绑定的典型发生在父类和子类的转换声明之下：
比如：Parent p = new Children();
**过程为：**
1：编译器检查对象的声明类型和方法名。
假设我们调用x.f(args)方法，并且x已经被声明为C类的对象，那么编译器会列举出C 类中所有的名称为f 的方法和从C 类的超类继承过来的f 方法。
2：接下来编译器检查方法调用中提供的参数类型。
如果在所有名称为f 的方法中有一个参数类型和调用提供的参数类型最为匹配，那么就调用这个方法，这个过程叫做“重载解析”。

3：当程序运行并且使用动态绑定调用方法时，虚拟机必须调用同x所指向的对象的实际类型相匹配的方法版本。

假设实际类型为D(C的子类)，如果D类定义了f(String)那么该方法被调用，否则就在D的超类中搜寻方法f(String),依次类推。

Java 虚拟机调用一个类方法时（静态方法），它会基于对象引用的类型(通常在编译时可知)来选择所调用的方法。相反，当虚拟机调用一个实例方法时，它会基于对象实际的类型(只能在运行时得知)来选择所调用的方法，这就是动态绑定，是多态的一种。动态绑定为解决实际的业务问题提供了很大的灵活性，是一种非常优美的机制。

与方法不同，在处理Java类中的成员变量（实例变量和类变量）时，并不是采用运行时绑定，而是一般意义上的静态绑定。所以在向上转型的情况下，对象的方法可以找到子类，而对象的属性（成员变量）还是父类的属性（子类对父类成员变量的隐藏）。
Java代码 
```java
 public class Father {  
    protected String name = "父亲属性";  
 }  

public class Son extends Father {  
  protected String name = "儿子属性";  

public static void main(String[] args) {  
    Father sample = new Son();  
    System.out.println("调用的属性：" + sample.name);  
  }  
}  

```


结论，调用的成员为父亲的属性。
这个结果表明，子类的对象(由父类的引用handle)调用到的是父类的成员变量。所以必须明确，运行时（动态）绑定针对的范畴只是对象的方法。
现在试图调用子类的成员变量name，该怎么做？最简单的办法是将该成员变量封装成方法getter形式。
代码如下：
Java代码 
```java
public class Father { 
  protected String name = "父亲属性"; 

  public String getName() { 
  return name; 
  } 
}　　 

public class Son extends Father { 
   protected String name = "儿子属性"; 

   public String getName() { 
     return name; 
 } 

 public static void main(String[] args) { 
 Father sample = new Son(); 

 System.out.println("调用的属性:" + sample.getName()); 

   } 
 }
```

结果：调用的是儿子的属性
Java因为什么对属性要采取静态的绑定方法。这是因为静态绑定是有很多的好处，它可以让我们在编译期就发现程序中的错误，而不是在运行期。这样就可以提高程序的运行效率！而对方法采取动态绑定是为了实现多态，多态是Java的一大特色。多态也是面向对象的关键技术之一，所以Java是以效率为代价来实现多态这是很值得的。
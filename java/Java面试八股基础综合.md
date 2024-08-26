# java面试八股基础综合

## jdk jre jvm

### jvm

英文名称（Java Virtual Machine），就是我们耳熟能详的 Java 虚拟机。Java 能够跨平台运行的核心在于 JVM 。

所有的java程序会首先被编译为.class的类文件，这种类文件可以在虚拟机上执行。也就是说class文件并不直接与机器的操作系统交互，而是经过虚拟机间接与操作系统交互，由虚拟机将程序解释给本地系统执行。

针对不同的系统有不同的 jvm 实现，有 Linux 版本的 jvm 实现，也有Windows 版本的 jvm 实现，但是同一段代码在编译后的字节码是一样的。这就是Java能够跨平台，实现一次编写，多处运行的原因所在。

### JRE

英文名称（Java Runtime Environment），就是Java 运行时环境。我们编写的Java程序必须要在JRE才能运行。它主要包含两个部分，JVM 和 Java 核心类库。

**JRE = JVM + Java 核心类库**

### JDK

英文名称（Java Development Kit），就是 Java 开发工具包

**JDK = JRE + Java工具 + 编译器 + 调试器**



## Java是编译执行还是解释执行

对于Java这种语言，它的**源代码**会先通过javac编译成**字节码**，再通过jvm将字节码转换成**机器码**执行，即解释运行 和编译运行配合使用，所以可以称为混合型或者半编译型。



## 面向对象和面向过程的区别？

面向过程就是分析出解决问题所需要的步骤，然后用函数按这些步骤实现，使用的时候依次调用就可以了。

面向对象是把构成问题事务分解成各个对象，分别设计这些对象，然后将他们组装成有完整功能的系统。面向过程只用函数实现，面向对象是用类实现各个功能模块。



## 面向对象有哪些特性？

面向对象四大特性：**封装，继承，多态，抽象**

1、封装就是将类的信息隐藏在类内部，不允许外部程序直接访问，而是通过该类的方法实现对隐藏信息的操作和访问。 良好的封装能够减少耦合。

2、继承是从已有的类中派生出新的类，新的类继承父类的属性和行为，并能扩展新的能力，大大增加程序的重用性和易维护性。在Java中是单继承的，也就是说一个子类只有一个父类。

3、多态是同一个行为具有多个不同表现形式的能力。在不修改程序代码的情况下改变程序运行时绑定的代码。实现多态的三要素：继承、重写、父类引用指向子类对象。

- 静态多态性：通过重载实现，相同的方法有不同的參数列表，可以根据参数的不同，做出不同的处理。
- 动态多态性：在子类中重写父类的方法。运行期间判断所引用对象的实际类型，根据其实际类型调用相应的方法。

4、抽象。把客观事物用代码抽象出来。



## 基本数据类型

| 简单类型   | boolean | byte | char      | short | int     | long | float | double |
| ---------- | ------- | ---- | --------- | ----- | ------- | ---- | ----- | ------ |
| 二进制位数 | 1       | 8    | 16        | 16    | 32      | 64   | 32    | 64     |
| 包装类     | Boolean | Byte | Character | Short | Integer | Long | Float | Double |

在Java规范中，没有明确指出boolean的大小。在《Java虚拟机规范》给出了单个boolean占4个字节，和boolean数组1个字节的定义，具体 **还要看虚拟机实现是否按照规范来**，因此boolean占用1个字节或者4个字节都是有可能的。



## 不可用浮点表示金额

由于计算机中保存的小数其实是十进制的小数的近似值，并不是准确值，所以，千万不要在代码中使用浮点数来表示金额等重要的指标。

建议使用BigDecimal或者Long来表示金额。



## 两个Integer 用== 比较不相等的原因

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b);

Integer c = 200;
Integer d = 200;
System.out.println(c == d);
```

输出

```java
true
false
```

为什么第二个输出是false？Integer类源码：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

`Integer c = 200;` 会调用 调⽤`Integer.valueOf(200)`。而从Integer的valueOf()源码可以看到，这里的实现并不是简单的new Integer，而是用IntegerCache做一个cache。

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;
    }
    ...
}
```

这是IntegerCache静态代码块中的一段，默认Integer cache 的下限是-128，上限默认127。当赋值100给Integer时，刚好在这个范围内，所以从cache中取对应的Integer并返回，所以a和b返回的是同一个对象，所以`==`比较是相等的，当赋值200给Integer时，不在cache 的范围内，所以会new Integer并返回，当然`==`比较的结果是不相等的。



## String 为什么不可变？

先看看什么是不可变的对象。

如果一个对象，在它创建完成之后，不能再改变它的状态，那么这个对象就是不可变的。不能改变状态的意思是，不能改变对象内的成员变量，包括基本数据类型的值不能改变，引用类型的变量不能指向其他的对象，引用类型指向的对象的状态也不能改变。

接着来看Java8 String类的源码：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
}
```

从源码可以看出，String对象其实在内部就是一个个字符，存储在这个value数组里面的。

value数组用final修饰，final 修饰的变量，值不能被修改。因此value不可以指向其他对象。

String类内部所有的字段都是私有的，也就是被private修饰。而且String没有对外提供修改内部状态的方法，因此value数组不能改变。

为什么String要设计为不可变的？

1. **线程安全**。同一个字符串实例可以被多个线程共享，因为字符串不可变，本身就是线程安全的。
2. **支持hash映射和缓存**。因为String的hash值经常会使用到，比如作为 Map 的键，不可变的特性使得 hash 值也不会变，不需要重新计算。
3. **安全考虑**。网络地址URL、文件路径path、密码通常情况下都是以String类型保存，假若String不是固定不变的，将会引起各种安全隐患。比如将密码用String的类型保存，那么它将一直留在内存中，直到垃圾收集器把它清除。假如String类不是固定不变的，那么这个密码可能会被改变，导致出现安全隐患。
4. **字符串常量池优化**。String对象创建之后，会缓存到字符串常量池中，下次需要创建同样的对象时，可以直接返回缓存的引用。



## 为何JDK9要将String的底层实现由char[]改成byte[]?

主要是为了**节约String占用的内存**。

在大部分Java程序的堆内存中，String占用的空间最大，并且绝大多数String只有Latin-1字符，这些Latin-1字符只需要1个字节就够了。

而在JDK9之前，JVM因为String使用char数组存储，每个char占2个字节，所以即使字符串只需要1字节，它也要按照2字节进行分配，浪费了一半的内存空间。

到了JDK9之后，对于每个字符串，会先判断它是不是只有Latin-1字符，如果是，就按照1字节的规格进行分配内存，如果不是，就按照2字节的规格进行分配，这样便提高了内存使用率，同时GC次数也会减少，提升效率。

不过Latin-1编码集支持的字符有限，比如不支持中文字符，因此对于中文字符串，用的是UTF16编码（两个字节），所以用byte[]和char[]实现没什么区别。



## String, StringBuffer 和 StringBuilder区别

**1. 可变性**

- String 不可变
- StringBuffer 和 StringBuilder 可变

**2. 线程安全**

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步

### StringBuilder线程不安全是指什么？

StringBuilder的多线程不安全是指**程序不能正常执行**，即**数组越界异常**，而不是数据错乱问题。

**结论：因为AbstractStringBuilder中的append方法中使用了全局变量`count`，多线程无法保证数组正常扩充，因此，出现数组越界的异常。**

StringBuilder继承AbstractStringBuilder，而StringBuilder中直接使用了父类的append方法。

```java
    @Override
    @IntrinsicCandidate
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
```

既然StringBuilder直接使用了AbstractStringBuilder的append方法，我们直接探究这个父类的方法，源码如下，使用的全局变量count作为数组扩容的参数：ensureCapacityInternal。

```java
    public AbstractStringBuilder append(String str) {
        if (str == null) {
            return appendNull();
        }
        int len = str.length();
        ensureCapacityInternal(count + len);
        putStringAt(count, str);
        count += len;
        return this;
    }
```

数组扩容系列操作源码如下，如果传入的值大于存储数据的字符数组长度，则拷贝数据到新的数组中，保证数据可以正常存储。
问题就出现在这里，多线程出现数组没有及时扩充，使用`putStringAt`时，导致数组越界。



## new String("dabin")会创建几个对象？

使用这种方式会创建两个字符串对象（前提是字符串常量池中没有 "dabin" 这个字符串对象）。

- "dabin" 属于字符串字面量，因此编译时期会在字符串常量池中创建一个字符串对象，指向这个 "dabin" 字符串字面量；
- 使用 new 的方式会在堆中创建一个字符串对象。



## 什么是字符串常量池？

字符串常量池（String Pool）保存着所有字符串字面量，这些字面量在编译时期就确定。字符串常量池位于<u>**`堆内存`**</u>中，专门用来存储字符串常量。在创建字符串时，JVM首先会检查字符串常量池，如果该字符串已经存在池中，则返回其引用，如果不存在，则创建此字符串并放入池中，并返回其引用。



## String最大长度是多少？

String类提供了一个length方法，返回值为int类型，而int的取值上限为2^31 -1。

所以理论上String的最大长度为2^31 -1。

### **达到这个长度的话需要多大的内存**

String内部是使用一个char数组来维护字符序列的，一个char占用两个字节。如果说String最大长度是2^31 -1的话，那么最大的字符串占用内存空间约等于4GB。

也就是说，我们需要有大于4GB的JVM运行内存才行。

###  **String一般都存储在JVM的哪块区域**

字符串在JVM中的存储分两种情况，一种是String对象，存储在JVM的堆栈中。一种是字符串常量，存储在常量池里面。

### **什么情况下字符串会存储在常量池**

当通过字面量进行字符串声明时，比如String s = "abcde";，这个字符串在编译之后会以常量的形式进入到常量池。



## equals与hashcode的关系：

1. 如果两个对象调用equals比较返回true，那么它们的hashCode值一定要相同；
2. 如果两个对象的hashCode相同，它们并不一定相同。

hashcode方法主要是用来**提升对象比较的效率**，先进行hashcode()的比较，如果不相同，那就不必在进行equals的比较，这样就大大减少了equals比较的次数，当比较对象的数量很大的时候能提升效率。



## 为什么重写 equals 时一定要重写 hashCode？

之所以重写`equals()`要重写`hashcode()`，是为了保证`equals()`方法返回true的情况下hashcode值也要一致，如果重写了`equals()`没有重写`hashcode()`，就会出现两个对象相等但`hashcode()`不相等的情况。这样，当用其中的一个对象作为键保存到hashMap、hashTable或hashSet中，再以另一个对象作为键值去查找他们的时候，则会查找不到。



## Java创建对象有几种方式？

Java创建对象有以下几种方式：

- 用new语句创建对象。
- 使用反射，使用Class.newInstance()创建对象。
- 调用对象的clone()方法。
- 运用反序列化手段，调用java.io.ObjectInputStream对象的readObject()方法。



## 类实例化的顺序

Java中类实例化顺序：

1. 静态属性，静态代码块。
2. 普通属性，普通代码块。
3. 构造方法。



## 常见的关键字

### static

static可以用来修饰类的成员方法、类的成员变量。

static变量也称作**静态变量**，静态变量和非静态变量的区别是：静态变量被所有的对象所共享，在内存中只有一个副本，它当且仅当在类初次加载时会被初始化。而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。

static方法一般称作**静态方法**。静态方法不依赖于任何对象就可以进行访问，通过类名即可调用静态方法。

**静态代码块**只会在类加载的时候执行一次。以下例子，startDate和endDate在类加载的时候进行赋值。

**静态内部类**

**在静态方法里**，使用⾮静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类。⽽静态内部类不需要。



### final

**基本数据**类型用final修饰，则不能修改，是常量；**对象引用**用final修饰，则引用只能指向该对象，不能指向别的对象，但是对象本身可以修改。

final修饰的方法不能被子类重写

final修饰的类不能被继承。



### this

`this.属性名称`指访问类中的成员变量，可以用来区分成员变量和局部变量。

`this.方法名称`用来访问本类的方法。



### super

super 关键字用于在子类中访问父类的变量和方法。



## final, finally, finalize 的区别

- final 用于修饰属性、方法和类, 分别表示属性不能被重新赋值，方法不可被覆盖，类不可被继承。
- finally 是异常处理语句结构的一部分，一般以`try-catch-finally`出现，`finally`代码块表示总是被执行。
- finalize 是Object类的一个方法，该方法一般由垃圾回收器来调用，当我们调用`System.gc()`方法的时候，由垃圾回收器调用`finalize()`方法，回收垃圾，JVM并不保证此方法总被调用。

​	

### finally一定会被执行吗？

答案是不一定。

有以下两种情况finally不会被执行：

- 程序未执行到try代码块
- 如果当一个线程在执行 try 语句块或者 catch 语句块时被打断（interrupted）或者被终止（killed），与其相对应的 finally 语句块可能不会执行。还有更极端的情况，就是在线程运行 try 语句块或者 catch 语句块时，突然死机或者断电，finally 语句块肯定不会执行了。

### final关键字的作用

- final 修饰的类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。



## 方法重载和重写的区别？

### 重载

**同个类中的多个方法可以有相同的方法名称，但是有不同的参数列表，这就称为方法重载**。

### 重写

**方法的重写描述的是父类和子类之间的。当父类的功能无法满足子类的需求，可以在子类对方法进行重写**。方法重写时， 方法名与形参列表必须一致。



## 接口与抽象类区别

### 抽象类

包含抽象方法的类

```java
abstract void fun();
```

实际上如果一个类不包含抽象方法，只是用 abstract 修饰的话也是抽象类。也就是说抽象类不一定必须含有抽象方法。但是这种类设计出来无意义。

1. 抽象方法必须为 public 或者 protected（因为如果为 private，则不能被子类继承，子类便无法实现该方法）
2. 抽象类不能用来创建对象；
3. 如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为 abstract 类。

### 接口

在软件工程中，接口泛指供 *别人调用的方法或者函数*。从这里，我们可以体会到 Java 语言设计者的初衷，它是 **对行为的抽象**。

### 区别

#### 1. 语法层面上的区别

- 1）抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
- 2）抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
- 3）接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
- 4）一个类只能继承一个抽象类，而一个类却可以实现多个接口。

#### 2. 设计层面上的区别

 1）抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。

> 举个简单的例子，飞机和鸟是不同类的事物，但是它们都有一个共性，就是都会飞。那么在设计的时候，可以将飞机设计为一个类 Airplane，将鸟设计为一个类 Bird，但是不能将 **飞行** 这个特性也设计为类，因此它只是一个行为特性，并不是对一类事物的抽象描述。此时可以将 飞行 设计为一个接口Fly，包含方法fly( )，然后Airplane和Bird分别根据自己的需要实现Fly这个接口。
>
> 然后至于有不同种类的飞机，比如战斗机、民用飞机等直接继承Airplane即可，对于鸟也是类似的，不同种类的鸟直接继承Bird类即可。
>
> 从这里可以看出，继承是一个 **"是不是" **的关系，而 接口 实现则是 **"有没有"** 的关系。如果一个类继承了某个抽象类，则子类必定是抽象类的种类，而接口实现则是有没有、具备不具备的关系，比如鸟是否能飞（或者是否具备飞行这个特点），能飞行则可以实现这个接口，不能飞行就不实现这个接口。

2）设计层面不同，抽象类作为很多子类的父类，它是一种模板式设计。而接口是一种行为规范，它是一种辐射式设计。

抽象类父类实现做了变动，子类super调用不需要变化即可直接提现父类的变化。

接口发生了变化，所有实现了该接口的类都需要进行相应的改动。



## Error和Exception的区别

**Error**：JVM 无法解决的严重问题，如栈溢出`StackOverflowError`、内存溢出`OOM`等。程序无法处理的错误。

**Exception**：其它因编程错误或偶然的外在因素导致的一般性问题。可以在代码中进行处理。如：空指针异常、数组下标越界等。



## 运行时异常和非运行时异常（checked）的区别？

### 受检异常（Checked Exception）

- 受检异常是在编译时强制处理的异常，程序必须在代码中显式地处理或者通过 `throws` 关键字声明方法可能抛出的受检异常。典型的受检异常包括 `IOException、SQLException` 等，它们表示程序在运行时可能遇到的外部因素导致的问题。

### 非受检异常（Unchecked Exception）

- 非受检异常是在运行时可能抛出的异常，也称为`运行时异常（Runtime Exception）`。它们通常是由程序逻辑错误引起的，无法在编译时预测。典型的非受检异常包括 `NullPointerException、ArrayIndexOutOfBoundsException` 等。


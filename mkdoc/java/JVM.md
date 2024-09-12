# JVM

JVM（Java Virtual Machine）Java 虚拟机。

## JDK、JRE 和 JVM

JDK 包含 JRE、JRE 包含 JVM。

### JDK

JDK（Java Development Kit）开发工具包，支持开发和运行 Java 程序。

JDK 包含 JRE 以及各种 Java 开发工具（如编译器 javac 、调试器 jdb 等）。

### JRE

JRE（Java Runtime Environment）能够运行已编译的 Java 程序。

JRE 包含 JVM 以及运行时所需调用的基础类库（如 java.lang 包、 java.util 包等）。

### JVM

JVM（Java Virtual Machine） 运行 Java 程序的工作环境。

Java 程序在虚拟机上运行而不是直接在操作系统上运行，从软件层面屏蔽了底层硬件指令的细节。虚拟机会根据操作系统自动将字节码文件转化成相应的机器码，使
Java 字节码文件能够在多种平台上不加修改地运行。

> HotSpot 虚拟机 是 SunJDK 和 OpenJDK 中所带的虚拟机，也是目前使用范围最广的 Java 虚拟机。

JVM 的主要组成部分包括：

- 类加载器（ClassLoader）：负责将类文件加载到内存中，并且生成对应的 Class 对象。
- 运行时数据区：包括方法区、堆、栈、程序计数器等，用于存储程序运行时所需的数据。
- 执行引擎：负责执行字节码指令，将字节码翻译成机器码并执行。
- 本地方法接口（Native Interface）：允许 Java 程序调用本地方法（例如 C/C++编写的方法）。
- 垃圾回收器（Garbage Collector）：负责管理堆内存中的对象，回收不再使用的对象以释放内存空间。

## JVM 运行流程

- 类加载器
  类加载器的作用是加载类文件到内存。
- 解释器
  解释器负责解释命令，提交到操作系统执行。
- 本地接口
  本地接口（Java Native Interface，JNI）提供了调用本地方法（如 C 或 C++等其他语言编写的方法）。
- 运行时数据区
  用 Java 编写的所有代码都被加载到这里，之后才开始运行。

## JVM 内存区域

内存区域也就是上面提到的运行时数据区。内存区域分为以下几个部分：

- 程序计数器
- 虚拟机栈
- 本地方法栈
- 堆
- 方法区
- 运行时常量池

### 程序计数器

程序计数器的作用是记录程序执行的位置。每个线程都有独立的程序计数器，JVM 在进行线程切换时会根据程序计数器恢复之前运行的状态。程序计数器的生命周期与线程相同。

### 虚拟机栈

‌ 虚拟机栈 ‌ 也是线程私有的，用于存储局部变量和方法调用的信息。每个方法执行时都会创建一个栈帧，当方法执行完毕，栈帧会被移除。Java 虚拟机栈是线程私有的，每个线程在创建时都会创建一个虚拟机栈，生命周期与线程相同。

### 本地方法栈

本地方法栈 ‌ 与虚拟机栈类似，但存储的是 native 方法的调用信息。本地方法栈也是线程私有的。

### 方法区

- 用途：存储每一个类的结构信息，例如运行时常量池、字段和方法数据、构造函数和普通方法的字节码内容。
- 特点：这是一个共享区域，对所有线程都是可见的。类信息在类加载时加载到方法区，类卸载时从方法区移除。
- 垃圾回收：垃圾回收很少发生在这个区域，但并不是不会发生。此区域的垃圾回收主要回收常量池中的常量以及类型的卸载。

方法区存储的是方法本身，虚拟机栈存在的是方法的调用信息。

### ‌ 堆

堆 ‌ 是 JVM 中最大的内存区域，用于存储所有线程共享的对象实例。堆可以被细分为新生代和老年代，进一步细分为 Eden 空间、From Survivor 空间、To Survivor 空间等。成员变量和通过 new 关键字创建的对象都在堆上分配内存。

堆是垃圾收集器（GC）管理的主要区域。

### 例子

```java
public class MemoryModel {
    // 类属性存在于方法区
    private static int classProperty = 0;
    // 实例属性存在于堆
    private int instanceProperty = 0;

    // 实例方法存在于方法区
    public void show(){
        // 局部变量，存在在虚拟机栈
        int value = 0;
        System.out.println(classProperty);
    }

    // 静态方法存在于方法区
    public static void main(String[] args) {
        // model对象存于堆，引用存于虚拟机栈
        MemoryModel model = new MemoryModel();
        // 调用方法涉及虚拟机栈
        model.show();
    }
}
```

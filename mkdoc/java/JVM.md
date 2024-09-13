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

虚拟机栈 也是线程私有的，用于存储局部变量和方法调用的信息。每个方法执行时都会创建一个栈帧，当方法执行完毕，栈帧会被移除。Java
虚拟机栈是线程私有的，每个线程在创建时都会创建一个虚拟机栈，生命周期与线程相同。

### 本地方法栈

本地方法栈 与虚拟机栈类似，但存储的是 native 方法的调用信息。本地方法栈也是线程私有的。

### 方法区

- 用途：存储每一个类的结构信息，例如运行时常量池、字段和方法数据、构造函数和普通方法的字节码内容。
- 特点：这是一个共享区域，对所有线程都是可见的。类信息在类加载时加载到方法区，类卸载时从方法区移除。
- 垃圾回收：垃圾回收很少发生在这个区域，但并不是不会发生。此区域的垃圾回收主要回收常量池中的常量以及类型的卸载。

方法区存储的是方法本身，虚拟机栈存在的是方法的调用信息。

### 堆

堆 是 JVM 中最大的内存区域，用于存储所有线程共享的对象实例。堆可以被细分为新生代和老年代，进一步细分为 Eden 空间、From
Survivor 空间、To Survivor 空间等。成员变量和通过 new 关键字创建的对象都在堆上分配内存。

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

## JVM 参数

在 Java 虚拟机的参数中，有 3 种表示方法

- 标准参数（-），所有的 JVM 实现都必须实现这些参数的功能，而且向后兼容；
- 非标准参数（-X），默认 jvm 实现这些参数的功能，但是并不保证所有 jvm 实现都满足，且不保证向后兼容；
- 非 Stable 参数（-XX），此类参数各个 jvm 实现会有所不同，将来可能会随时取消，需要慎重使用（但是，这些参数往往是非常有用的）；

### 标准参数（-）

使用`java -help`命令可以查看所有标准参数。

```bash
PS C:\Users\Administrator> java -help
用法: java [-options] class [args...]
           (执行类)
   或  java [-options] -jar jarfile [args...]
           (执行 jar 文件)
其中选项包括:
    -d32          使用 32 位数据模型 (如果可用)
    -d64          使用 64 位数据模型 (如果可用)
    -server       选择 "server" VM
                  默认 VM 是 server.

    -cp <目录和 zip/jar 文件的类搜索路径>
    -classpath <目录和 zip/jar 文件的类搜索路径>
                  用 ; 分隔的目录, JAR 档案
                  和 ZIP 档案列表, 用于搜索类文件。
    -D<名称>=<值>
                  设置系统属性
    -verbose:[class|gc|jni]
                  启用详细输出
    -version      输出产品版本并退出
    -version:<值>
                  警告: 此功能已过时, 将在
                  未来发行版中删除。
                  需要指定的版本才能运行
    -showversion  输出产品版本并继续
    -jre-restrict-search | -no-jre-restrict-search
                  警告: 此功能已过时, 将在
                  未来发行版中删除。
                  在版本搜索中包括/排除用户专用 JRE
    -? -help      输出此帮助消息
    -X            输出非标准选项的帮助
    -ea[:<packagename>...|:<classname>]
    -enableassertions[:<packagename>...|:<classname>]
                  按指定的粒度启用断言
    -da[:<packagename>...|:<classname>]
    -disableassertions[:<packagename>...|:<classname>]
                  禁用具有指定粒度的断言
    -esa | -enablesystemassertions
                  启用系统断言
    -dsa | -disablesystemassertions
                  禁用系统断言
    -agentlib:<libname>[=<选项>]
                  加载本机代理库 <libname>, 例如 -agentlib:hprof
                  另请参阅 -agentlib:jdwp=help 和 -agentlib:hprof=help
    -agentpath:<pathname>[=<选项>]
                  按完整路径名加载本机代理库
    -javaagent:<jarpath>[=<选项>]
                  加载 Java 编程语言代理, 请参阅 java.lang.instrument
    -splash:<imagepath>
                  使用指定的图像显示启动屏幕
有关详细信息, 请参阅 http://www.oracle.com/technetwork/java/javase/documentation/index.html。
```

### 非标准参数（-X）

非标准参数，是在标准参数的基础上进行扩展的参数，输入`java -X`命令，能够获得当前 JVM 支持的所有非标准参数列表。

```bash
PS C:\Users\Administrator> java -X
    -Xmixed           混合模式执行（默认）
    -Xint             仅解释模式执行
    -Xbootclasspath:<用 ; 分隔的目录和 zip/jar 文件>
                      设置引导类和资源的搜索路径
    -Xbootclasspath/a:<用 ; 分隔的目录和 zip/jar 文件>
                      附加在引导类路径末尾
    -Xbootclasspath/p:<用 ; 分隔的目录和 zip/jar 文件>
                      置于引导类路径之前
    -Xdiag            显示附加诊断消息
    -Xnoclassgc        禁用类垃圾收集
    -Xincgc           启用增量垃圾收集
    -Xloggc:<file>    将 GC 状态记录在文件中（带时间戳）
    -Xbatch           禁用后台编译
    -Xms<size>        设置初始 Java 堆大小
    -Xmx<size>        设置最大 Java 堆大小
    -Xss<size>        设置 Java 线程堆栈大小
    -Xprof            输出 cpu 分析数据
    -Xfuture          启用最严格的检查，预计会成为将来的默认值
    -Xrs              减少 Java/VM 对操作系统信号的使用（请参阅文档）
    -Xcheck:jni       对 JNI 函数执行其他检查
    -Xshare:off       不尝试使用共享类数据
    -Xshare:auto      在可能的情况下使用共享类数据（默认）
    -Xshare:on        要求使用共享类数据，否则将失败。
    -XshowSettings    显示所有设置并继续
    -XshowSettings:system
                      （仅限 Linux）显示系统或容器
                      配置并继续
    -XshowSettings:all
                      显示所有设置并继续
    -XshowSettings:vm 显示所有与 vm 相关的设置并继续
    -XshowSettings:properties
                      显示所有属性设置并继续
    -XshowSettings:locale
                      显示所有与区域设置相关的设置并继续

-X 选项是非标准选项。如有更改，恕不另行通知。
```

- -Xmn 新生代大小，使用方法：`-Xmn65535`、`-Xmn1024k`、`-Xmn512m`以及`-Xmn1g`（`-Xms -Xmx -Xss`也是这种写法）。

### 非 Stable 参数（-XX，非表态参数）

非静态参数，可以分为三类。

- 性能参数：用于 JVM 性能调优和内存分配控制，如初始化内存大小设置。
- 行为参数：用于改变 JVM 的基础行为，如 GC 的方式和算法的选择。
- 调试参数：用于监控、打印、输出等 JVM 参数，用于显示 JVM 更加详细的信息
  非静态参数，使用方法有四种：
- `-XX:+<option>` 启用选项。
- `-XX:-<option>` 启用选项。
- `-XX:<option>=<number><size>` 给选项设置一个数字类型值和单位，例如 1024k、512m、1g。
- `-XX:<option>=<string>` 给选项设置一个字符串值，例如`-XX:HeapDumpPath=./dump.core`。

#### 性能参数

- -XX:LargePageSizeInBytes=4m 设置用于 Java 堆的大页面尺寸。
- -XX:MaxHeapFreeRatio=70 GC 后 java 堆中空闲量占的最大比例。
- -XX:PermSize=64m 初始老年代内存区域大小。
- -XX:NewSize=24m 初始新生代大小。
- -XX:MaxNewSize=24m 新生成对象能占用内存的最大值。
- -XX:MaxPermSize=64m 老年代对象能占用内存的最大值。
- -XX:MinHeapFreeRatio=40 GC 后 java 堆中空闲量占的最小比例。
- -XX:NewRatio=2 新生代内存容量与老生代内存容量的比例。
- -XX:NewSize=2.125m 新生代对象生成时占用内存的默认值。
- -XX:ReservedCodeCacheSize=32m 保留代码占用的内存容量。
- -XX:ThreadStackSize=512 设置线程栈大小，若为 0 则使用系统默认值。
- -XX:+UseLargePages 使用大页面内存。

按照官方推荐新生代老年代:持久代=3:4:1

#### 行为参数

- -XX:-DisableExplicitGC 禁止调用 System.gc() ；但 jvm 的 gc 仍然有效。
- -XX:+MaxFDLimit 最大化文件描述符的数量限制。
- -XX:+ScavengeBeforeFullGC 新生代 GC 优先于 Full GC（全体回收模式） 执行。
- -XX:+UseGCOverheadLimit 在抛出 OOM 之前限制 jvm 耗费在 GC 上的时间比例。
- -XX:-UseConcMarkSweepGC 对老生代采用并发标记交换算法进行 GC。
- -XX:-UseParallelGC 启用并行 GC。
- -XX:-UseParallelOldGC 对 Full GC 启用并行，当-XX:-UseParallelGC 启用时该项自动启用。
- -XX:-UseSerialGC 启用串行 GC。
- -XX:+UseThreadPriorities 启用本地线程优先级。

#### 调试参数

- -XX:-CITime 打印消耗在 JIT 编译的时间。
- -XX:ErrorFile=./hs_err_pid<pid>.log 保存错误日志或者数据到文件中。
- -XX:-ExtendedDTraceProbes 开启 solaris 特有的 dtrace 探针。
- -XX:HeapDumpPath=./java_pid<pid>.hprof 指定导出堆信息时的路径或文件名。
- -XX:-HeapDumpOnOutOfMemoryError 当首次遭遇 OOM 时导出此时堆中相关信息。
- -XX:OnError="<cmd args>;<cmd args>" 出现致命 ERROR 之后运行自定义命令。
- -XX:OnOutOfMemoryError="<cmd args>;<cmd args>" 当首次遭遇 OOM 时执行自定义命令。
- -XX:-PrintClassHistogram 遇到 Ctrl-Break 后打印类实例的柱状信息，与 jmap -histo 功能相同。
- -XX:-PrintConcurrentLocks 遇到 Ctrl-Break 后打印并发锁的相关信息，与 jstack -l 功能相同。
- -XX:-PrintCommandLineFlags 打印在命令行中出现过的标记。
- -XX:-PrintCompilation 当一个方法被编译时打印相关信息。
- -XX:-PrintGC 每次 GC 时打印相关信息。
- -XX:-PrintGC Details 每次 GC 时打印详细信息。
- -XX:-PrintGCTimeStamps 打印每次 GC 的时间戳。
- -XX:-TraceClassLoading 跟踪类的加载信息。
- -XX:-TraceClassLoadingPreorder 跟踪被引用到的所有类的加载信息。
- -XX:-TraceClassResolution 跟踪常量池。
- -XX:-TraceClassUnloading 跟踪类的卸载信息。
- -XX:-TraceLoaderConstraints 跟踪类加载器约束的相关信息。

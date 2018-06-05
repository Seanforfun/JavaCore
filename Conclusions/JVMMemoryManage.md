# 自动内存管理机制

### 运行时数据区域
![JVM ram areas](https://i.imgur.com/5a3nvpz.jpg)

1. 程序计数器（Program counter register）
> 类似于Stack pointer。用于控制代码的运行行号，控制分支，循环，跳转，异常处理。
> 存储虚拟机字节码指令的地址。
> 每一条线程均有自己的PC。线程间的PC互不影响，相互独立。
> 当正在运行native方法时，PC值为空（Undifined）。

2. Java虚拟机栈（JVM Stack）
> 线程私有，生命周期和线程相同。可以理解为一种局部变量表。
> 在一个方法被执行的时候，会开辟一块栈空间（Stack Frame）,用于存储局部变量，操作栈，动态链接，方法出口信息。
> 开辟栈空间后会将局部变量等信息压栈。
> 除了double和long会占用2个slots（4 bytes）。别的占用1slot（32位）。例如对象的引用，存储了对象的指针。returnAddress存储了字节码的地址。由此可见，JVM是32位机。
> 局部变量表所需的内存空间在编译期就完成了分配。

3. 本地方法栈（Native method stack）
> 作用和JVM Stack非常类似。
> 为虚拟机使用到的Native方法服务。

4. Java堆（Java Heap）
> 所有线程共享堆。
> 存放对象实例。所有的对象和数组均在对上分配空间。
> Garbage collection的重要区域。
> 物理上的不连续区域，但是在逻辑上是连续的区域。

5. 方法区（Method Area）
> 线程的共享区域。
> 存储类信息，常量，静态变量即时编译器编译后的代码。

5.1. 运行时常量池(Runtime constant pool)
> 运行时常量池是方法区的一部分。
> Class文件中有一项信息是常量池（Constant Pool Table）,用于存放编译期生成的字面量和符号引用。这部分将在类加载后存放到方法区的运行时常量池中。

6. 直接内存（Direct Memory）
> 为了避免在Java堆和Native堆中来回复制数据而使用的堆外内存。
> 在NIO中被使用。
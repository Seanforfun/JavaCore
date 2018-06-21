# Concurrent

### Java Memory Model and Thread
> TPS(Transaction per second)是衡量服务性能好坏的一个重要指标。代表着一秒内服务端平均能响应的请求总数。

#### 硬件效率一致性
![processer, cache and memory](https://i.imgur.com/jaNupIN.png)

#### Java内存模型(Java Memory Model)
1. JMM定义了程序中每个变量的访问规则。此处变量指的是实例字段，静态字段和构成数组对象的元素，这些变量都不是线程私有的。
![JMM](https://i.imgur.com/254ieV7.png)
	* Main Memory 主内存
		* 这是虚拟机内存的一部分。
		* 所有的变量都存储在主内存中。
	* Working Memory 工作内存
		* 保存所有当前线程需要用到的变量在主内存中的副本拷贝。
		* 线程对变量的所有操作都必须在工作内存中完成，不能直接读写主内存的变量。

#### 内存间的交互操作
* lock：作用于主内存的变量，它把一个变量标识为一条线成独占的状态。
* unlock：作用于主内存的变量，把一个出于锁定状态的变量释放出来，释放后的变量才能被别的线程锁定。
* read：作用于主内存的变量，将主内存中的变量传输到线程的工作内存中，以便随后load使用。
* load：作用于工作内存的变量，将read从主内存中得到的变量放入工作内存的变量副本中。
* use:作用于工作内存的变量，它把工作内存中的一个变量的值传递给执行操作，每当虚拟机需要用到变量的值的时候会执行这个操作。
* assign：作用于工作内存的变量。当JVM需要对某个变量进行赋值时，将调用该指令对工作内存中的变量进行赋值。
* store： 将工作内存的一个变量的值传到主内存，为了write做准备。
* write：作用于主内存的变量，将store得到的变量的值写入。